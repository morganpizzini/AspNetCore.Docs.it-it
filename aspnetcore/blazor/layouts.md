---
title: BlazorLayout ASP.NET Core
author: guardrex
description: Informazioni su come creare componenti di layout riutilizzabili per le Blazor app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
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
uid: blazor/layouts
ms.openlocfilehash: c6f9815f473380f96092d663cdd785fd19109701
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855300"
---
# <a name="aspnet-core-no-locblazor-layouts"></a><span data-ttu-id="c584a-103">BlazorLayout ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c584a-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="c584a-104">Di [Rainer Stropek](https://www.timecockpit.com) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c584a-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c584a-105">Alcuni elementi dell'app, ad esempio i menu, i messaggi di copyright e i logo aziendali, fanno in genere parte del layout generale dell'app e vengono usati da ogni componente nell'app.</span><span class="sxs-lookup"><span data-stu-id="c584a-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="c584a-106">La copia del codice di questi elementi in tutti i componenti di un'app non è un approccio efficace.</span><span class="sxs-lookup"><span data-stu-id="c584a-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="c584a-107">Ogni volta che uno degli elementi richiede un aggiornamento, è necessario aggiornare ogni componente.</span><span class="sxs-lookup"><span data-stu-id="c584a-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="c584a-108">Tale duplicazione è difficile da gestire e può causare contenuti incoerenti nel tempo.</span><span class="sxs-lookup"><span data-stu-id="c584a-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="c584a-109">I *layout* risolvono questo problema.</span><span class="sxs-lookup"><span data-stu-id="c584a-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="c584a-110">Tecnicamente, un layout è semplicemente un altro componente.</span><span class="sxs-lookup"><span data-stu-id="c584a-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="c584a-111">Un layout è definito in un Razor modello o nel codice C# e può usare [Data Binding](xref:blazor/components/data-binding), l' [inserimento delle dipendenze](xref:blazor/fundamentals/dependency-injection)e altri scenari di componenti.</span><span class="sxs-lookup"><span data-stu-id="c584a-111">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="c584a-112">Per convertire un componente in un layout:</span><span class="sxs-lookup"><span data-stu-id="c584a-112">To convert a component into a layout:</span></span>

* <span data-ttu-id="c584a-113">Ereditare il componente da <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="c584a-113">Inherit the component from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>.</span></span> <span data-ttu-id="c584a-114"><xref:Microsoft.AspNetCore.Components.LayoutComponentBase>Definisce una <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> proprietà per il contenuto di cui è stato eseguito il rendering all'interno del layout.</span><span class="sxs-lookup"><span data-stu-id="c584a-114">The <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="c584a-115">Utilizzare la Razor sintassi `@Body` per specificare il percorso nel markup di layout in cui viene eseguito il rendering del contenuto.</span><span class="sxs-lookup"><span data-stu-id="c584a-115">Use the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="c584a-116">Nell'esempio di codice riportato di seguito viene illustrato il Razor modello di un componente di layout `MainLayout.razor` .</span><span class="sxs-lookup"><span data-stu-id="c584a-116">The following code sample shows the Razor template of a layout component, `MainLayout.razor`.</span></span> <span data-ttu-id="c584a-117">Il layout eredita <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> e imposta l'oggetto `@Body` tra la barra di spostamento e il piè di pagina:</span><span class="sxs-lookup"><span data-stu-id="c584a-117">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor)]

## <a name="mainlayout-component"></a><span data-ttu-id="c584a-118">Componente `MainLayout`</span><span class="sxs-lookup"><span data-stu-id="c584a-118">`MainLayout` component</span></span>

<span data-ttu-id="c584a-119">In un'app basata su uno dei Blazor modelli di progetto, il `MainLayout` componente ( `MainLayout.razor` ) si trova nella cartella dell'app `Shared` :</span><span class="sxs-lookup"><span data-stu-id="c584a-119">In an app based on one of the Blazor project templates, the `MainLayout` component (`MainLayout.razor`) is in the app's `Shared` folder:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](./common/samples/5.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](./common/samples/3.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

## <a name="default-layout"></a><span data-ttu-id="c584a-120">Layout predefinito</span><span class="sxs-lookup"><span data-stu-id="c584a-120">Default layout</span></span>

