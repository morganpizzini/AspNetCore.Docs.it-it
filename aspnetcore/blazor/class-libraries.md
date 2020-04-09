---
title: librerie di classi dei componenti ASP.NET Core Razor
author: guardrex
description: Scopri come i componenti Blazor possono essere inclusi nelle app da una libreria di componenti esterna.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218766"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="73c0a-103">librerie di classi dei componenti ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="73c0a-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="73c0a-104">Di [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="73c0a-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="73c0a-105">I componenti possono essere condivisi in una libreria di [classi Razor (RCL)](xref:razor-pages/ui-class) tra progetti.</span><span class="sxs-lookup"><span data-stu-id="73c0a-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="73c0a-106">Una libreria di *classi di componenti Razor* può essere inclusa da:A Razor components library can be included from:</span><span class="sxs-lookup"><span data-stu-id="73c0a-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="73c0a-107">Un altro progetto nella soluzione.</span><span class="sxs-lookup"><span data-stu-id="73c0a-107">Another project in the solution.</span></span>
* <span data-ttu-id="73c0a-108">Un pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="73c0a-108">A NuGet package.</span></span>
* <span data-ttu-id="73c0a-109">Una libreria .NET di riferimento.</span><span class="sxs-lookup"><span data-stu-id="73c0a-109">A referenced .NET library.</span></span>

<span data-ttu-id="73c0a-110">Così come i componenti sono tipi .NET regolari, i componenti forniti da un RCL sono normali assembly .NET.</span><span class="sxs-lookup"><span data-stu-id="73c0a-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="73c0a-111">Creare un RCLCreate an RCL</span><span class="sxs-lookup"><span data-stu-id="73c0a-111">Create an RCL</span></span>

<span data-ttu-id="73c0a-112">Seguire le indicazioni <xref:blazor/get-started> contenute nell'articolo per configurare l'ambiente per Blazor.</span><span class="sxs-lookup"><span data-stu-id="73c0a-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="73c0a-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73c0a-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="73c0a-114">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="73c0a-114">Create a new project.</span></span>
1. <span data-ttu-id="73c0a-115">Selezionare **Libreria di classi Razor**.</span><span class="sxs-lookup"><span data-stu-id="73c0a-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="73c0a-116">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="73c0a-116">Select **Next**.</span></span>
1. <span data-ttu-id="73c0a-117">Nella finestra di dialogo Crea una nuova libreria di classi **Razor** selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="73c0a-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="73c0a-118">Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito.</span><span class="sxs-lookup"><span data-stu-id="73c0a-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="73c0a-119">Negli esempi di questo argomento `MyComponentLib1`viene utilizzato il nome del progetto .</span><span class="sxs-lookup"><span data-stu-id="73c0a-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="73c0a-120">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="73c0a-120">Select **Create**.</span></span>
1. <span data-ttu-id="73c0a-121">Aggiungere la RCL a una soluzione:Add the RCL to a solution:</span><span class="sxs-lookup"><span data-stu-id="73c0a-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="73c0a-122">Fare clic con il pulsante destro del mouse sulla soluzione.</span><span class="sxs-lookup"><span data-stu-id="73c0a-122">Right-click the solution.</span></span> <span data-ttu-id="73c0a-123">Selezionare **Aggiungi** > **progetto esistente**.</span><span class="sxs-lookup"><span data-stu-id="73c0a-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="73c0a-124">Passare al file di progetto della RCL.</span><span class="sxs-lookup"><span data-stu-id="73c0a-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="73c0a-125">Selezionare il file di progetto della RCL (*con estensione csproj*).</span><span class="sxs-lookup"><span data-stu-id="73c0a-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="73c0a-126">Aggiungere un riferimento RCL dall'app:Add a reference the RCL from the app:</span><span class="sxs-lookup"><span data-stu-id="73c0a-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="73c0a-127">Fare clic con il pulsante destro del mouse sul progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="73c0a-127">Right-click the app project.</span></span> <span data-ttu-id="73c0a-128">Selezionare **Aggiungi** > **riferimento**.</span><span class="sxs-lookup"><span data-stu-id="73c0a-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="73c0a-129">Selezionare il progetto RCL.</span><span class="sxs-lookup"><span data-stu-id="73c0a-129">Select the RCL project.</span></span> <span data-ttu-id="73c0a-130">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="73c0a-130">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="73c0a-131">Se la casella di controllo **Pagine e viste** di supporto è selezionata durante la generazione dell'RCL dal modello, aggiungere anche un file *_Imports.razor* alla radice del progetto generato con il contenuto seguente per abilitare la creazione del componente Razor:</span><span class="sxs-lookup"><span data-stu-id="73c0a-131">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="73c0a-132">Aggiungere manualmente il file alla radice del progetto generato.</span><span class="sxs-lookup"><span data-stu-id="73c0a-132">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="73c0a-133">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="73c0a-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="73c0a-134">Utilizzare il modello Libreria`razorclasslib`di classi **Razor** ( ) con il [comando dotnet new](/dotnet/core/tools/dotnet-new) in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="73c0a-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="73c0a-135">Nell'esempio riportato di seguito `MyComponentLib1`viene creato un oggetto RCL denominato .</span><span class="sxs-lookup"><span data-stu-id="73c0a-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="73c0a-136">La cartella `MyComponentLib1` che contiene viene creata automaticamente quando viene eseguito il comando:</span><span class="sxs-lookup"><span data-stu-id="73c0a-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="73c0a-137">Se `-s|--support-pages-and-views` l'opzione viene utilizzata durante la generazione dell'RCL dal modello, aggiungere anche un file *_Imports.razor* alla radice del progetto generato con il contenuto seguente per abilitare la creazione del componente Razor:</span><span class="sxs-lookup"><span data-stu-id="73c0a-137">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="73c0a-138">Aggiungere manualmente il file alla radice del progetto generato.</span><span class="sxs-lookup"><span data-stu-id="73c0a-138">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="73c0a-139">Per aggiungere la libreria a un progetto esistente, utilizzare il comando [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="73c0a-139">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="73c0a-140">Nell'esempio seguente, il valore RCL viene aggiunto all'app.</span><span class="sxs-lookup"><span data-stu-id="73c0a-140">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="73c0a-141">Eseguire il comando seguente dalla cartella del progetto dell'app con il percorso della libreria:</span><span class="sxs-lookup"><span data-stu-id="73c0a-141">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="73c0a-142">Utilizzo di un componente di libreria</span><span class="sxs-lookup"><span data-stu-id="73c0a-142">Consume a library component</span></span>

<span data-ttu-id="73c0a-143">Per utilizzare i componenti definiti in una libreria in un altro progetto, utilizzare uno dei seguenti approcci:</span><span class="sxs-lookup"><span data-stu-id="73c0a-143">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="73c0a-144">Utilizzare il nome completo del tipo con lo spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="73c0a-144">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="73c0a-145">Utilizzare la direttiva [ \@using](xref:mvc/views/razor#using) di Razor.Use Razor's using directive.</span><span class="sxs-lookup"><span data-stu-id="73c0a-145">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="73c0a-146">I singoli componenti possono essere aggiunti per nome.</span><span class="sxs-lookup"><span data-stu-id="73c0a-146">Individual components can be added by name.</span></span>

<span data-ttu-id="73c0a-147">Negli esempi seguenti `MyComponentLib1` è una libreria `SalesReport` di componenti contenente un componente.</span><span class="sxs-lookup"><span data-stu-id="73c0a-147">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="73c0a-148">È `SalesReport` possibile fare riferimento al componente usando il nome completo del tipo con spazio dei nomi:The component can be referenced using its full type name with namespace:</span><span class="sxs-lookup"><span data-stu-id="73c0a-148">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="73c0a-149">È inoltre possibile fare riferimento al componente se `@using` la libreria viene inserita nell'ambito con una direttiva:The component can also be referenced if the library is brought into scope with an directive:</span><span class="sxs-lookup"><span data-stu-id="73c0a-149">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="73c0a-150">Includere `@using MyComponentLib1` la direttiva nel file *_Import.razor* di primo livello per rendere i componenti della libreria disponibili per un intero progetto.</span><span class="sxs-lookup"><span data-stu-id="73c0a-150">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="73c0a-151">Aggiungere la direttiva a un file *_Import.razor* a qualsiasi livello per applicare lo spazio dei nomi a una singola pagina o a un set di pagine all'interno di una cartella.</span><span class="sxs-lookup"><span data-stu-id="73c0a-151">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="73c0a-152">Creare una libreria di classi di componenti Razor con risorse staticheCreate a Razor components class library with static assets</span><span class="sxs-lookup"><span data-stu-id="73c0a-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="73c0a-153">Un RCL può includere asset statici.</span><span class="sxs-lookup"><span data-stu-id="73c0a-153">An RCL can include static assets.</span></span> <span data-ttu-id="73c0a-154">Le risorse statiche sono disponibili per qualsiasi app che utilizza la libreria.</span><span class="sxs-lookup"><span data-stu-id="73c0a-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="73c0a-155">Per altre informazioni, vedere <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="73c0a-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="73c0a-156">Costruisci, impacchetta e spedisci a NuGet</span><span class="sxs-lookup"><span data-stu-id="73c0a-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="73c0a-157">Poiché le librerie di componenti sono librerie .NET standard, la confezione e la spedizione a NuGet non è diversa dalla creazione di pacchetti e la spedizione di qualsiasi libreria a NuGet.Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span><span class="sxs-lookup"><span data-stu-id="73c0a-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="73c0a-158">La creazione di pacchetti viene eseguita utilizzando il comando [dotnet pack](/dotnet/core/tools/dotnet-pack) in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="73c0a-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="73c0a-159">Caricare il pacchetto su NuGet usando il comando [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="73c0a-159">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73c0a-160">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="73c0a-160">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="73c0a-161">Aggiungere un file di configurazione del linker XML a una libreriaAdd an XML linker configuration file to a library</span><span class="sxs-lookup"><span data-stu-id="73c0a-161">Add an XML linker configuration file to a library</span></span>](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
