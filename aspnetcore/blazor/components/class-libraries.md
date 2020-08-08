---
title: RazorLibrerie di classi dei componenti ASP.NET Core
author: guardrex
description: Scopri in che modo i componenti possono essere inclusi nelle Blazor app da una libreria di componenti esterna.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: 0bace66e0aab41cf31e18fe9f86dbf9bbcf59447
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014698"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="ec028-103">RazorLibrerie di classi dei componenti ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ec028-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="ec028-104">Di [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="ec028-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="ec028-105">I componenti possono essere condivisi in una [ Razor libreria di classi (RCL)](xref:razor-pages/ui-class) nei progetti.</span><span class="sxs-lookup"><span data-stu-id="ec028-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="ec028-106">È possibile includere una \* Razor libreria di classi Components\* da:</span><span class="sxs-lookup"><span data-stu-id="ec028-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="ec028-107">Un altro progetto nella soluzione.</span><span class="sxs-lookup"><span data-stu-id="ec028-107">Another project in the solution.</span></span>
* <span data-ttu-id="ec028-108">Un pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="ec028-108">A NuGet package.</span></span>
* <span data-ttu-id="ec028-109">Libreria .NET A cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="ec028-109">A referenced .NET library.</span></span>

<span data-ttu-id="ec028-110">Così come i componenti sono tipi .NET normali, i componenti forniti da un RCL sono assembly .NET normali.</span><span class="sxs-lookup"><span data-stu-id="ec028-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="ec028-111">Creare un RCL</span><span class="sxs-lookup"><span data-stu-id="ec028-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec028-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec028-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ec028-113">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="ec028-113">Create a new project.</span></span>
1. <span data-ttu-id="ec028-114">Selezionare \*\* Razor libreria di classi\*\*.</span><span class="sxs-lookup"><span data-stu-id="ec028-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="ec028-115">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="ec028-115">Select **Next**.</span></span>
1. <span data-ttu-id="ec028-116">Nella finestra di dialogo **Crea una nuova Razor libreria di classi** Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="ec028-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="ec028-117">Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito.</span><span class="sxs-lookup"><span data-stu-id="ec028-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="ec028-118">Gli esempi in questo argomento usano il nome del progetto `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="ec028-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="ec028-119">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="ec028-119">Select **Create**.</span></span>
1. <span data-ttu-id="ec028-120">Aggiungere RCL a una soluzione:</span><span class="sxs-lookup"><span data-stu-id="ec028-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="ec028-121">Fare clic con il pulsante destro del mouse sulla soluzione.</span><span class="sxs-lookup"><span data-stu-id="ec028-121">Right-click the solution.</span></span> <span data-ttu-id="ec028-122">Selezionare **Aggiungi**  >  **progetto esistente**.</span><span class="sxs-lookup"><span data-stu-id="ec028-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="ec028-123">Passare al file di progetto di RCL.</span><span class="sxs-lookup"><span data-stu-id="ec028-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="ec028-124">Selezionare il file di progetto di RCL ( `.csproj` ).</span><span class="sxs-lookup"><span data-stu-id="ec028-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="ec028-125">Aggiungere un riferimento a RCL dall'app:</span><span class="sxs-lookup"><span data-stu-id="ec028-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="ec028-126">Fare clic con il pulsante destro del mouse sul progetto app.</span><span class="sxs-lookup"><span data-stu-id="ec028-126">Right-click the app project.</span></span> <span data-ttu-id="ec028-127">Selezionare **Aggiungi**  >  **riferimento**.</span><span class="sxs-lookup"><span data-stu-id="ec028-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="ec028-128">Selezionare il progetto RCL.</span><span class="sxs-lookup"><span data-stu-id="ec028-128">Select the RCL project.</span></span> <span data-ttu-id="ec028-129">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="ec028-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="ec028-130">Se la casella di controllo **pagine e visualizzazioni di supporto** è selezionata durante la generazione del RCL dal modello, aggiungere anche un `_Imports.razor` file alla radice del progetto generato con il contenuto seguente per abilitare la Razor creazione dei componenti:</span><span class="sxs-lookup"><span data-stu-id="ec028-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="ec028-131">Aggiungere manualmente il file alla radice del progetto generato.</span><span class="sxs-lookup"><span data-stu-id="ec028-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ec028-132">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="ec028-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="ec028-133">Usare il modello \*\* Razor libreria di classi\*\* ( `razorclasslib` ) con il [`dotnet new`](/dotnet/core/tools/dotnet-new) comando in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="ec028-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="ec028-134">Nell'esempio seguente viene creato un RCL denominato `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="ec028-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="ec028-135">La cartella che include `ComponentLibrary` viene creata automaticamente quando si esegue il comando:</span><span class="sxs-lookup"><span data-stu-id="ec028-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="ec028-136">Se l' `-s|--support-pages-and-views` opzione viene utilizzata durante la generazione del RCL dal modello, aggiungere anche un `_Imports.razor` file alla radice del progetto generato con il contenuto seguente per abilitare la Razor creazione dei componenti:</span><span class="sxs-lookup"><span data-stu-id="ec028-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="ec028-137">Aggiungere manualmente il file alla radice del progetto generato.</span><span class="sxs-lookup"><span data-stu-id="ec028-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="ec028-138">Per aggiungere la libreria a un progetto esistente, usare il [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) comando in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="ec028-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="ec028-139">Nell'esempio seguente viene aggiunto RCL all'app.</span><span class="sxs-lookup"><span data-stu-id="ec028-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="ec028-140">Eseguire il comando seguente dalla cartella del progetto dell'app con il percorso della libreria:</span><span class="sxs-lookup"><span data-stu-id="ec028-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="ec028-141">Utilizzare un componente di libreria</span><span class="sxs-lookup"><span data-stu-id="ec028-141">Consume a library component</span></span>

