---
title: Interfaccia utente riutilizzabile Razor nelle librerie di classi con ASP.NET Core
author: Rick-Anderson
description: Viene illustrato come creare Razor un'interfaccia utente riutilizzabile usando visualizzazioni parziali in una libreria di classi in ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
no-loc:
- appsettings.json
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
uid: razor-pages/ui-class
ms.openlocfilehash: 0bfdb1932d829ec00c9de1bd38b7920cb1f40c51
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "94570172"
---
# <a name="create-reusable-ui-using-the-no-locrazor-class-library-project-in-aspnet-core"></a><span data-ttu-id="e8898-103">Creare un'interfaccia utente riutilizzabile usando il Razor progetto libreria di classi in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e8898-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="e8898-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e8898-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e8898-105">Razorle visualizzazioni, le pagine, i controller, i modelli di pagina, i [ Razor componenti](xref:blazor/components/class-libraries), i [componenti di visualizzazione](xref:mvc/views/view-components)e i modelli di dati possono essere incorporati in una Razor libreria di classi (RCL).</span><span class="sxs-lookup"><span data-stu-id="e8898-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="e8898-106">La libreria di classi Razor può essere inclusa nel pacchetto e usata nuovamente.</span><span class="sxs-lookup"><span data-stu-id="e8898-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="e8898-107">Le applicazioni possono includere la libreria di classi Razor ed eseguire l'override delle visualizzazioni e pagine in essa contenute.</span><span class="sxs-lookup"><span data-stu-id="e8898-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="e8898-108">Quando si trova una visualizzazione, una visualizzazione parziale o una Razor pagina nell'app Web e nel RCL, il Razor markup (file con *estensione cshtml* ) nell'app Web ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="e8898-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="e8898-109">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e8898-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="e8898-110">Creare una libreria di classi contenente l' Razor interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="e8898-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e8898-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e8898-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e8898-112">In Visual Studio selezionare **Crea nuovo nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="e8898-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="e8898-113">Selezionare **Razor libreria di classi** > **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="e8898-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="e8898-114">Assegnare un nome alla libreria (ad esempio, " Razor CLASSLIB") > **creare**.</span><span class="sxs-lookup"><span data-stu-id="e8898-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="e8898-115">Per evitare un conflitto di nomi di file con la libreria di visualizzazione generata, verificare che il nome della libreria non finisca per `.Views`.</span><span class="sxs-lookup"><span data-stu-id="e8898-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="e8898-116">Se è necessario supportare le visualizzazioni, selezionare **pagine e visualizzazioni di supporto** .</span><span class="sxs-lookup"><span data-stu-id="e8898-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="e8898-117">Per impostazione predefinita, Razor sono supportate solo le pagine.</span><span class="sxs-lookup"><span data-stu-id="e8898-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="e8898-118">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="e8898-118">Select **Create**.</span></span>

