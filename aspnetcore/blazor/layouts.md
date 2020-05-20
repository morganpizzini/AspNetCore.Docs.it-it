---
title: BlazorLayout ASP.NET Core
author: guardrex
description: Informazioni su come creare componenti di layout riutilizzabili per le Blazor app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 09cca9c4af23c35fdbc2ee92169913c960b0a68d
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424328"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="7dc55-103">BlazorLayout ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7dc55-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="7dc55-104">Di [Rainer Stropek](https://www.timecockpit.com) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7dc55-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7dc55-105">Alcuni elementi dell'app, ad esempio i menu, i messaggi di copyright e i logo aziendali, fanno in genere parte del layout generale dell'app e vengono usati da ogni componente nell'app.</span><span class="sxs-lookup"><span data-stu-id="7dc55-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="7dc55-106">La copia del codice di questi elementi in tutti i componenti di un'app non è un approccio efficace.</span><span class="sxs-lookup"><span data-stu-id="7dc55-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="7dc55-107">Ogni volta che uno degli elementi richiede un aggiornamento, è necessario aggiornare ogni componente.</span><span class="sxs-lookup"><span data-stu-id="7dc55-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="7dc55-108">Tale duplicazione è difficile da gestire e può causare contenuti incoerenti nel tempo.</span><span class="sxs-lookup"><span data-stu-id="7dc55-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="7dc55-109">I *layout* risolvono questo problema.</span><span class="sxs-lookup"><span data-stu-id="7dc55-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="7dc55-110">Tecnicamente, un layout è semplicemente un altro componente.</span><span class="sxs-lookup"><span data-stu-id="7dc55-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="7dc55-111">Un layout è definito in un Razor modello o nel codice C# e può usare [Data Binding](xref:blazor/data-binding), l' [inserimento delle dipendenze](xref:blazor/dependency-injection)e altri scenari di componenti.</span><span class="sxs-lookup"><span data-stu-id="7dc55-111">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="7dc55-112">Per trasformare un *componente* in un *layout*, il componente:</span><span class="sxs-lookup"><span data-stu-id="7dc55-112">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="7dc55-113">Eredita da `LayoutComponentBase` , che definisce una `Body` proprietà per il contenuto di cui è stato eseguito il rendering all'interno del layout.</span><span class="sxs-lookup"><span data-stu-id="7dc55-113">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="7dc55-114">Usa la Razor sintassi `@Body` per specificare il percorso nel markup di layout in cui viene eseguito il rendering del contenuto.</span><span class="sxs-lookup"><span data-stu-id="7dc55-114">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="7dc55-115">Nell'esempio di codice seguente viene illustrato il Razor modello di un componente di layout, *MainLayout. Razor*.</span><span class="sxs-lookup"><span data-stu-id="7dc55-115">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="7dc55-116">Il layout eredita `LayoutComponentBase` e imposta l'oggetto `@Body` tra la barra di spostamento e il piè di pagina:</span><span class="sxs-lookup"><span data-stu-id="7dc55-116">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="7dc55-117">In un'app basata su uno dei Blazor modelli di app, il `MainLayout` componente (*MainLayout. Razor*) si trova nella cartella *condivisa* dell'app.</span><span class="sxs-lookup"><span data-stu-id="7dc55-117">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="7dc55-118">Layout predefinito</span><span class="sxs-lookup"><span data-stu-id="7dc55-118">Default layout</span></span>

<span data-ttu-id="7dc55-119">Specificare il layout predefinito dell'app nel `Router` componente nel file *app. Razor* dell'app.</span><span class="sxs-lookup"><span data-stu-id="7dc55-119">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="7dc55-120">Il `Router` componente seguente, fornito dai Blazor modelli predefiniti, imposta il layout predefinito sul `MainLayout` componente:</span><span class="sxs-lookup"><span data-stu-id="7dc55-120">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="7dc55-121">Per fornire un layout predefinito per il `NotFound` contenuto, specificare un oggetto `LayoutView` per il `NotFound` contenuto:</span><span class="sxs-lookup"><span data-stu-id="7dc55-121">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="7dc55-122">Per ulteriori informazioni sul `Router` componente, vedere <xref:blazor/routing> .</span><span class="sxs-lookup"><span data-stu-id="7dc55-122">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="7dc55-123">La specifica del layout come layout predefinito nel router è una procedura utile perché può essere sottoposta a override in base al singolo componente o alla cartella.</span><span class="sxs-lookup"><span data-stu-id="7dc55-123">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="7dc55-124">Preferisci usare il router per impostare il layout predefinito dell'app perché è la tecnica più generale.</span><span class="sxs-lookup"><span data-stu-id="7dc55-124">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="7dc55-125">Specificare un layout in un componente</span><span class="sxs-lookup"><span data-stu-id="7dc55-125">Specify a layout in a component</span></span>

<span data-ttu-id="7dc55-126">Utilizzare la Razor direttiva `@layout` per applicare un layout a un componente.</span><span class="sxs-lookup"><span data-stu-id="7dc55-126">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="7dc55-127">Il compilatore esegue la conversione `@layout` in un oggetto `LayoutAttribute` , che viene applicato alla classe Component.</span><span class="sxs-lookup"><span data-stu-id="7dc55-127">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="7dc55-128">Il contenuto del componente seguente `MasterList` viene inserito nella `MasterLayout` posizione di `@Body` :</span><span class="sxs-lookup"><span data-stu-id="7dc55-128">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="7dc55-129">La specifica del layout direttamente in un componente sostituisce un set di *layout predefinito* nel router o una `@layout` direttiva importata da *_Imports. Razor*.</span><span class="sxs-lookup"><span data-stu-id="7dc55-129">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="7dc55-130">Selezione layout centralizzata</span><span class="sxs-lookup"><span data-stu-id="7dc55-130">Centralized layout selection</span></span>

