---
title: 'Integrare ASP.NET Core Razor componenti in Razor pagine e app MVC'
author: guardrex
description: 'Informazioni sugli scenari di data binding per i componenti e gli elementi DOM nelle Blazor app.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/components/integrate-components-into-razor-pages-and-mvc-apps
ms.openlocfilehash: e56a08be082cef4ba3a0a58fdfa9d3800d244f75
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056257"
---
# <a name="integrate-aspnet-core-no-locrazor-components-into-no-locrazor-pages-and-mvc-apps"></a><span data-ttu-id="2e1c1-103">Integrare ASP.NET Core Razor componenti in Razor pagine e app MVC</span><span class="sxs-lookup"><span data-stu-id="2e1c1-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="2e1c1-104">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2e1c1-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="2e1c1-105">Razor i componenti possono essere integrati in Razor pagine e app MVC.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="2e1c1-106">Quando viene eseguito il rendering della pagina o della vista, è possibile eseguire il rendering dei componenti contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="2e1c1-107">Dopo aver [preparato l'app](#prepare-the-app), usare le istruzioni riportate nelle sezioni seguenti a seconda dei requisiti dell'app:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="2e1c1-108">Componenti instradabili: per i componenti che sono direttamente instradabili dalle richieste degli utenti.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-108">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="2e1c1-109">Seguire queste linee guida quando i visitatori devono essere in grado di effettuare una richiesta HTTP nel browser per un componente con una [`@page`](xref:mvc/views/razor#page) direttiva.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="2e1c1-110">Usare componenti instradabili in un' Razor app pagine</span><span class="sxs-lookup"><span data-stu-id="2e1c1-110">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="2e1c1-111">Usare componenti instradabili in un'app MVC</span><span class="sxs-lookup"><span data-stu-id="2e1c1-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="2e1c1-112">[Eseguire il rendering dei componenti da una pagina o vista](#render-components-from-a-page-or-view): per i componenti che non sono direttamente instradabili dalle richieste degli utenti.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-112">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="2e1c1-113">Seguire queste linee guida quando l'app incorpora i componenti in pagine e visualizzazioni esistenti con l' [Helper tag dei componenti](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="2e1c1-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="2e1c1-114">Preparare l'app</span><span class="sxs-lookup"><span data-stu-id="2e1c1-114">Prepare the app</span></span>

