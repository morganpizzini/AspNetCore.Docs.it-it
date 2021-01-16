---
title: RazorLibrerie di classi dei componenti ASP.NET Core
author: guardrex
description: Scopri in che modo i componenti possono essere inclusi nelle Blazor app da una libreria di componenti esterna.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/12/2021
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 14370f9bbf45079fd3654d3e55af4178691cf4f5
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252552"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="1f868-103">RazorLibrerie di classi dei componenti ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f868-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="1f868-104">Di [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="1f868-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="1f868-105">I componenti possono essere condivisi in una [ Razor libreria di classi (RCL)](xref:razor-pages/ui-class) nei progetti.</span><span class="sxs-lookup"><span data-stu-id="1f868-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="1f868-106">È possibile includere una *Razor libreria di classi Components* da:</span><span class="sxs-lookup"><span data-stu-id="1f868-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="1f868-107">Un altro progetto nella soluzione.</span><span class="sxs-lookup"><span data-stu-id="1f868-107">Another project in the solution.</span></span>
* <span data-ttu-id="1f868-108">Un pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="1f868-108">A NuGet package.</span></span>
* <span data-ttu-id="1f868-109">Libreria .NET A cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="1f868-109">A referenced .NET library.</span></span>

<span data-ttu-id="1f868-110">Così come i componenti sono tipi .NET normali, i componenti forniti da un RCL sono assembly .NET normali.</span><span class="sxs-lookup"><span data-stu-id="1f868-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="1f868-111">Creare un RCL</span><span class="sxs-lookup"><span data-stu-id="1f868-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f868-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f868-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1f868-113">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="1f868-113">Create a new project.</span></span>
1. <span data-ttu-id="1f868-114">Selezionare **Razor libreria di classi**.</span><span class="sxs-lookup"><span data-stu-id="1f868-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="1f868-115">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="1f868-115">Select **Next**.</span></span>
1. <span data-ttu-id="1f868-116">Nella finestra di dialogo **Crea una nuova Razor libreria di classi** Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="1f868-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="1f868-117">Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito.</span><span class="sxs-lookup"><span data-stu-id="1f868-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="1f868-118">Gli esempi in questo argomento usano il nome del progetto `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="1f868-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="1f868-119">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="1f868-119">Select **Create**.</span></span>
1. <span data-ttu-id="1f868-120">Aggiungere RCL a una soluzione:</span><span class="sxs-lookup"><span data-stu-id="1f868-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="1f868-121">Fare clic con il pulsante destro del mouse sulla soluzione.</span><span class="sxs-lookup"><span data-stu-id="1f868-121">Right-click the solution.</span></span> <span data-ttu-id="1f868-122">Selezionare **Aggiungi**  >  **progetto esistente**.</span><span class="sxs-lookup"><span data-stu-id="1f868-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="1f868-123">Passare al file di progetto di RCL.</span><span class="sxs-lookup"><span data-stu-id="1f868-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="1f868-124">Selezionare il file di progetto di RCL ( `.csproj` ).</span><span class="sxs-lookup"><span data-stu-id="1f868-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="1f868-125">Aggiungere un riferimento a RCL dall'app:</span><span class="sxs-lookup"><span data-stu-id="1f868-125">Add a reference to the RCL from the app:</span></span>
   1. <span data-ttu-id="1f868-126">Fare clic con il pulsante destro del mouse sul progetto app.</span><span class="sxs-lookup"><span data-stu-id="1f868-126">Right-click the app project.</span></span> <span data-ttu-id="1f868-127">Selezionare **Aggiungi**  >  **riferimento**.</span><span class="sxs-lookup"><span data-stu-id="1f868-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="1f868-128">Selezionare il progetto RCL.</span><span class="sxs-lookup"><span data-stu-id="1f868-128">Select the RCL project.</span></span> <span data-ttu-id="1f868-129">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="1f868-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="1f868-130">Se la casella di controllo **pagine e visualizzazioni di supporto** è selezionata durante la generazione del RCL dal modello, aggiungere anche un `_Imports.razor` file alla radice del progetto generato con il contenuto seguente per abilitare la Razor creazione dei componenti:</span><span class="sxs-lookup"><span data-stu-id="1f868-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="1f868-131">Aggiungere manualmente il file alla radice del progetto generato.</span><span class="sxs-lookup"><span data-stu-id="1f868-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1f868-132">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="1f868-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="1f868-133">Usare il modello **Razor libreria di classi** ( `razorclasslib` ) con il [`dotnet new`](/dotnet/core/tools/dotnet-new) comando in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="1f868-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="1f868-134">Nell'esempio seguente viene creato un RCL denominato `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="1f868-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="1f868-135">La cartella che include `ComponentLibrary` viene creata automaticamente quando si esegue il comando:</span><span class="sxs-lookup"><span data-stu-id="1f868-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="1f868-136">Se l' `-s|--support-pages-and-views` opzione viene utilizzata durante la generazione del RCL dal modello, aggiungere anche un `_Imports.razor` file alla radice del progetto generato con il contenuto seguente per abilitare la Razor creazione dei componenti:</span><span class="sxs-lookup"><span data-stu-id="1f868-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="1f868-137">Aggiungere manualmente il file alla radice del progetto generato.</span><span class="sxs-lookup"><span data-stu-id="1f868-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="1f868-138">Per aggiungere la libreria a un progetto esistente, usare il [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) comando in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="1f868-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="1f868-139">Nell'esempio seguente viene aggiunto RCL all'app.</span><span class="sxs-lookup"><span data-stu-id="1f868-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="1f868-140">Eseguire il comando seguente dalla cartella del progetto dell'app con il percorso della libreria:</span><span class="sxs-lookup"><span data-stu-id="1f868-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="1f868-141">Utilizzare un componente di libreria</span><span class="sxs-lookup"><span data-stu-id="1f868-141">Consume a library component</span></span>