<span data-ttu-id="e8898-119">Per impostazione predefinita, il Razor modello libreria di classi (RCL) prevede Razor lo sviluppo di componenti.</span><span class="sxs-lookup"><span data-stu-id="e8898-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="e8898-120">L'opzione **pagine e visualizzazioni di supporto** supporta pagine e viste.</span><span class="sxs-lookup"><span data-stu-id="e8898-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e8898-121">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="e8898-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e8898-122">Eseguire `dotnet new razorclasslib` dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="e8898-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="e8898-123">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e8898-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="e8898-124">Per impostazione predefinita, il Razor modello libreria di classi (RCL) prevede Razor lo sviluppo di componenti.</span><span class="sxs-lookup"><span data-stu-id="e8898-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="e8898-125">Passare l' `--support-pages-and-views` opzione ( `dotnet new razorclasslib --support-pages-and-views` ) per fornire il supporto per le pagine e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="e8898-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="e8898-126">Per altre informazioni, vedere [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="e8898-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="e8898-127">Per evitare un conflitto di nomi di file con la libreria di visualizzazione generata, verificare che il nome della libreria non finisca per `.Views`.</span><span class="sxs-lookup"><span data-stu-id="e8898-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="e8898-128">Aggiungere Razor i file a RCL.</span><span class="sxs-lookup"><span data-stu-id="e8898-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="e8898-129">I modelli di ASP.NET Core presuppongono che il contenuto RCL si trovi nella cartella *aree* .</span><span class="sxs-lookup"><span data-stu-id="e8898-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="e8898-130">Vedere [layout di pagine RCL](#rcl-pages-layout) per creare un RCL che espone il contenuto in `~/Pages` anziché `~/Areas/Pages` .</span><span class="sxs-lookup"><span data-stu-id="e8898-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="e8898-131">Contenuto RCL di riferimento</span><span class="sxs-lookup"><span data-stu-id="e8898-131">Reference RCL content</span></span>

<span data-ttu-id="e8898-132">Il riferimento alla libreria di classi Razor può essere eseguito da:</span><span class="sxs-lookup"><span data-stu-id="e8898-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="e8898-133">Pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="e8898-133">NuGet package.</span></span> <span data-ttu-id="e8898-134">Vedere [Creazione di pacchetti NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Creare e pubblicare un pacchetto NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="e8898-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="e8898-135">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="e8898-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="e8898-136">Vedere [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="e8898-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="e8898-137">Eseguire l'override di visualizzazioni, visualizzazioni parziali e pagine</span><span class="sxs-lookup"><span data-stu-id="e8898-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="e8898-138">Quando si trova una visualizzazione, una visualizzazione parziale o una Razor pagina nell'app Web e nel RCL, il Razor markup (file con *estensione cshtml* ) nell'app Web ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="e8898-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="e8898-139">Ad esempio, aggiungere *app Web 1/areas/la funzionalità/Pages/Pagina1. cshtml* a app Web 1 e Pagina1 in app Web 1 avrà la precedenza su PAGINA1 in RCL.</span><span class="sxs-lookup"><span data-stu-id="e8898-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="e8898-140">Nel download di esempio, rinominare *WebApp1/aree/MyFeature2* in *WebApp1/aree/MyFeature* per testare la precedenza.</span><span class="sxs-lookup"><span data-stu-id="e8898-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="e8898-141">Copiare la visualizzazione parziale *Razor UIClassLib/areas/la funzionalità/Pages/Shared/_Message. cshtml* in *app Web 1/areas/my feature/pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e8898-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="e8898-142">Aggiornare il markup per indicare la nuova posizione.</span><span class="sxs-lookup"><span data-stu-id="e8898-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="e8898-143">Compilare ed eseguire l'app per verificare quale versione del parziale sia in uso.</span><span class="sxs-lookup"><span data-stu-id="e8898-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="e8898-144">Layout di pagine RCL</span><span class="sxs-lookup"><span data-stu-id="e8898-144">RCL Pages layout</span></span>

<span data-ttu-id="e8898-145">Per fare riferimento al contenuto RCL come se facesse parte della cartella *pagine* dell'app Web, creare il progetto RCL con la struttura di file seguente:</span><span class="sxs-lookup"><span data-stu-id="e8898-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="e8898-146">*RazorUIClassLib/pagine*</span><span class="sxs-lookup"><span data-stu-id="e8898-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="e8898-147">*RazorUIClassLib/pagine/condiviso*</span><span class="sxs-lookup"><span data-stu-id="e8898-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="e8898-148">Si supponga che *Razor UIClassLib/Pages/Shared* contenga due file parziali: *_Header. cshtml* e *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e8898-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="e8898-149">`<partial>`È possibile aggiungere i tag al file *_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="e8898-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="e8898-150">Creare un RCL con asset statici</span><span class="sxs-lookup"><span data-stu-id="e8898-150">Create an RCL with static assets</span></span>

<span data-ttu-id="e8898-151">Un RCL può richiedere risorse statiche complementari a cui è possibile fare riferimento tramite il RCL o l'app consumer di RCL.</span><span class="sxs-lookup"><span data-stu-id="e8898-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="e8898-152">ASP.NET Core consente di creare RCL che includono risorse statiche disponibili per un'app di consumo.</span><span class="sxs-lookup"><span data-stu-id="e8898-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="e8898-153">Per includere asset complementari come parte di un RCL, creare una cartella *wwwroot* nella libreria di classi e includere tutti i file necessari in tale cartella.</span><span class="sxs-lookup"><span data-stu-id="e8898-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="e8898-154">Quando si esegue la compressione di un RCL, tutti gli asset complementari nella cartella *wwwroot* vengono inclusi automaticamente nel pacchetto.</span><span class="sxs-lookup"><span data-stu-id="e8898-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

<span data-ttu-id="e8898-155">Usare il `dotnet pack` comando anziché la versione NuGet.exe `nuget pack` .</span><span class="sxs-lookup"><span data-stu-id="e8898-155">Use the `dotnet pack` command rather than the NuGet.exe version `nuget pack`.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="e8898-156">Escludi asset statici</span><span class="sxs-lookup"><span data-stu-id="e8898-156">Exclude static assets</span></span>

<span data-ttu-id="e8898-157">Per escludere asset statici, aggiungere il percorso di esclusione desiderato al `$(DefaultItemExcludes)` gruppo di proprietà nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="e8898-157">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="e8898-158">Separare le voci con un punto e virgola ( `;` ).</span><span class="sxs-lookup"><span data-stu-id="e8898-158">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="e8898-159">Nell'esempio seguente, il foglio di stile *lib. CSS* nella cartella *wwwroot* non è considerato un asset statico e non è incluso nel RCL pubblicato:</span><span class="sxs-lookup"><span data-stu-id="e8898-159">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="e8898-160">Integrazione typescript</span><span class="sxs-lookup"><span data-stu-id="e8898-160">Typescript integration</span></span>

<span data-ttu-id="e8898-161">Per includere i file TypeScript in un RCL:</span><span class="sxs-lookup"><span data-stu-id="e8898-161">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="e8898-162">Inserire i file TypeScript (*. TS*) all'esterno della cartella *wwwroot*</span><span class="sxs-lookup"><span data-stu-id="e8898-162">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="e8898-163">Inserire, ad esempio, i file in una cartella *client* .</span><span class="sxs-lookup"><span data-stu-id="e8898-163">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="e8898-164">Configurare l'output di compilazione TypeScript per la cartella *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="e8898-164">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="e8898-165">Impostare la `TypescriptOutDir` proprietà all'interno di un oggetto `PropertyGroup` nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="e8898-165">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="e8898-166">Includere la destinazione TypeScript come dipendenza della destinazione aggiungendo `ResolveCurrentProjectStaticWebAssets` la destinazione seguente all'interno di un oggetto `PropertyGroup` nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="e8898-166">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="e8898-167">Utilizzare il contenuto da un RCL a cui si fa riferimento</span><span class="sxs-lookup"><span data-stu-id="e8898-167">Consume content from a referenced RCL</span></span>

<span data-ttu-id="e8898-168">I file inclusi nella cartella *wwwroot* di RCL vengono esposti a RCL o all'app consumer sotto il prefisso `_content/{LIBRARY NAME}/` .</span><span class="sxs-lookup"><span data-stu-id="e8898-168">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="e8898-169">Ad esempio, una libreria denominata *Razor . Class. lib* restituisce un percorso al contenuto statico in `_content/Razor.Class.Lib/` .</span><span class="sxs-lookup"><span data-stu-id="e8898-169">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="e8898-170">Quando si produce un pacchetto NuGet e il nome dell'assembly non è uguale all'ID del pacchetto, usare l'ID del pacchetto per `{LIBRARY NAME}` .</span><span class="sxs-lookup"><span data-stu-id="e8898-170">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="e8898-171">L'app consumer fa riferimento a risorse statiche fornite dalla libreria con `<script>` ,, `<style>` `<img>` e altri tag HTML.</span><span class="sxs-lookup"><span data-stu-id="e8898-171">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="e8898-172">Per l'app consumer deve essere abilitato il [supporto file statico](xref:fundamentals/static-files) in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="e8898-172">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="e8898-173">Quando si esegue l'app consumer dall'output di compilazione ( `dotnet run` ), le risorse Web statiche sono abilitate per impostazione predefinita nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="e8898-173">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="e8898-174">Per supportare asset in altri ambienti durante l'esecuzione dall'output di compilazione, chiamare `UseStaticWebAssets` sul generatore host in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e8898-174">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="e8898-175">`UseStaticWebAssets`Quando si esegue un'app dall'output pubblicato (), la chiamata non è obbligatoria `dotnet publish` .</span><span class="sxs-lookup"><span data-stu-id="e8898-175">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="e8898-176">Flusso di sviluppo per più progetti</span><span class="sxs-lookup"><span data-stu-id="e8898-176">Multi-project development flow</span></span>

<span data-ttu-id="e8898-177">Quando viene eseguita l'app consumer:</span><span class="sxs-lookup"><span data-stu-id="e8898-177">When the consuming app runs:</span></span>

* <span data-ttu-id="e8898-178">Gli asset nel RCL rimanere nelle cartelle originali.</span><span class="sxs-lookup"><span data-stu-id="e8898-178">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="e8898-179">Gli asset non vengono spostati nell'app consumer.</span><span class="sxs-lookup"><span data-stu-id="e8898-179">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="e8898-180">Tutte le modifiche apportate all'interno della cartella *wwwroot* di RCL vengono riflesse nell'app consumer dopo che il RCL viene ricompilato e senza ricompilare l'app consumer.</span><span class="sxs-lookup"><span data-stu-id="e8898-180">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="e8898-181">Quando viene compilato il RCL, viene generato un manifesto che descrive i percorsi di asset Web statici.</span><span class="sxs-lookup"><span data-stu-id="e8898-181">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="e8898-182">L'app consumer legge il manifesto in fase di esecuzione per usare gli asset dei progetti e dei pacchetti a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="e8898-182">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="e8898-183">Quando si aggiunge un nuovo asset a un RCL, è necessario ricompilare il RCL per aggiornare il manifesto prima che un'app che utilizza possa accedere al nuovo asset.</span><span class="sxs-lookup"><span data-stu-id="e8898-183">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="e8898-184">Pubblica</span><span class="sxs-lookup"><span data-stu-id="e8898-184">Publish</span></span>

<span data-ttu-id="e8898-185">Quando viene pubblicata l'app, gli asset complementari di tutti i progetti e i pacchetti a cui viene fatto riferimento vengono copiati nella cartella *wwwroot* dell'app pubblicata in `_content/{LIBRARY NAME}/` .</span><span class="sxs-lookup"><span data-stu-id="e8898-185">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e8898-186">Razorle visualizzazioni, le pagine, i controller, i modelli di pagina, i [ Razor componenti](xref:blazor/components/class-libraries), i [componenti di visualizzazione](xref:mvc/views/view-components)e i modelli di dati possono essere incorporati in una Razor libreria di classi (RCL).</span><span class="sxs-lookup"><span data-stu-id="e8898-186">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="e8898-187">La libreria di classi Razor può essere inclusa nel pacchetto e usata nuovamente.</span><span class="sxs-lookup"><span data-stu-id="e8898-187">The RCL can be packaged and reused.</span></span> <span data-ttu-id="e8898-188">Le applicazioni possono includere la libreria di classi Razor ed eseguire l'override delle visualizzazioni e pagine in essa contenute.</span><span class="sxs-lookup"><span data-stu-id="e8898-188">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="e8898-189">Quando si trova una visualizzazione, una visualizzazione parziale o una Razor pagina nell'app Web e nel RCL, il Razor markup (file con *estensione cshtml* ) nell'app Web ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="e8898-189">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="e8898-190">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e8898-190">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="e8898-191">Creare una libreria di classi contenente l' Razor interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="e8898-191">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e8898-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e8898-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e8898-193">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="e8898-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e8898-194">Selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="e8898-194">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="e8898-195">Assegnare un nome alla libreria (ad esempio, " Razor CLASSLIB") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="e8898-195">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="e8898-196">Per evitare un conflitto di nomi di file con la libreria di visualizzazione generata, verificare che il nome della libreria non finisca per `.Views`.</span><span class="sxs-lookup"><span data-stu-id="e8898-196">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="e8898-197">Verificare che sia selezionato **ASP.NET Core 2.1** o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="e8898-197">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="e8898-198">Selezionare **Razor libreria di classi** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="e8898-198">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="e8898-199">Un RCL ha il seguente file di progetto:</span><span class="sxs-lookup"><span data-stu-id="e8898-199">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="e8898-200">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="e8898-200">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e8898-201">Eseguire `dotnet new razorclasslib` dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="e8898-201">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="e8898-202">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e8898-202">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="e8898-203">Per altre informazioni, vedere [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="e8898-203">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="e8898-204">Per evitare un conflitto di nomi di file con la libreria di visualizzazione generata, verificare che il nome della libreria non finisca per `.Views`.</span><span class="sxs-lookup"><span data-stu-id="e8898-204">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="e8898-205">Aggiungere Razor i file a RCL.</span><span class="sxs-lookup"><span data-stu-id="e8898-205">Add Razor files to the RCL.</span></span>

<span data-ttu-id="e8898-206">I modelli di ASP.NET Core presuppongono che il contenuto RCL si trovi nella cartella *aree* .</span><span class="sxs-lookup"><span data-stu-id="e8898-206">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="e8898-207">Vedere [layout di pagine RCL](#rcl-pages-layout) per creare un RCL che espone il contenuto in `~/Pages` anziché `~/Areas/Pages` .</span><span class="sxs-lookup"><span data-stu-id="e8898-207">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="e8898-208">Contenuto RCL di riferimento</span><span class="sxs-lookup"><span data-stu-id="e8898-208">Reference RCL content</span></span>

<span data-ttu-id="e8898-209">Il riferimento alla libreria di classi Razor può essere eseguito da:</span><span class="sxs-lookup"><span data-stu-id="e8898-209">The RCL can be referenced by:</span></span>

* <span data-ttu-id="e8898-210">Pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="e8898-210">NuGet package.</span></span> <span data-ttu-id="e8898-211">Vedere [Creazione di pacchetti NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Creare e pubblicare un pacchetto NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="e8898-211">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="e8898-212">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="e8898-212">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="e8898-213">Vedere [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="e8898-213">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-no-locrazor-pages-project"></a><span data-ttu-id="e8898-214">Procedura dettagliata: creare un progetto RCL e usarlo da un Razor progetto Pages</span><span class="sxs-lookup"><span data-stu-id="e8898-214">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="e8898-215">È possibile scaricare il [progetto completo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) e testarlo anziché crearlo.</span><span class="sxs-lookup"><span data-stu-id="e8898-215">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="e8898-216">Il download di esempio contiene codice aggiuntivo e collegamenti che rendono più semplice testare il progetto.</span><span class="sxs-lookup"><span data-stu-id="e8898-216">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="e8898-217">È possibile lasciare commenti e suggerimenti in [questa discussione su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6098) con i commenti sui download di esempio rispetto alle istruzioni dettagliate.</span><span class="sxs-lookup"><span data-stu-id="e8898-217">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="e8898-218">Testare l'app di download</span><span class="sxs-lookup"><span data-stu-id="e8898-218">Test the download app</span></span>

<span data-ttu-id="e8898-219">Se non è stata scaricata l'app completa e si vuole invece creare il progetto della procedura dettagliata, passare alla [prossima sezione](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="e8898-219">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e8898-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e8898-220">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e8898-221">Aprire il file con estensione *sln* in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e8898-221">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="e8898-222">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="e8898-222">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e8898-223">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="e8898-223">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e8898-224">Al prompt dei comandi nella directory *cli*, compilare la libreria di classi Razor e l'app Web.</span><span class="sxs-lookup"><span data-stu-id="e8898-224">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="e8898-225">Passare alla directory *WebApp1* ed eseguire l'app:</span><span class="sxs-lookup"><span data-stu-id="e8898-225">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="e8898-226">Seguire le istruzioni indicate in [Testare WebApp1](#test-webapp1)</span><span class="sxs-lookup"><span data-stu-id="e8898-226">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="e8898-227">Creare un RCL</span><span class="sxs-lookup"><span data-stu-id="e8898-227">Create an RCL</span></span>

<span data-ttu-id="e8898-228">In questa sezione viene creato un RCL.</span><span class="sxs-lookup"><span data-stu-id="e8898-228">In this section, an RCL is created.</span></span> <span data-ttu-id="e8898-229">Razor i file vengono aggiunti a RCL.</span><span class="sxs-lookup"><span data-stu-id="e8898-229">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e8898-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e8898-230">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e8898-231">Creare il progetto di libreria di classi Razor:</span><span class="sxs-lookup"><span data-stu-id="e8898-231">Create the RCL project:</span></span>

* <span data-ttu-id="e8898-232">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="e8898-232">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e8898-233">Selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="e8898-233">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="e8898-234">Denominare l'app **Razor UIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="e8898-234">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="e8898-235">Verificare che sia selezionato **ASP.NET Core 2.1** o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="e8898-235">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="e8898-236">Selezionare **Razor libreria di classi** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="e8898-236">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="e8898-237">Aggiungere un Razor file di visualizzazione parziale denominato *Razor UIClassLib/areas/la funzionalità/Pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e8898-237">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e8898-238">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="e8898-238">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e8898-239">Eseguire il comando seguente dalla riga di comando:</span><span class="sxs-lookup"><span data-stu-id="e8898-239">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="e8898-240">I comandi precedenti:</span><span class="sxs-lookup"><span data-stu-id="e8898-240">The preceding commands:</span></span>

* <span data-ttu-id="e8898-241">Crea l'oggetto `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="e8898-241">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="e8898-242">Crea una Razor pagina di _Message e la aggiunge a RCL.</span><span class="sxs-lookup"><span data-stu-id="e8898-242">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="e8898-243">Il parametro `-np` crea la pagina senza un `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="e8898-243">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="e8898-244">Crea un file [_ViewStart. cshtml](xref:mvc/views/layout#running-code-before-each-view) e lo aggiunge al RCL.</span><span class="sxs-lookup"><span data-stu-id="e8898-244">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="e8898-245">Il file *_ViewStart. cshtml* è necessario per usare il layout del Razor progetto Pages, che viene aggiunto nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="e8898-245">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-no-locrazor-files-and-folders-to-the-project"></a><span data-ttu-id="e8898-246">Aggiungere Razor file e cartelle al progetto</span><span class="sxs-lookup"><span data-stu-id="e8898-246">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="e8898-247">Sostituire il markup in *Razor UIClassLib/areas/la funzionalità/Pages/Shared/_Message. cshtml* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="e8898-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="e8898-248">Sostituire il markup in *Razor UIClassLib/areas/la funzionalità/Pages/Pagina1. cshtml* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="e8898-248">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="e8898-249">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` è necessario per usare la visualizzazione parziale (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="e8898-249">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="e8898-250">Anziché includere la direttiva `@addTagHelper`, è possibile aggiungere un file *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e8898-250">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="e8898-251">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e8898-251">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="e8898-252">Per altre informazioni su *_ViewImports. cshtml*, vedere [importazione di direttive condivise](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="e8898-252">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="e8898-253">Compilare la libreria di classi per verificare che non siano presenti errori del compilatore:</span><span class="sxs-lookup"><span data-stu-id="e8898-253">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="e8898-254">L'output di compilazione contiene *RazorUIClassLib.dll* e *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="e8898-254">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="e8898-255">*RazorUIClassLib.Views.dll* contiene il Razor contenuto compilato.</span><span class="sxs-lookup"><span data-stu-id="e8898-255">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-no-locrazor-ui-library-from-a-no-locrazor-pages-project"></a><span data-ttu-id="e8898-256">Usare la Razor libreria dell'interfaccia utente da un Razor progetto di pagine</span><span class="sxs-lookup"><span data-stu-id="e8898-256">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e8898-257">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e8898-257">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e8898-258">Creare l' Razor app Web Pages:</span><span class="sxs-lookup"><span data-stu-id="e8898-258">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="e8898-259">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla soluzione > **Aggiungi** >  **Nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="e8898-259">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="e8898-260">Selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="e8898-260">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="e8898-261">Denominare l'app **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="e8898-261">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="e8898-262">Verificare che sia selezionato **ASP.NET Core 2.1** o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="e8898-262">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="e8898-263">Selezionare **Applicazione Web** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="e8898-263">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="e8898-264">In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **WebApp1** e selezionare **Imposta come progetto di avvio**.</span><span class="sxs-lookup"><span data-stu-id="e8898-264">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="e8898-265">In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **WebApp1** e selezionare **Dipendenze di compilazione** > **Dipendenze progetto**.</span><span class="sxs-lookup"><span data-stu-id="e8898-265">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="e8898-266">Controllare **Razor UIClassLib** come dipendenza di **app Web 1**.</span><span class="sxs-lookup"><span data-stu-id="e8898-266">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="e8898-267">In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **WebApp1** e scegliere **Aggiungi** > **Riferimento**.</span><span class="sxs-lookup"><span data-stu-id="e8898-267">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="e8898-268">Nella finestra di dialogo **Gestione riferimenti** selezionare **Razor UIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="e8898-268">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="e8898-269">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="e8898-269">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e8898-270">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="e8898-270">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e8898-271">Creare un' Razor app Web di pagine e un file di soluzione contenente l' Razor app pagine e il RCL:</span><span class="sxs-lookup"><span data-stu-id="e8898-271">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="e8898-272">Compilare ed eseguire l'app Web:</span><span class="sxs-lookup"><span data-stu-id="e8898-272">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="e8898-273">Testare WebApp1</span><span class="sxs-lookup"><span data-stu-id="e8898-273">Test WebApp1</span></span>

<span data-ttu-id="e8898-274">Passare a `/MyFeature/Page1` per verificare che la Razor libreria di classi dell'interfaccia utente sia in uso.</span><span class="sxs-lookup"><span data-stu-id="e8898-274">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="e8898-275">Eseguire l'override di visualizzazioni, visualizzazioni parziali e pagine</span><span class="sxs-lookup"><span data-stu-id="e8898-275">Override views, partial views, and pages</span></span>

<span data-ttu-id="e8898-276">Quando si trova una visualizzazione, una visualizzazione parziale o una Razor pagina nell'app Web e nel RCL, il Razor markup (file con *estensione cshtml* ) nell'app Web ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="e8898-276">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="e8898-277">Ad esempio, aggiungere *app Web 1/areas/la funzionalità/Pages/Pagina1. cshtml* a app Web 1 e Pagina1 in app Web 1 avrà la precedenza su PAGINA1 in RCL.</span><span class="sxs-lookup"><span data-stu-id="e8898-277">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="e8898-278">Nel download di esempio, rinominare *WebApp1/aree/MyFeature2* in *WebApp1/aree/MyFeature* per testare la precedenza.</span><span class="sxs-lookup"><span data-stu-id="e8898-278">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="e8898-279">Copiare la visualizzazione parziale *Razor UIClassLib/areas/la funzionalità/Pages/Shared/_Message. cshtml* in *app Web 1/areas/my feature/pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e8898-279">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="e8898-280">Aggiornare il markup per indicare la nuova posizione.</span><span class="sxs-lookup"><span data-stu-id="e8898-280">Update the markup to indicate the new location.</span></span> <span data-ttu-id="e8898-281">Compilare ed eseguire l'app per verificare quale versione del parziale sia in uso.</span><span class="sxs-lookup"><span data-stu-id="e8898-281">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="e8898-282">Layout di pagine RCL</span><span class="sxs-lookup"><span data-stu-id="e8898-282">RCL Pages layout</span></span>

<span data-ttu-id="e8898-283">Per fare riferimento al contenuto RCL come se facesse parte della cartella *pagine* dell'app Web, creare il progetto RCL con la struttura di file seguente:</span><span class="sxs-lookup"><span data-stu-id="e8898-283">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="e8898-284">*RazorUIClassLib/pagine*</span><span class="sxs-lookup"><span data-stu-id="e8898-284">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="e8898-285">*RazorUIClassLib/pagine/condiviso*</span><span class="sxs-lookup"><span data-stu-id="e8898-285">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="e8898-286">Si supponga che *Razor UIClassLib/Pages/Shared* contenga due file parziali: *_Header. cshtml* e *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e8898-286">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="e8898-287">`<partial>`È possibile aggiungere i tag al file *_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="e8898-287">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e8898-288">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e8898-288">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:blazor/components/class-libraries>
* <xref:blazor/components/css-isolation#razor-class-library-rcl-support>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:blazor/components/class-libraries>

::: moniker-end