<span data-ttu-id="ec028-142">Per utilizzare i componenti definiti in una raccolta in un altro progetto, utilizzare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="ec028-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="ec028-143">Usare il nome completo del tipo con lo spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="ec028-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="ec028-144">Usare Razor la [`@using`](xref:mvc/views/razor#using) direttiva.</span><span class="sxs-lookup"><span data-stu-id="ec028-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="ec028-145">I singoli componenti possono essere aggiunti in base al nome.</span><span class="sxs-lookup"><span data-stu-id="ec028-145">Individual components can be added by name.</span></span>

<span data-ttu-id="ec028-146">Negli esempi seguenti `ComponentLibrary` è una libreria di componenti contenente il `Component1` componente ( `Component1.razor` ).</span><span class="sxs-lookup"><span data-stu-id="ec028-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="ec028-147">Il `Component1` componente è un componente di esempio aggiunto automaticamente dal modello di progetto RCL al momento della creazione della libreria.</span><span class="sxs-lookup"><span data-stu-id="ec028-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="ec028-148">Fare riferimento al `Component1` componente utilizzando il relativo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="ec028-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="ec028-149">In alternativa, portare la libreria nell'ambito con una [`@using`](xref:mvc/views/razor#using) direttiva e usare il componente senza il relativo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="ec028-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="ec028-150">Facoltativamente, includere la `@using ComponentLibrary` direttiva nel file di primo livello `_Import.razor` per rendere disponibili i componenti della libreria a un intero progetto.</span><span class="sxs-lookup"><span data-stu-id="ec028-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="ec028-151">Aggiungere la direttiva a un `_Import.razor` file a qualsiasi livello per applicare lo spazio dei nomi a un singolo componente o a un set di componenti all'interno di una cartella.</span><span class="sxs-lookup"><span data-stu-id="ec028-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ec028-152">Per specificare `Component1` la `my-component` classe CSS per il componente, collegarsi al foglio di stile della libreria usando il [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) del Framework in `Component1.razor` :</span><span class="sxs-lookup"><span data-stu-id="ec028-152">To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:</span></span>

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

<span data-ttu-id="ec028-153">Per fornire il foglio di stile nell'app, è possibile collegarsi in alternativa al foglio di stile della libreria nel `wwwroot/index.html` file ( Blazor WebAssembly ) o nel file dell'app `Pages/_Host.cshtml` ( Blazor Server ):</span><span class="sxs-lookup"><span data-stu-id="ec028-153">To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<span data-ttu-id="ec028-154">Quando il `Link` componente viene usato in un componente figlio, l'asset collegato è disponibile anche per qualsiasi altro componente figlio del componente padre, purché venga eseguito il rendering dell'elemento figlio con il `Link` componente.</span><span class="sxs-lookup"><span data-stu-id="ec028-154">When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered.</span></span> <span data-ttu-id="ec028-155">La distinzione tra l'uso del `Link` componente in un componente figlio e l'inserimento di un `<link>` tag HTML in `wwwroot/index.html` o `Pages/_Host.cshtml` è che il tag HTML sottoposto a rendering di un componente del Framework:</span><span class="sxs-lookup"><span data-stu-id="ec028-155">The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="ec028-156">Può essere modificato in base allo stato dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="ec028-156">Can be modified by application state.</span></span> <span data-ttu-id="ec028-157">Un `<link>` tag HTML hardcoded non può essere modificato in base allo stato dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="ec028-157">A hard-coded `<link>` HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="ec028-158">Viene rimosso dal codice HTML `<head>` quando il componente padre non viene più sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="ec028-158">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="ec028-159">Per specificare `Component1` la `my-component` classe CSS, collegarsi al foglio di stile della libreria nel `wwwroot/index.html` file ( Blazor WebAssembly ) o nel file dell'app `Pages/_Host.cshtml` ( Blazor Server ):</span><span class="sxs-lookup"><span data-stu-id="ec028-159">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="ec028-160">Creare una Razor libreria di classi di componenti con asset statici</span><span class="sxs-lookup"><span data-stu-id="ec028-160">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="ec028-161">Un RCL può includere asset statici.</span><span class="sxs-lookup"><span data-stu-id="ec028-161">An RCL can include static assets.</span></span> <span data-ttu-id="ec028-162">Gli asset statici sono disponibili per qualsiasi app che usa la libreria.</span><span class="sxs-lookup"><span data-stu-id="ec028-162">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="ec028-163">Per altre informazioni, vedere <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="ec028-163">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="ec028-164">Fornire componenti e risorse statiche a più app ospitate Blazor</span><span class="sxs-lookup"><span data-stu-id="ec028-164">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="ec028-165">Per altre informazioni, vedere <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="ec028-165">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="ec028-166">Compilare, comprimere e spedire a NuGet</span><span class="sxs-lookup"><span data-stu-id="ec028-166">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="ec028-167">Poiché le librerie dei componenti sono librerie .NET standard, la creazione di pacchetti e la spedizione a NuGet non è diversa dalla creazione di pacchetti e dalla distribuzione di qualsiasi libreria a NuGet.</span><span class="sxs-lookup"><span data-stu-id="ec028-167">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="ec028-168">La creazione di pacchetti viene eseguita usando il [`dotnet pack`](/dotnet/core/tools/dotnet-pack) comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="ec028-168">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="ec028-169">Caricare il pacchetto in NuGet usando il [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) comando in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="ec028-169">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ec028-170">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="ec028-170">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="ec028-171">Aggiungere un file di configurazione del linker XML a una raccolta</span><span class="sxs-lookup"><span data-stu-id="ec028-171">Add an XML linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