<span data-ttu-id="2e1c1-115">Un' Razor app MVC o pagine esistente può integrare i Razor componenti in pagine e visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-115">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="2e1c1-116">Nel file di layout dell'app ( `_Layout.cshtml` ):</span><span class="sxs-lookup"><span data-stu-id="2e1c1-116">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="2e1c1-117">Aggiungere il `<base>` tag seguente all' `<head>` elemento:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="2e1c1-118">Il `href` valore (il *percorso di base dell'app* ) nell'esempio precedente presuppone che l'app si trovi nel percorso URL radice ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="2e1c1-118">The `href` value (the *app base path* ) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="2e1c1-119">Se l'app è un'applicazione secondaria, seguire le istruzioni nella sezione *percorso di base* dell'applicazione dell' <xref:blazor/host-and-deploy/index#app-base-path> articolo.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="2e1c1-120">Il `_Layout.cshtml` file si trova nella cartella *pages/Shared* in un' Razor app Pages o in una cartella *condivisa* o in un'app MVC.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-120">The `_Layout.cshtml` file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="2e1c1-121">Aggiungere un `<script>` tag per lo script *blazor.server.js* immediatamente prima del tag di chiusura `</body>` :</span><span class="sxs-lookup"><span data-stu-id="2e1c1-121">Add a `<script>` tag for the *blazor.server.js* script immediately before the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="2e1c1-122">Il Framework aggiunge lo script *blazor.server.js* all'app.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="2e1c1-123">Non è necessario aggiungere manualmente lo script all'app.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="2e1c1-124">Aggiungere un `_Imports.razor` file alla cartella radice del progetto con il contenuto seguente (modificare l'ultimo spazio dei nomi, `MyAppNamespace` , nello spazio dei nomi dell'app):</span><span class="sxs-lookup"><span data-stu-id="2e1c1-124">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="2e1c1-125">In `Startup.ConfigureServices` registrare il Blazor Server servizio:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-125">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="2e1c1-126">In `Startup.Configure` aggiungere l' Blazor endpoint dell'hub a `app.UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="2e1c1-126">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="2e1c1-127">Integrare i componenti in qualsiasi pagina o visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-127">Integrate components into any page or view.</span></span> <span data-ttu-id="2e1c1-128">Per ulteriori informazioni, vedere la sezione [rendering dei componenti da una pagina o vista](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="2e1c1-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a><span data-ttu-id="2e1c1-129">Usare componenti instradabili in un' Razor app pagine</span><span class="sxs-lookup"><span data-stu-id="2e1c1-129">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="2e1c1-130">*Questa sezione riguarda l'aggiunta di componenti che sono direttamente instradabili dalle richieste degli utenti.*</span><span class="sxs-lookup"><span data-stu-id="2e1c1-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="2e1c1-131">Per supportare componenti instradabili Razor nelle Razor app di pagine:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-131">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="2e1c1-132">Seguire le istruzioni nella sezione [preparare l'app](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="2e1c1-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="2e1c1-133">Aggiungere un `App.razor` file alla radice del progetto con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-133">Add an `App.razor` file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="2e1c1-134">Aggiungere un `_Host.cshtml` file alla `Pages` cartella con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-134">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="2e1c1-135">I componenti utilizzano il `_Layout.cshtml` file condiviso per il layout.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-135">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="2e1c1-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Configura se il `App` componente:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="2e1c1-137">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="2e1c1-138">Viene sottoposto a rendering come HTML statico nella pagina o se include le informazioni necessarie per il bootstrap di un' Blazor app dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="2e1c1-139">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="2e1c1-139">Render Mode</span></span> | <span data-ttu-id="2e1c1-140">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2e1c1-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="2e1c1-141">Esegue il rendering del `App` componente in HTML statico e include un marcatore per un' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-141">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="2e1c1-142">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-142">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="2e1c1-143">Esegue il rendering di un marcatore per un' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-143">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="2e1c1-144">L'output del `App` componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="2e1c1-145">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-145">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="2e1c1-146">Esegue il rendering del `App` componente in HTML statico.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="2e1c1-147">Per ulteriori informazioni sull'helper tag dei componenti, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="2e1c1-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="2e1c1-148">Aggiungere una route con priorità bassa per la `_Host.cshtml` pagina alla configurazione dell'endpoint in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="2e1c1-148">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="2e1c1-149">Aggiungere componenti instradabili all'app.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-149">Add routable components to the app.</span></span> <span data-ttu-id="2e1c1-150">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="2e1c1-151">Per ulteriori informazioni sugli spazi dei nomi, vedere la sezione relativa agli [spazi dei nomi dei componenti](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="2e1c1-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="2e1c1-152">Usare componenti instradabili in un'app MVC</span><span class="sxs-lookup"><span data-stu-id="2e1c1-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="2e1c1-153">*Questa sezione riguarda l'aggiunta di componenti che sono direttamente instradabili dalle richieste degli utenti.*</span><span class="sxs-lookup"><span data-stu-id="2e1c1-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="2e1c1-154">Per supportare componenti instradabili Razor nelle app MVC:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-154">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="2e1c1-155">Seguire le istruzioni nella sezione [preparare l'app](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="2e1c1-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="2e1c1-156">Aggiungere un `App.razor` file alla radice del progetto con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-156">Add an `App.razor` file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="2e1c1-157">Aggiungere un `_Host.cshtml` file alla `Views/Home` cartella con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-157">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="2e1c1-158">I componenti utilizzano il `_Layout.cshtml` file condiviso per il layout.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-158">Components use the shared `_Layout.cshtml` file for their layout.</span></span>
   
   <span data-ttu-id="2e1c1-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Configura se il `App` componente:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="2e1c1-160">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="2e1c1-161">Viene sottoposto a rendering come HTML statico nella pagina o se include le informazioni necessarie per il bootstrap di un' Blazor app dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="2e1c1-162">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="2e1c1-162">Render Mode</span></span> | <span data-ttu-id="2e1c1-163">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2e1c1-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="2e1c1-164">Esegue il rendering del `App` componente in HTML statico e include un marcatore per un' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-164">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="2e1c1-165">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="2e1c1-166">Esegue il rendering di un marcatore per un' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="2e1c1-167">L'output del `App` componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="2e1c1-168">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="2e1c1-169">Esegue il rendering del `App` componente in HTML statico.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="2e1c1-170">Per ulteriori informazioni sull'helper tag dei componenti, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="2e1c1-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="2e1c1-171">Aggiungere un'azione al controller Home:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="2e1c1-172">Aggiungere una route con priorità bassa per l'azione del controller che restituisce la `_Host.cshtml` visualizzazione alla configurazione dell'endpoint in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="2e1c1-172">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="2e1c1-173">Creare una `Pages` cartella e aggiungere componenti instradabili all'app.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-173">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="2e1c1-174">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="2e1c1-175">Per ulteriori informazioni sugli spazi dei nomi, vedere la sezione relativa agli [spazi dei nomi dei componenti](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="2e1c1-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="2e1c1-176">Eseguire il rendering dei componenti da una pagina o da una vista</span><span class="sxs-lookup"><span data-stu-id="2e1c1-176">Render components from a page or view</span></span>