<span data-ttu-id="c584a-121">Specificare il layout dell'app predefinito nel <xref:Microsoft.AspNetCore.Components.Routing.Router> componente nel file dell'app `App.razor` .</span><span class="sxs-lookup"><span data-stu-id="c584a-121">Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component in the app's `App.razor` file.</span></span> <span data-ttu-id="c584a-122">Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente seguente, fornito dai Blazor modelli predefiniti, imposta il layout predefinito sul `MainLayout` componente:</span><span class="sxs-lookup"><span data-stu-id="c584a-122">The following <xref:Microsoft.AspNetCore.Components.Routing.Router> component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="c584a-123">Per fornire un layout predefinito per il <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenuto, specificare un oggetto <xref:Microsoft.AspNetCore.Components.LayoutView> per il <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenuto:</span><span class="sxs-lookup"><span data-stu-id="c584a-123">To supply a default layout for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, specify a <xref:Microsoft.AspNetCore.Components.LayoutView> for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="c584a-124">Per ulteriori informazioni sul <xref:Microsoft.AspNetCore.Components.Routing.Router> componente, vedere <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="c584a-124">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="c584a-125">La specifica del layout come layout predefinito nel router è una procedura utile perché può essere sottoposta a override in base al singolo componente o alla cartella.</span><span class="sxs-lookup"><span data-stu-id="c584a-125">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="c584a-126">Preferisci usare il router per impostare il layout predefinito dell'app perché è la tecnica più generale.</span><span class="sxs-lookup"><span data-stu-id="c584a-126">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="c584a-127">Specificare un layout in un componente</span><span class="sxs-lookup"><span data-stu-id="c584a-127">Specify a layout in a component</span></span>

<span data-ttu-id="c584a-128">Utilizzare la Razor direttiva `@layout` per applicare un layout a un componente.</span><span class="sxs-lookup"><span data-stu-id="c584a-128">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="c584a-129">Il compilatore esegue la conversione `@layout` in un oggetto <xref:Microsoft.AspNetCore.Components.LayoutAttribute> , che viene applicato alla classe Component.</span><span class="sxs-lookup"><span data-stu-id="c584a-129">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, which is applied to the component class.</span></span>

<span data-ttu-id="c584a-130">Il contenuto del componente seguente `MasterList` viene inserito nella `MasterLayout` posizione di `@Body` :</span><span class="sxs-lookup"><span data-stu-id="c584a-130">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="c584a-131">La specifica del layout direttamente in un componente sostituisce un set di *layout predefinito* nel router o una `@layout` direttiva importata da `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="c584a-131">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from `_Imports.razor`.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="c584a-132">Selezione layout centralizzata</span><span class="sxs-lookup"><span data-stu-id="c584a-132">Centralized layout selection</span></span>

<span data-ttu-id="c584a-133">Ogni cartella di un'app può contenere facoltativamente un file modello denominato `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="c584a-133">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="c584a-134">Il compilatore include le direttive specificate nel file Imports in tutti i Razor modelli nella stessa cartella e in modo ricorsivo in tutte le relative sottocartelle.</span><span class="sxs-lookup"><span data-stu-id="c584a-134">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="c584a-135">Pertanto, un `_Imports.razor` file contenente `@layout MyCoolLayout` garantisce che tutti i componenti di una cartella usino `MyCoolLayout` .</span><span class="sxs-lookup"><span data-stu-id="c584a-135">Therefore, an `_Imports.razor` file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="c584a-136">Non è necessario aggiungere ripetutamente `@layout MyCoolLayout` a tutti i file nella `.razor` cartella e nelle sottocartelle.</span><span class="sxs-lookup"><span data-stu-id="c584a-136">There's no need to repeatedly add `@layout MyCoolLayout` to all of the `.razor` files within the folder and subfolders.</span></span> <span data-ttu-id="c584a-137">`@using` le direttive vengono applicate anche ai componenti nello stesso modo.</span><span class="sxs-lookup"><span data-stu-id="c584a-137">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="c584a-138">Le `_Imports.razor` importazioni di file seguenti:</span><span class="sxs-lookup"><span data-stu-id="c584a-138">The following `_Imports.razor` file imports:</span></span>

