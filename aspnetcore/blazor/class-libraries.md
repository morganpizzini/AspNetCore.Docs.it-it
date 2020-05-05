---
title: Librerie Razor di classi dei componenti ASP.NET Core
author: guardrex
description: Scopri in che modo i componenti possono Blazor essere inclusi nelle app da una libreria di componenti esterna.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: 57f3494fd825b6549c40f56962da2c8076e8fd51
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767096"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="6a041-103">Librerie di classi dei componenti di ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="6a041-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="6a041-104">Di [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="6a041-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="6a041-105">I componenti possono essere condivisi in una [libreria di classi Razor (RCL)](xref:razor-pages/ui-class) tra i progetti.</span><span class="sxs-lookup"><span data-stu-id="6a041-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="6a041-106">È possibile includere una *libreria di classi di componenti Razor* da:</span><span class="sxs-lookup"><span data-stu-id="6a041-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="6a041-107">Un altro progetto nella soluzione.</span><span class="sxs-lookup"><span data-stu-id="6a041-107">Another project in the solution.</span></span>
* <span data-ttu-id="6a041-108">Un pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="6a041-108">A NuGet package.</span></span>
* <span data-ttu-id="6a041-109">Libreria .NET A cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="6a041-109">A referenced .NET library.</span></span>

<span data-ttu-id="6a041-110">Così come i componenti sono tipi .NET normali, i componenti forniti da un RCL sono assembly .NET normali.</span><span class="sxs-lookup"><span data-stu-id="6a041-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="6a041-111">Creare un RCL</span><span class="sxs-lookup"><span data-stu-id="6a041-111">Create an RCL</span></span>

<span data-ttu-id="6a041-112">Per configurare l'ambiente per <xref:blazor/get-started> blazer, seguire le istruzioni riportate nell'articolo.</span><span class="sxs-lookup"><span data-stu-id="6a041-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a041-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a041-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6a041-114">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="6a041-114">Create a new project.</span></span>
1. <span data-ttu-id="6a041-115">Selezionare **libreria di classi Razor**.</span><span class="sxs-lookup"><span data-stu-id="6a041-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="6a041-116">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="6a041-116">Select **Next**.</span></span>
1. <span data-ttu-id="6a041-117">Nella finestra di dialogo **Crea una nuova libreria di classi Razor** selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="6a041-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="6a041-118">Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito.</span><span class="sxs-lookup"><span data-stu-id="6a041-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="6a041-119">Gli esempi in questo argomento usano il nome `MyComponentLib1`del progetto.</span><span class="sxs-lookup"><span data-stu-id="6a041-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="6a041-120">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="6a041-120">Select **Create**.</span></span>
1. <span data-ttu-id="6a041-121">Aggiungere RCL a una soluzione:</span><span class="sxs-lookup"><span data-stu-id="6a041-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="6a041-122">Fare clic con il pulsante destro del mouse sulla soluzione.</span><span class="sxs-lookup"><span data-stu-id="6a041-122">Right-click the solution.</span></span> <span data-ttu-id="6a041-123">Selezionare **Aggiungi** > **progetto esistente**.</span><span class="sxs-lookup"><span data-stu-id="6a041-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="6a041-124">Passare al file di progetto di RCL.</span><span class="sxs-lookup"><span data-stu-id="6a041-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="6a041-125">Selezionare il file di progetto di RCL (con*estensione csproj*).</span><span class="sxs-lookup"><span data-stu-id="6a041-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="6a041-126">Aggiungere un riferimento a RCL dall'app:</span><span class="sxs-lookup"><span data-stu-id="6a041-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="6a041-127">Fare clic con il pulsante destro del mouse sul progetto app.</span><span class="sxs-lookup"><span data-stu-id="6a041-127">Right-click the app project.</span></span> <span data-ttu-id="6a041-128">Selezionare **Aggiungi** > **riferimento**.</span><span class="sxs-lookup"><span data-stu-id="6a041-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="6a041-129">Selezionare il progetto RCL.</span><span class="sxs-lookup"><span data-stu-id="6a041-129">Select the RCL project.</span></span> <span data-ttu-id="6a041-130">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="6a041-130">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="6a041-131">Se la casella di controllo **pagine e visualizzazioni di supporto** è selezionata durante la generazione del RCL dal modello, aggiungere anche un file *_Imports. Razor* alla radice del progetto generato con il contenuto seguente per abilitare la creazione dei componenti Razor:</span><span class="sxs-lookup"><span data-stu-id="6a041-131">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="6a041-132">Aggiungere manualmente il file alla radice del progetto generato.</span><span class="sxs-lookup"><span data-stu-id="6a041-132">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6a041-133">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="6a041-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="6a041-134">Usare il modello di **libreria di classi Razor** (`razorclasslib`) con il comando [DotNet New](/dotnet/core/tools/dotnet-new) in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="6a041-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="6a041-135">Nell'esempio seguente viene creato un RCL denominato `MyComponentLib1`.</span><span class="sxs-lookup"><span data-stu-id="6a041-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="6a041-136">La cartella che include `MyComponentLib1` viene creata automaticamente quando si esegue il comando:</span><span class="sxs-lookup"><span data-stu-id="6a041-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="6a041-137">Se l' `-s|--support-pages-and-views` opzione viene usata quando si genera RCL dal modello, aggiungere anche un file *_Imports. Razor* alla radice del progetto generato con il contenuto seguente per abilitare la creazione dei componenti Razor:</span><span class="sxs-lookup"><span data-stu-id="6a041-137">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="6a041-138">Aggiungere manualmente il file alla radice del progetto generato.</span><span class="sxs-lookup"><span data-stu-id="6a041-138">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="6a041-139">Per aggiungere la libreria a un progetto esistente, usare il comando [DotNet Add Reference](/dotnet/core/tools/dotnet-add-reference) in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="6a041-139">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="6a041-140">Nell'esempio seguente viene aggiunto RCL all'app.</span><span class="sxs-lookup"><span data-stu-id="6a041-140">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="6a041-141">Eseguire il comando seguente dalla cartella del progetto dell'app con il percorso della libreria:</span><span class="sxs-lookup"><span data-stu-id="6a041-141">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="6a041-142">Utilizzare un componente di libreria</span><span class="sxs-lookup"><span data-stu-id="6a041-142">Consume a library component</span></span>

<span data-ttu-id="6a041-143">Per utilizzare i componenti definiti in una raccolta in un altro progetto, utilizzare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="6a041-143">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="6a041-144">Usare il nome completo del tipo con lo spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="6a041-144">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="6a041-145">Usare Razorla [ \@direttiva using](xref:mvc/views/razor#using) .</span><span class="sxs-lookup"><span data-stu-id="6a041-145">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="6a041-146">I singoli componenti possono essere aggiunti in base al nome.</span><span class="sxs-lookup"><span data-stu-id="6a041-146">Individual components can be added by name.</span></span>

<span data-ttu-id="6a041-147">Negli esempi seguenti `MyComponentLib1` è una libreria di componenti contenente un `SalesReport` componente di.</span><span class="sxs-lookup"><span data-stu-id="6a041-147">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="6a041-148">È `SalesReport` possibile fare riferimento al componente usando il nome completo del tipo con lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="6a041-148">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="6a041-149">È possibile fare riferimento al componente anche se la libreria viene inserita nell'ambito con una `@using` direttiva:</span><span class="sxs-lookup"><span data-stu-id="6a041-149">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="6a041-150">Includere la `@using MyComponentLib1` direttiva nel file *_Import. Razor* di primo livello per rendere disponibili i componenti della libreria a un intero progetto.</span><span class="sxs-lookup"><span data-stu-id="6a041-150">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="6a041-151">Aggiungere la direttiva a un file *_Import. Razor* a qualsiasi livello per applicare lo spazio dei nomi a una singola pagina o a un set di pagine all'interno di una cartella.</span><span class="sxs-lookup"><span data-stu-id="6a041-151">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="6a041-152">Creare una Razor libreria di classi di componenti con asset statici</span><span class="sxs-lookup"><span data-stu-id="6a041-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="6a041-153">Un RCL può includere asset statici.</span><span class="sxs-lookup"><span data-stu-id="6a041-153">An RCL can include static assets.</span></span> <span data-ttu-id="6a041-154">Gli asset statici sono disponibili per qualsiasi app che usa la libreria.</span><span class="sxs-lookup"><span data-stu-id="6a041-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="6a041-155">Per altre informazioni, vedere <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="6a041-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="6a041-156">Compilare, comprimere e spedire a NuGet</span><span class="sxs-lookup"><span data-stu-id="6a041-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="6a041-157">Poiché le librerie dei componenti sono librerie .NET standard, la creazione di pacchetti e la spedizione a NuGet non è diversa dalla creazione di pacchetti e dalla distribuzione di qualsiasi libreria a NuGet.</span><span class="sxs-lookup"><span data-stu-id="6a041-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="6a041-158">La creazione di pacchetti viene eseguita usando il comando [DotNet Pack](/dotnet/core/tools/dotnet-pack) in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="6a041-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="6a041-159">Caricare il pacchetto in NuGet usando il comando [DotNet NuGet push](/dotnet/core/tools/dotnet-nuget-push) in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="6a041-159">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6a041-160">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6a041-160">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="6a041-161">Aggiungere un file di configurazione del linker XML a una raccolta</span><span class="sxs-lookup"><span data-stu-id="6a041-161">Add an XML linker configuration file to a library</span></span>](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
