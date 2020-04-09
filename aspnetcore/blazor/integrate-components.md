---
title: Integra ASP.NET componenti di base di Razor in pagine Razor e app MVC
author: guardrex
description: Informazioni sugli scenari di associazione Blazor dati per i componenti e gli elementi DOM nelle app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: cf6056e0985d5433bddecac8dd183ca3f4c2af5b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218934"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="dd3da-103">Integra ASP.NET componenti di base di Razor in pagine Razor e app MVC</span><span class="sxs-lookup"><span data-stu-id="dd3da-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="dd3da-104">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="dd3da-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="dd3da-105">I componenti Razor possono essere integrati nelle pagine Razor e nelle app MVC.</span><span class="sxs-lookup"><span data-stu-id="dd3da-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="dd3da-106">Quando viene eseguito il rendering della pagina o della vista, è possibile eseguire il prerendering dei componenti contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="dd3da-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a><span data-ttu-id="dd3da-107">Preparare l'app per l'uso dei componenti in pagine e visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="dd3da-107">Prepare the app to use components in pages and views</span></span>

<span data-ttu-id="dd3da-108">Un'app Razor Pages o MVC esistente può integrare i componenti Razor nelle pagine e nelle visualizzazioni:An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span><span class="sxs-lookup"><span data-stu-id="dd3da-108">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="dd3da-109">Nel file di layout dell'app (*_Layout.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="dd3da-109">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="dd3da-110">Aggiungere il `<base>` seguente `<head>` tag all'elemento:</span><span class="sxs-lookup"><span data-stu-id="dd3da-110">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="dd3da-111">Il `href` valore *(percorso di base dell'app*) nell'esempio precedente presuppone che`/`l'app si trovi nel percorso dell'URL radice ( ).</span><span class="sxs-lookup"><span data-stu-id="dd3da-111">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="dd3da-112">Se l'app è un'applicazione secondaria, seguire le indicazioni <xref:host-and-deploy/blazor/index#app-base-path> nella sezione Percorso di *base* dell'app dell'articolo.</span><span class="sxs-lookup"><span data-stu-id="dd3da-112">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="dd3da-113">Il file *_Layout.cshtml* si trova nella cartella *Pages/Shared* in un'app Razor Pages o nella cartella *Views/Shared* in un'app MVC.</span><span class="sxs-lookup"><span data-stu-id="dd3da-113">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="dd3da-114">Aggiungere `<script>` un tag per lo script *blazor.server.js* immediatamente prima del tag di chiusura: `</body>`</span><span class="sxs-lookup"><span data-stu-id="dd3da-114">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="dd3da-115">Il framework aggiunge lo script *blazor.server.js* all'app.</span><span class="sxs-lookup"><span data-stu-id="dd3da-115">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="dd3da-116">Non è necessario aggiungere manualmente lo script all'app.</span><span class="sxs-lookup"><span data-stu-id="dd3da-116">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="dd3da-117">Aggiungere un file *_Imports.razor* alla cartella radice del progetto con il `MyAppNamespace`contenuto seguente (modificare l'ultimo spazio dei nomi, , , nello spazio dei nomi dell'app):</span><span class="sxs-lookup"><span data-stu-id="dd3da-117">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="dd3da-118">In `Startup.ConfigureServices`, Blazor registrare il servizio Server:</span><span class="sxs-lookup"><span data-stu-id="dd3da-118">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="dd3da-119">In `Startup.Configure`, Blazor aggiungere l'endpoint Hub a `app.UseEndpoints`:</span><span class="sxs-lookup"><span data-stu-id="dd3da-119">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="dd3da-120">Integrare i componenti in qualsiasi pagina o vista.</span><span class="sxs-lookup"><span data-stu-id="dd3da-120">Integrate components into any page or view.</span></span> <span data-ttu-id="dd3da-121">Per ulteriori informazioni, vedere la sezione [Rendering di componenti da una pagina o vista.](#render-components-from-a-page-or-view)</span><span class="sxs-lookup"><span data-stu-id="dd3da-121">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="dd3da-122">Usare componenti instradabili in un'app Razor Pages</span><span class="sxs-lookup"><span data-stu-id="dd3da-122">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="dd3da-123">*Questa sezione riguarda l'aggiunta di componenti instradabili direttamente dalle richieste dell'utente.*</span><span class="sxs-lookup"><span data-stu-id="dd3da-123">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="dd3da-124">Per supportare i componenti Razor instradabili nelle app Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="dd3da-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="dd3da-125">Seguire le istruzioni nella sezione [Preparare l'app per l'uso dei componenti nelle pagine e nelle visualizzazioni.](#prepare-the-app-to-use-components-in-pages-and-views)</span><span class="sxs-lookup"><span data-stu-id="dd3da-125">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="dd3da-126">Aggiungere un file App.razor alla radice del progetto con il contenuto seguente:Add an *App.razor* file to the project root with the following content:</span><span class="sxs-lookup"><span data-stu-id="dd3da-126">Add an *App.razor* file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="dd3da-127">Aggiungere un file *_Host.cshtml* alla cartella *Pages* con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="dd3da-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="dd3da-128">I componenti utilizzano il file *condiviso _Layout.cshtml* per il layout.</span><span class="sxs-lookup"><span data-stu-id="dd3da-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="dd3da-129">Aggiungere una route con priorità bassa per la pagina `Startup.Configure` *_Host.cshtml* alla configurazione dell'endpoint in:</span><span class="sxs-lookup"><span data-stu-id="dd3da-129">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="dd3da-130">Aggiungere componenti instradabili all'app.</span><span class="sxs-lookup"><span data-stu-id="dd3da-130">Add routable components to the app.</span></span> <span data-ttu-id="dd3da-131">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="dd3da-131">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="dd3da-132">Per altre informazioni sugli spazi dei nomi, vedere la sezione [Spazi dei nomi dei](#component-namespaces) componenti.</span><span class="sxs-lookup"><span data-stu-id="dd3da-132">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="dd3da-133">Usare componenti instradabili in un'app MVCUse routable components in an MVC app</span><span class="sxs-lookup"><span data-stu-id="dd3da-133">Use routable components in an MVC app</span></span>

<span data-ttu-id="dd3da-134">*Questa sezione riguarda l'aggiunta di componenti instradabili direttamente dalle richieste dell'utente.*</span><span class="sxs-lookup"><span data-stu-id="dd3da-134">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="dd3da-135">Per supportare i componenti Razor instradabili nelle app MVC:</span><span class="sxs-lookup"><span data-stu-id="dd3da-135">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="dd3da-136">Seguire le istruzioni nella sezione [Preparare l'app per l'uso dei componenti nelle pagine e nelle visualizzazioni.](#prepare-the-app-to-use-components-in-pages-and-views)</span><span class="sxs-lookup"><span data-stu-id="dd3da-136">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="dd3da-137">Aggiungere un file *App.razor* alla radice del progetto con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="dd3da-137">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="dd3da-138">Aggiungere un file *_Host.cshtml* alla cartella *Views/Home* con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="dd3da-138">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="dd3da-139">I componenti utilizzano il file *condiviso _Layout.cshtml* per il layout.</span><span class="sxs-lookup"><span data-stu-id="dd3da-139">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="dd3da-140">Aggiungere un'azione al controller Home:Add an action to the Home controller:</span><span class="sxs-lookup"><span data-stu-id="dd3da-140">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="dd3da-141">Aggiungere una route con priorità bassa per l'azione del controller che `Startup.Configure`restituisce la visualizzazione *_Host.cshtml* alla configurazione dell'endpoint in:</span><span class="sxs-lookup"><span data-stu-id="dd3da-141">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="dd3da-142">Creare una cartella *Pages* e aggiungere componenti instradabili all'app.</span><span class="sxs-lookup"><span data-stu-id="dd3da-142">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="dd3da-143">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="dd3da-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="dd3da-144">Per altre informazioni sugli spazi dei nomi, vedere la sezione [Spazi dei nomi dei](#component-namespaces) componenti.</span><span class="sxs-lookup"><span data-stu-id="dd3da-144">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="dd3da-145">Spazi dei nomi dei componentiComponent namespaces</span><span class="sxs-lookup"><span data-stu-id="dd3da-145">Component namespaces</span></span>

<span data-ttu-id="dd3da-146">Quando usi una cartella personalizzata per contenere i componenti dell'app, aggiungi lo spazio dei nomi che rappresenta la cartella alla pagina/visualizzazione o al file *_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="dd3da-146">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="dd3da-147">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="dd3da-147">In the following example:</span></span>

* <span data-ttu-id="dd3da-148">Passare `MyAppNamespace` allo spazio dei nomi dell'app.</span><span class="sxs-lookup"><span data-stu-id="dd3da-148">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="dd3da-149">Se una cartella denominata *Componenti* non viene `Components` utilizzata per contenere i componenti, passare alla cartella in cui risiedono i componenti.</span><span class="sxs-lookup"><span data-stu-id="dd3da-149">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="dd3da-150">Il file *_ViewImports.cshtml* si trova nella cartella *Pages* di un'app Razor Pages o nella cartella *Views* di un'app MVC.</span><span class="sxs-lookup"><span data-stu-id="dd3da-150">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="dd3da-151">Per altre informazioni, vedere <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="dd3da-151">For more information, see <xref:blazor/components#import-components>.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="dd3da-152">Rendering di componenti da una pagina o una visualizzazione</span><span class="sxs-lookup"><span data-stu-id="dd3da-152">Render components from a page or view</span></span>

<span data-ttu-id="dd3da-153">*Questa sezione riguarda l'aggiunta di componenti a pagine o viste, in cui i componenti non sono instradabili direttamente dalle richieste degli utenti.*</span><span class="sxs-lookup"><span data-stu-id="dd3da-153">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="dd3da-154">Per eseguire il rendering di un componente da una pagina o da una vista, utilizzate [l'helper tag componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="dd3da-154">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

<span data-ttu-id="dd3da-155">Per altre informazioni su come viene eseguito `Component` il rendering dei componenti, sullo stato dei componenti e su Tag Helper, vedere gli articoli seguenti:For more information on how components are rendered, component state, and the Tag Helper, see the following articles:</span><span class="sxs-lookup"><span data-stu-id="dd3da-155">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