<span data-ttu-id="2e1c1-177">*Questa sezione riguarda l'aggiunta di componenti a pagine o viste, in cui i componenti non sono direttamente instradabili dalle richieste degli utenti.*</span><span class="sxs-lookup"><span data-stu-id="2e1c1-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="2e1c1-178">Per eseguire il rendering di un componente da una pagina o da una vista, usare l' [Helper Tag Component](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="2e1c1-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="2e1c1-179">Eseguire il rendering di componenti interattivi con stato</span><span class="sxs-lookup"><span data-stu-id="2e1c1-179">Render stateful interactive components</span></span>

<span data-ttu-id="2e1c1-180">I componenti interattivi con stato possono essere aggiunti a una Razor pagina o a una vista.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-180">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="2e1c1-181">Quando viene eseguito il rendering della pagina o della visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-181">When the page or view renders:</span></span>

* <span data-ttu-id="2e1c1-182">Il componente viene preeseguito con la pagina o la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-182">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="2e1c1-183">Lo stato iniziale del componente utilizzato per il prerendering viene perso.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-183">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="2e1c1-184">Quando viene stabilita la connessione, viene creato il nuovo stato del componente SignalR .</span><span class="sxs-lookup"><span data-stu-id="2e1c1-184">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="2e1c1-185">Nella pagina seguente viene Razor eseguito il rendering di un `Counter` componente:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-185">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="2e1c1-186">Per altre informazioni, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-186">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="2e1c1-187">Eseguire il rendering di componenti non interattivi</span><span class="sxs-lookup"><span data-stu-id="2e1c1-187">Render noninteractive components</span></span>

<span data-ttu-id="2e1c1-188">Nella pagina seguente Razor il componente viene sottoposto a `Counter` rendering statico con un valore iniziale specificato utilizzando un form.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-188">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="2e1c1-189">Poiché il componente viene sottoposto a rendering statico, il componente non è interattivo:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-189">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="2e1c1-190">Per altre informazioni, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-190">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="2e1c1-191">Spazi dei nomi dei componenti</span><span class="sxs-lookup"><span data-stu-id="2e1c1-191">Component namespaces</span></span>

<span data-ttu-id="2e1c1-192">Quando si usa una cartella personalizzata per conservare i componenti dell'app, aggiungere lo spazio dei nomi che rappresenta la cartella alla pagina/visualizzazione o al `_ViewImports.cshtml` file.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-192">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="2e1c1-193">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="2e1c1-193">In the following example:</span></span>

* <span data-ttu-id="2e1c1-194">Passare `MyAppNamespace` allo spazio dei nomi dell'app.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-194">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="2e1c1-195">Se una cartella denominata *Components* non viene utilizzata per conservare i componenti, passare `Components` alla cartella in cui si trovano i componenti.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-195">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="2e1c1-196">Il `_ViewImports.cshtml` file si trova nella `Pages` cartella dell' Razor app pagine o nella `Views` cartella di un'app MVC.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-196">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="2e1c1-197">Per altre informazioni, vedere <xref:blazor/components/index#namespaces>.</span><span class="sxs-lookup"><span data-stu-id="2e1c1-197">For more information, see <xref:blazor/components/index#namespaces>.</span></span>