<span data-ttu-id="1f868-142">Per utilizzare i componenti definiti in una raccolta in un altro progetto, utilizzare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="1f868-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="1f868-143">Usare il nome completo del tipo con lo spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="1f868-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="1f868-144">Usare Razor la [`@using`](xref:mvc/views/razor#using) direttiva.</span><span class="sxs-lookup"><span data-stu-id="1f868-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="1f868-145">I singoli componenti possono essere aggiunti in base al nome.</span><span class="sxs-lookup"><span data-stu-id="1f868-145">Individual components can be added by name.</span></span>

<span data-ttu-id="1f868-146">Negli esempi seguenti `ComponentLibrary` è una libreria di componenti contenente il `Component1` componente ( `Component1.razor` ).</span><span class="sxs-lookup"><span data-stu-id="1f868-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="1f868-147">Il `Component1` componente è un componente di esempio aggiunto automaticamente dal modello di progetto RCL al momento della creazione della libreria.</span><span class="sxs-lookup"><span data-stu-id="1f868-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="1f868-148">Fare riferimento al `Component1` componente utilizzando il relativo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="1f868-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="1f868-149">In alternativa, portare la libreria nell'ambito con una [`@using`](xref:mvc/views/razor#using) direttiva e usare il componente senza il relativo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="1f868-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="1f868-150">Facoltativamente, includere la `@using ComponentLibrary` direttiva nel file di primo livello `_Import.razor` per rendere disponibili i componenti della libreria a un intero progetto.</span><span class="sxs-lookup"><span data-stu-id="1f868-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="1f868-151">Aggiungere la direttiva a un `_Import.razor` file a qualsiasi livello per applicare lo spazio dei nomi a un singolo componente o a un set di componenti all'interno di una cartella.</span><span class="sxs-lookup"><span data-stu-id="1f868-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="1f868-152">Per i componenti di libreria che usano l' [isolamento CSS](xref:blazor/components/css-isolation), non è necessario collegare in modo esplicito i fogli di stile dei singoli componenti della libreria nell'app che utilizza la libreria.</span><span class="sxs-lookup"><span data-stu-id="1f868-152">For library components that use [CSS isolation](xref:blazor/components/css-isolation), there's no need to explicitly link the library's individual component stylesheets in the app that consumes the library.</span></span> <span data-ttu-id="1f868-153">Gli stili dei componenti vengono resi disponibili automaticamente per l'app di consumo.</span><span class="sxs-lookup"><span data-stu-id="1f868-153">The component styles are automatically made available to the consuming app.</span></span>

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

NEXT PARAGRAPH: RECAST TO 'CAN ALSO ADOPT ...'

-->

<span data-ttu-id="1f868-154">Per fornire stili di componenti di libreria aggiuntivi dai fogli di stile nella `wwwroot` cartella della libreria, collegare i fogli di stile nel `wwwroot/index.html` file () o nel file dell'app che lo utilizza () Blazor WebAssembly `Pages/_Host.cshtml` Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="1f868-154">To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets in the consuming app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/additionalStyles.css" rel="stylesheet" />
</head>
```

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="1f868-155">Per specificare `Component1` la `my-component` classe CSS, collegarsi al foglio di stile della libreria nel `wwwroot/index.html` file ( Blazor WebAssembly ) o nel file dell'app `Pages/_Host.cshtml` ( Blazor Server ):</span><span class="sxs-lookup"><span data-stu-id="1f868-155">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="1f868-156">Creare una Razor libreria di classi di componenti con asset statici</span><span class="sxs-lookup"><span data-stu-id="1f868-156">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="1f868-157">Un RCL può includere asset statici.</span><span class="sxs-lookup"><span data-stu-id="1f868-157">An RCL can include static assets.</span></span> <span data-ttu-id="1f868-158">Gli asset statici sono disponibili per qualsiasi app che usa la libreria.</span><span class="sxs-lookup"><span data-stu-id="1f868-158">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="1f868-159">Per altre informazioni, vedere <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="1f868-159">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="1f868-160">Fornire componenti e risorse statiche a più app ospitate Blazor</span><span class="sxs-lookup"><span data-stu-id="1f868-160">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="1f868-161">Per altre informazioni, vedere <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="1f868-161">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a><span data-ttu-id="1f868-162">Analizzatore compatibilità browser per Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="1f868-162">Browser compatibility analyzer for Blazor WebAssembly</span></span>

<span data-ttu-id="1f868-163">Blazor WebAssembly le app sono destinate alla superficie di attacco dell'API .NET completa, ma non tutte le API .NET sono supportate nel webassembly a causa dei vincoli del sandbox del browser.</span><span class="sxs-lookup"><span data-stu-id="1f868-163">Blazor WebAssembly apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="1f868-164">Le API non supportate vengono generate <xref:System.PlatformNotSupportedException> durante l'esecuzione su webassembly.</span><span class="sxs-lookup"><span data-stu-id="1f868-164">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="1f868-165">Un analizzatore della compatibilità della piattaforma avvisa lo sviluppatore quando l'app usa API non supportate dalle piattaforme di destinazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1f868-165">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="1f868-166">Per le Blazor WebAssembly app, questo significa verificare che le API siano supportate nei browser.</span><span class="sxs-lookup"><span data-stu-id="1f868-166">For Blazor WebAssembly apps, this means checking that APIs are supported in browsers.</span></span> <span data-ttu-id="1f868-167">L'annotazione delle API di .NET Framework per Compatibility Analyzer è un processo in corso, quindi non tutte le API .NET Framework sono attualmente annotate.</span><span class="sxs-lookup"><span data-stu-id="1f868-167">Annotating .NET framework APIs for the compatibility analyzer is an on-going process, so not all .NET framework API is currently annotated.</span></span>

<span data-ttu-id="1f868-168">Blazor WebAssembly e Razor i progetti libreria di classi abilitano *automaticamente* i controlli compatibilità del browser aggiungendo `browser` come piattaforma supportata con l' `SupportedPlatform` elemento MSBuild.</span><span class="sxs-lookup"><span data-stu-id="1f868-168">Blazor WebAssembly and Razor class library projects *automatically* enable browser compatibilty checks by adding `browser` as a supported platform with the `SupportedPlatform` MSBuild item.</span></span> <span data-ttu-id="1f868-169">Gli sviluppatori di librerie possono aggiungere manualmente l' `SupportedPlatform` elemento al file di progetto di una raccolta per abilitare la funzionalità:</span><span class="sxs-lookup"><span data-stu-id="1f868-169">Library developers can manually add the `SupportedPlatform` item to a library's project file to enable the feature:</span></span>

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

<span data-ttu-id="1f868-170">Quando si crea una libreria, indicare che un'API specifica non è supportata nei browser specificando `browser` <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> :</span><span class="sxs-lookup"><span data-stu-id="1f868-170">When authoring a library, indicate that a particular API isn't supported in browsers by specifying `browser` to <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>:</span></span>

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

<span data-ttu-id="1f868-171">Per altre informazioni, vedere [annotazione delle API come non supportate in piattaforme specifiche (archivio GitHub DotNet/Designs](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span><span class="sxs-lookup"><span data-stu-id="1f868-171">For more information, see [Annotating APIs as unsupported on specific platforms (dotnet/designs GitHub repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span></span>

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="1f868-172">Blazor Isolamento JavaScript e riferimenti a oggetti</span><span class="sxs-lookup"><span data-stu-id="1f868-172">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="1f868-173">Blazor Abilita l'isolamento JavaScript nei [moduli JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)standard.</span><span class="sxs-lookup"><span data-stu-id="1f868-173">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="1f868-174">L'isolamento JavaScript offre i vantaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="1f868-174">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="1f868-175">Il codice JavaScript importato non inquina più lo spazio dei nomi globale.</span><span class="sxs-lookup"><span data-stu-id="1f868-175">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="1f868-176">I consumer della libreria e dei componenti non devono importare manualmente il codice JavaScript correlato.</span><span class="sxs-lookup"><span data-stu-id="1f868-176">Consumers of the library and components aren't required to manually import the related JavaScript.</span></span>

<span data-ttu-id="1f868-177">Per altre informazioni, vedere <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span><span class="sxs-lookup"><span data-stu-id="1f868-177">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="1f868-178">Compilare, comprimere e spedire a NuGet</span><span class="sxs-lookup"><span data-stu-id="1f868-178">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="1f868-179">Poiché le librerie dei componenti sono librerie .NET standard, la creazione di pacchetti e la spedizione a NuGet non è diversa dalla creazione di pacchetti e dalla distribuzione di qualsiasi libreria a NuGet.</span><span class="sxs-lookup"><span data-stu-id="1f868-179">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="1f868-180">La creazione di pacchetti viene eseguita usando il [`dotnet pack`](/dotnet/core/tools/dotnet-pack) comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="1f868-180">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="1f868-181">Caricare il pacchetto in NuGet usando il [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) comando in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="1f868-181">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f868-182">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="1f868-182">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="1f868-183">Aggiungere un file di configurazione del trimmer XML Intermediate Language (IL) a una raccolta</span><span class="sxs-lookup"><span data-stu-id="1f868-183">Add an XML Intermediate Language (IL) Trimmer configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-trimmer)
* [<span data-ttu-id="1f868-184">Supporto dell'isolamento CSS con le Razor librerie di classi</span><span class="sxs-lookup"><span data-stu-id="1f868-184">CSS isolation support with Razor class libraries</span></span>](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="1f868-185">Aggiungere un file di configurazione del linker (IL) XML Intermediate Language (IL) a una raccolta</span><span class="sxs-lookup"><span data-stu-id="1f868-185">Add an XML Intermediate Language (IL) Linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