* <span data-ttu-id="c584a-139">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="c584a-139">`MyCoolLayout`.</span></span>
* <span data-ttu-id="c584a-140">Tutti i Razor componenti nella stessa cartella e in tutte le sottocartelle.</span><span class="sxs-lookup"><span data-stu-id="c584a-140">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="c584a-141">Spazio dei nomi `BlazorApp1.Data`.</span><span class="sxs-lookup"><span data-stu-id="c584a-141">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="c584a-142">Il `_Imports.razor` file è simile al [file _ViewImports. cshtml per le Razor visualizzazioni e le pagine,](xref:mvc/views/layout#importing-shared-directives) ma viene applicato in modo specifico ai Razor file del componente.</span><span class="sxs-lookup"><span data-stu-id="c584a-142">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="c584a-143">La specifica di un layout in `_Imports.razor` sostituisce un layout specificato come *layout predefinito* del router.</span><span class="sxs-lookup"><span data-stu-id="c584a-143">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's *default layout*.</span></span>

> [!WARNING]
> <span data-ttu-id="c584a-144">**Non** aggiungere una Razor `@layout` direttiva al `_Imports.razor` file radice, il che comporta un ciclo infinito di layout nell'app.</span><span class="sxs-lookup"><span data-stu-id="c584a-144">Do **not** add a Razor `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts in the app.</span></span> <span data-ttu-id="c584a-145">Per controllare il layout predefinito dell'app, specificare il layout nel `Router` componente.</span><span class="sxs-lookup"><span data-stu-id="c584a-145">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="c584a-146">Per ulteriori informazioni, vedere la sezione [layout predefinito](#default-layout) .</span><span class="sxs-lookup"><span data-stu-id="c584a-146">For more information, see the [Default layout](#default-layout) section.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="c584a-147">Layout annidati</span><span class="sxs-lookup"><span data-stu-id="c584a-147">Nested layouts</span></span>

<span data-ttu-id="c584a-148">Le app possono essere costituite da layout annidati.</span><span class="sxs-lookup"><span data-stu-id="c584a-148">Apps can consist of nested layouts.</span></span> <span data-ttu-id="c584a-149">Un componente può fare riferimento a un layout che a sua volta fa riferimento a un altro layout.</span><span class="sxs-lookup"><span data-stu-id="c584a-149">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="c584a-150">Ad esempio, i layout di annidamento vengono usati per creare una struttura di menu a più livelli.</span><span class="sxs-lookup"><span data-stu-id="c584a-150">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="c584a-151">Nell'esempio seguente viene illustrato come utilizzare layout annidati.</span><span class="sxs-lookup"><span data-stu-id="c584a-151">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="c584a-152">Il `EpisodesComponent.razor` file è il componente da visualizzare.</span><span class="sxs-lookup"><span data-stu-id="c584a-152">The `EpisodesComponent.razor` file is the component to display.</span></span> <span data-ttu-id="c584a-153">Il componente fa riferimento a `MasterListLayout` :</span><span class="sxs-lookup"><span data-stu-id="c584a-153">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="c584a-154">Il `MasterListLayout.razor` file fornisce il `MasterListLayout` .</span><span class="sxs-lookup"><span data-stu-id="c584a-154">The `MasterListLayout.razor` file provides the `MasterListLayout`.</span></span> <span data-ttu-id="c584a-155">Il layout fa riferimento a un altro layout, `MasterLayout` , in cui viene eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="c584a-155">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="c584a-156">`EpisodesComponent` viene sottoposto a rendering quando `@Body` viene visualizzato:</span><span class="sxs-lookup"><span data-stu-id="c584a-156">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="c584a-157">Infine, `MasterLayout` in `MasterLayout.razor` contiene gli elementi di layout di primo livello, ad esempio l'intestazione, il menu principale e il piè di pagina.</span><span class="sxs-lookup"><span data-stu-id="c584a-157">Finally, `MasterLayout` in `MasterLayout.razor` contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="c584a-158">`MasterListLayout` con `EpisodesComponent` viene eseguito il rendering di quando viene `@Body` visualizzato:</span><span class="sxs-lookup"><span data-stu-id="c584a-158">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-no-locrazor-pages-layout-with-integrated-components"></a><span data-ttu-id="c584a-159">Condividere un Razor layout di pagine con componenti integrati</span><span class="sxs-lookup"><span data-stu-id="c584a-159">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="c584a-160">Quando i componenti instradabili sono integrati in un' Razor app pagine, il layout condiviso dell'app può essere usato con i componenti.</span><span class="sxs-lookup"><span data-stu-id="c584a-160">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="c584a-161">Per altre informazioni, vedere <xref:blazor/components/prerendering-and-integration>.</span><span class="sxs-lookup"><span data-stu-id="c584a-161">For more information, see <xref:blazor/components/prerendering-and-integration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c584a-162">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="c584a-162">Additional resources</span></span>

* <xref:mvc/views/layout>