<span data-ttu-id="7dc55-131">Ogni cartella di un'app può contenere facoltativamente un file modello denominato *_Imports. Razor*.</span><span class="sxs-lookup"><span data-stu-id="7dc55-131">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="7dc55-132">Il compilatore include le direttive specificate nel file Imports in tutti i Razor modelli nella stessa cartella e in modo ricorsivo in tutte le relative sottocartelle.</span><span class="sxs-lookup"><span data-stu-id="7dc55-132">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="7dc55-133">Pertanto, un file *_Imports. Razor* contenente `@layout MyCoolLayout` garantisce che tutti i componenti di una cartella usino `MyCoolLayout` .</span><span class="sxs-lookup"><span data-stu-id="7dc55-133">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="7dc55-134">Non è necessario aggiungere ripetutamente `@layout MyCoolLayout` a tutti i file con *estensione Razor* nella cartella e nelle sottocartelle.</span><span class="sxs-lookup"><span data-stu-id="7dc55-134">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="7dc55-135">`@using`le direttive vengono applicate anche ai componenti nello stesso modo.</span><span class="sxs-lookup"><span data-stu-id="7dc55-135">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="7dc55-136">Il seguente file di *_Imports. Razor* Imports:</span><span class="sxs-lookup"><span data-stu-id="7dc55-136">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="7dc55-137">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="7dc55-137">`MyCoolLayout`.</span></span>
* <span data-ttu-id="7dc55-138">Tutti i Razor componenti nella stessa cartella e in tutte le sottocartelle.</span><span class="sxs-lookup"><span data-stu-id="7dc55-138">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="7dc55-139">Spazio dei nomi `BlazorApp1.Data`.</span><span class="sxs-lookup"><span data-stu-id="7dc55-139">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="7dc55-140">Il file *_Imports. Razor* è simile al [file _ViewImports. cshtml per le Razor visualizzazioni e le pagine,](xref:mvc/views/layout#importing-shared-directives) ma viene applicato in modo specifico ai Razor file del componente.</span><span class="sxs-lookup"><span data-stu-id="7dc55-140">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="7dc55-141">Specifica di un layout in *_Imports. Razor* sostituisce un layout specificato come *layout predefinito*del router.</span><span class="sxs-lookup"><span data-stu-id="7dc55-141">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="7dc55-142">Layout annidati</span><span class="sxs-lookup"><span data-stu-id="7dc55-142">Nested layouts</span></span>

<span data-ttu-id="7dc55-143">Le app possono essere costituite da layout annidati.</span><span class="sxs-lookup"><span data-stu-id="7dc55-143">Apps can consist of nested layouts.</span></span> <span data-ttu-id="7dc55-144">Un componente può fare riferimento a un layout che a sua volta fa riferimento a un altro layout.</span><span class="sxs-lookup"><span data-stu-id="7dc55-144">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="7dc55-145">Ad esempio, i layout di annidamento vengono usati per creare una struttura di menu a più livelli.</span><span class="sxs-lookup"><span data-stu-id="7dc55-145">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="7dc55-146">Nell'esempio seguente viene illustrato come utilizzare layout annidati.</span><span class="sxs-lookup"><span data-stu-id="7dc55-146">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="7dc55-147">Il file *EpisodesComponent. Razor* è il componente da visualizzare.</span><span class="sxs-lookup"><span data-stu-id="7dc55-147">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="7dc55-148">Il componente fa riferimento a `MasterListLayout` :</span><span class="sxs-lookup"><span data-stu-id="7dc55-148">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="7dc55-149">Il file *MasterListLayout. Razor* fornisce `MasterListLayout` .</span><span class="sxs-lookup"><span data-stu-id="7dc55-149">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="7dc55-150">Il layout fa riferimento a un altro layout, `MasterLayout` , in cui viene eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="7dc55-150">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="7dc55-151">`EpisodesComponent`viene sottoposto a rendering quando `@Body` viene visualizzato:</span><span class="sxs-lookup"><span data-stu-id="7dc55-151">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="7dc55-152">Infine, `MasterLayout` in *MasterLayout. Razor* contiene gli elementi di layout di primo livello, ad esempio l'intestazione, il menu principale e il piè di pagina.</span><span class="sxs-lookup"><span data-stu-id="7dc55-152">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="7dc55-153">`MasterListLayout`con `EpisodesComponent` viene eseguito il rendering di quando viene `@Body` visualizzato:</span><span class="sxs-lookup"><span data-stu-id="7dc55-153">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="7dc55-154">Condividere un Razor layout di pagine con componenti integrati</span><span class="sxs-lookup"><span data-stu-id="7dc55-154">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="7dc55-155">Quando i componenti instradabili sono integrati in un' Razor app pagine, il layout condiviso dell'app può essere usato con i componenti.</span><span class="sxs-lookup"><span data-stu-id="7dc55-155">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="7dc55-156">Per altre informazioni, vedere <xref:blazor/integrate-components>.</span><span class="sxs-lookup"><span data-stu-id="7dc55-156">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7dc55-157">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="7dc55-157">Additional resources</span></span>

* <xref:mvc/views/layout>
