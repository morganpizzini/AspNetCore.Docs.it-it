---
title: layout Blazor di ASP.NET Core
author: guardrex
description: Scopri come creare componenti di Blazor layout riutilizzabili per le app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5b6e1c7ceb4a6e41230e31bbe379bde1bb0a8286
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660412"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a><span data-ttu-id="f7f7e-103">layout Blazor di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7f7e-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="f7f7e-104">Di [Rainer Stropek](https://www.timecockpit.com) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f7f7e-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f7f7e-105">Alcuni elementi dell'app, ad esempio menu, messaggi di copyright e loghi aziendali, fanno in genere parte del layout generale dell'app e vengono usati da ogni componente dell'app.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="f7f7e-106">La copia del codice di questi elementi in tutti i&mdash;componenti di un'app non è un approccio efficiente ogni volta che uno degli elementi richiede un aggiornamento, ogni componente deve essere aggiornato.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="f7f7e-107">Tale duplicazione è difficile da mantenere e può portare a contenuti incoerenti nel tempo.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="f7f7e-108">*I layout* risolvono questo problema.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="f7f7e-109">Tecnicamente, un layout è solo un altro componente.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="f7f7e-110">Un layout è definito in un modello Razor o nel codice C , e può utilizzare [l'associazione dati,](xref:blazor/data-binding) [l'inserimento](xref:blazor/dependency-injection)delle dipendenze e altri scenari di componenti.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="f7f7e-111">Per trasformare un *componente* in un *layout,* il componente:</span><span class="sxs-lookup"><span data-stu-id="f7f7e-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="f7f7e-112">Eredita da `LayoutComponentBase`, che `Body` definisce una proprietà per il contenuto di cui è stato eseguito il rendering all'interno del layout.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="f7f7e-113">Utilizza la sintassi `@Body` Razor per specificare la posizione nel markup del layout in cui viene eseguito il rendering del contenuto.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="f7f7e-114">Nell'esempio di codice riportato di seguito viene illustrato il modello Razor di un componente di layout, *MainLayout.razor*.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="f7f7e-115">Il layout `LayoutComponentBase` eredita e `@Body` imposta l'oggetto tra la barra di navigazione e il piè di pagina:</span><span class="sxs-lookup"><span data-stu-id="f7f7e-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="f7f7e-116">In un'app basata Blazor su uno `MainLayout` dei modelli di app, il componente (*MainLayout.razor*) si trova nella cartella *Shared* dell'app.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="f7f7e-117">Layout predefinito</span><span class="sxs-lookup"><span data-stu-id="f7f7e-117">Default layout</span></span>

<span data-ttu-id="f7f7e-118">Specificare il layout `Router` predefinito dell'app nel componente nel file *App.razor* dell'app.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="f7f7e-119">Il `Router` componente seguente, fornito dai Blazor modelli di default, `MainLayout` imposta il layout di default sul componente:</span><span class="sxs-lookup"><span data-stu-id="f7f7e-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="f7f7e-120">Per fornire un `NotFound` layout predefinito `LayoutView` per `NotFound` il contenuto, specificare un per il contenuto:</span><span class="sxs-lookup"><span data-stu-id="f7f7e-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="f7f7e-121">Per ulteriori informazioni `Router` sul <xref:blazor/routing>componente, vedere .</span><span class="sxs-lookup"><span data-stu-id="f7f7e-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="f7f7e-122">Specificare il layout come layout predefinito nel router è una procedura utile perché può essere sostituito in base al componente o alla cartella.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-122">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="f7f7e-123">Preferisci usare il router per impostare il layout predefinito dell'app perché è la tecnica più generale.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-123">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="f7f7e-124">Specificare un layout in un componente</span><span class="sxs-lookup"><span data-stu-id="f7f7e-124">Specify a layout in a component</span></span>

<span data-ttu-id="f7f7e-125">Utilizzare il Razor direttiva `@layout` per applicare un layout a un componente.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-125">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="f7f7e-126">Il compilatore `@layout` converte in un `LayoutAttribute`oggetto , che viene applicato alla classe del componente.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-126">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="f7f7e-127">Il contenuto del `MasterList` seguente componente `MasterLayout` viene inserito `@Body`nella posizione di:</span><span class="sxs-lookup"><span data-stu-id="f7f7e-127">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="f7f7e-128">Se si specifica il layout direttamente in un componente, `@layout` viene eseguito l'override di un set di layout *predefinito* nel router o di una direttiva importata da *_Imports.razor*.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-128">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="f7f7e-129">Selezione centralizzata del layout</span><span class="sxs-lookup"><span data-stu-id="f7f7e-129">Centralized layout selection</span></span>

<span data-ttu-id="f7f7e-130">Ogni cartella di un'app può contenere facoltativamente un file modello denominato *_Imports.razor*.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-130">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="f7f7e-131">Il compilatore include le direttive specificate nel file imports in tutti i modelli Razor nella stessa cartella e in modo ricorsivo in tutte le relative sottocartelle.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-131">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="f7f7e-132">Pertanto, un file *_Imports.razor* contenente `@layout MyCoolLayout` garantisce che `MyCoolLayout`tutti i componenti in una cartella utilizzino .</span><span class="sxs-lookup"><span data-stu-id="f7f7e-132">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="f7f7e-133">Non è necessario aggiungere `@layout MyCoolLayout` ripetutamente a tutti i file *.razor* all'interno della cartella e delle sottocartelle.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-133">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="f7f7e-134">`@using`direttive vengono applicate anche ai componenti nello stesso modo.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-134">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="f7f7e-135">Le seguenti importazioni di file *_Imports.razor:*</span><span class="sxs-lookup"><span data-stu-id="f7f7e-135">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="f7f7e-136">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-136">`MyCoolLayout`.</span></span>
* <span data-ttu-id="f7f7e-137">Tutti i componenti Razor nella stessa cartella ed eventuali sottocartelle.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-137">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="f7f7e-138">Spazio dei nomi `BlazorApp1.Data`.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-138">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="f7f7e-139">Il file *_Imports.razor* è simile al [file _ViewImports.cshtml per le visualizzazioni e le pagine Razor,](xref:mvc/views/layout#importing-shared-directives) ma applicato in modo specifico ai file dei componenti Razor.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-139">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="f7f7e-140">Se si specifica un layout in *_Imports.razor,* viene eseguito l'override di un layout specificato come *layout predefinito*del router.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-140">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="f7f7e-141">Layout nidificati</span><span class="sxs-lookup"><span data-stu-id="f7f7e-141">Nested layouts</span></span>

<span data-ttu-id="f7f7e-142">Le app possono essere costituite da layout annidati.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-142">Apps can consist of nested layouts.</span></span> <span data-ttu-id="f7f7e-143">Un componente può fare riferimento a un layout che a sua volta fa riferimento a un altro layout.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-143">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="f7f7e-144">Ad esempio, i layout di nidificazione vengono utilizzati per creare una struttura di menu a più livelli.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-144">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="f7f7e-145">Nell'esempio seguente viene illustrato come utilizzare layout annidati.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-145">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="f7f7e-146">Il file *EpisodesComponent.razor* è il componente da visualizzare.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-146">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="f7f7e-147">Il componente `MasterListLayout`fa riferimento a :</span><span class="sxs-lookup"><span data-stu-id="f7f7e-147">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="f7f7e-148">Il file *MasterListLayout.razor* fornisce il `MasterListLayout`file .</span><span class="sxs-lookup"><span data-stu-id="f7f7e-148">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="f7f7e-149">Il layout fa `MasterLayout`riferimento a un altro layout, , dove viene eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-149">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="f7f7e-150">`EpisodesComponent`viene eseguito il rendering dove `@Body` appare:</span><span class="sxs-lookup"><span data-stu-id="f7f7e-150">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="f7f7e-151">Infine, `MasterLayout` in *MasterLayout.razor* contiene gli elementi di layout di primo livello, ad esempio l'intestazione, il menu principale e il piè di pagina.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-151">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="f7f7e-152">`MasterListLayout`con `EpisodesComponent` il rendering `@Body` viene eseguito il punto in cui viene visualizzato:</span><span class="sxs-lookup"><span data-stu-id="f7f7e-152">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="f7f7e-153">Condividere un layout Pagine Razor con componenti integrati</span><span class="sxs-lookup"><span data-stu-id="f7f7e-153">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="f7f7e-154">Quando i componenti instradabili sono integrati in un'app Razor Pages, il layout condiviso dell'app può essere usato con i componenti.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-154">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="f7f7e-155">Per altre informazioni, vedere <xref:blazor/integrate-components>.</span><span class="sxs-lookup"><span data-stu-id="f7f7e-155">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f7f7e-156">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f7f7e-156">Additional resources</span></span>

* <xref:mvc/views/layout>
