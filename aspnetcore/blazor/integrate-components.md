---
<span data-ttu-id="dcd3a-101">title: "integra ASP.NET Core Razor componenti in Razor pagine e app MVC" autore: Descrizione: "informazioni sugli scenari data binding per i componenti e gli elementi DOM nelle Blazor app".</span><span class="sxs-lookup"><span data-stu-id="dcd3a-101">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="dcd3a-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dcd3a-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-103">'Blazor'</span></span>
- <span data-ttu-id="dcd3a-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-104">'Identity'</span></span>
- <span data-ttu-id="dcd3a-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="dcd3a-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-106">'Razor'</span></span>
- <span data-ttu-id="dcd3a-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="dcd3a-107">'SignalR' uid:</span></span> 

---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="dcd3a-108">Integrare ASP.NET Core Razor componenti in Razor pagine e app MVC</span><span class="sxs-lookup"><span data-stu-id="dcd3a-108">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="dcd3a-109">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="dcd3a-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="dcd3a-110">i componenti possono essere integrati in Razor pagine e app MVC.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-110"> components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="dcd3a-111">Quando viene eseguito il rendering della pagina o della vista, è possibile eseguire il rendering dei componenti contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-111">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="dcd3a-112">Dopo aver [preparato l'app](#prepare-the-app), usare le istruzioni riportate nelle sezioni seguenti a seconda dei requisiti dell'app:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-112">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="dcd3a-113">Componenti instradabili: per i componenti che sono direttamente instradabili dalle richieste degli utenti.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-113">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="dcd3a-114">Seguire queste linee guida quando i visitatori devono essere in grado di effettuare una richiesta HTTP nel browser per un componente con una [`@page`](xref:mvc/views/razor#page) direttiva.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-114">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="dcd3a-115">[Usare componenti instradabili in un' Razor app pagine](#use-routable-components-in-a-razor-pages-app)</span><span class="sxs-lookup"><span data-stu-id="dcd3a-115">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * [<span data-ttu-id="dcd3a-116">Usare componenti instradabili in un'app MVC</span><span class="sxs-lookup"><span data-stu-id="dcd3a-116">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="dcd3a-117">[Eseguire il rendering dei componenti da una pagina o vista](#render-components-from-a-page-or-view): per i componenti che non sono direttamente instradabili dalle richieste degli utenti.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-117">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="dcd3a-118">Seguire queste linee guida quando l'app incorpora i componenti in pagine e visualizzazioni esistenti con l' [Helper tag dei componenti](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="dcd3a-118">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="dcd3a-119">Preparare l'app</span><span class="sxs-lookup"><span data-stu-id="dcd3a-119">Prepare the app</span></span>

<span data-ttu-id="dcd3a-120">Un' Razor app MVC o pagine esistente può integrare i Razor componenti in pagine e visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-120">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="dcd3a-121">Nel file di layout dell'app (*_Layout. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="dcd3a-121">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="dcd3a-122">Aggiungere il `<base>` tag seguente all' `<head>` elemento:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-122">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="dcd3a-123">Il `href` valore (il *percorso di base dell'app*) nell'esempio precedente presuppone che l'app si trovi nel percorso URL radice ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="dcd3a-123">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="dcd3a-124">Se l'app è un'applicazione secondaria, seguire le istruzioni nella sezione *percorso di base* dell'applicazione dell' <xref:host-and-deploy/blazor/index#app-base-path> articolo.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-124">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="dcd3a-125">Il file *_Layout. cshtml* si trova nella cartella *pages/Shared* in un' Razor app Pages o in una cartella *condivisa* o in un'app MVC.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-125">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="dcd3a-126">Aggiungere un `<script>` tag per lo script *blazer. Server. js* immediatamente prima del tag di chiusura `</body>` :</span><span class="sxs-lookup"><span data-stu-id="dcd3a-126">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="dcd3a-127">Il Framework aggiunge lo script *blazer. Server. js* all'app.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-127">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="dcd3a-128">Non è necessario aggiungere manualmente lo script all'app.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-128">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="dcd3a-129">Aggiungere un file *_Imports. Razor* alla cartella radice del progetto con il contenuto seguente (modificare l'ultimo spazio dei nomi, `MyAppNamespace` , nello spazio dei nomi dell'app):</span><span class="sxs-lookup"><span data-stu-id="dcd3a-129">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="dcd3a-130">In `Startup.ConfigureServices` registrare il Blazor servizio Server:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-130">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="dcd3a-131">In `Startup.Configure` aggiungere l' Blazor endpoint dell'hub a `app.UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="dcd3a-131">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="dcd3a-132">Integrare i componenti in qualsiasi pagina o visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-132">Integrate components into any page or view.</span></span> <span data-ttu-id="dcd3a-133">Per ulteriori informazioni, vedere la sezione [rendering dei componenti da una pagina o vista](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="dcd3a-133">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="dcd3a-134">Usare componenti instradabili in un' Razor app pagine</span><span class="sxs-lookup"><span data-stu-id="dcd3a-134">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="dcd3a-135">*Questa sezione riguarda l'aggiunta di componenti che sono direttamente instradabili dalle richieste degli utenti.*</span><span class="sxs-lookup"><span data-stu-id="dcd3a-135">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="dcd3a-136">Per supportare componenti instradabili Razor nelle Razor app di pagine:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-136">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="dcd3a-137">Seguire le istruzioni nella sezione [preparare l'app](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="dcd3a-137">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="dcd3a-138">Aggiungere un file *app. Razor* alla radice del progetto con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-138">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="dcd3a-139">Aggiungere un file *_Host. cshtml* alla cartella *pages* con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-139">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="dcd3a-140">I componenti usano il file Shared *_Layout. cshtml* per il layout.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-140">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="dcd3a-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Configura se il `App` componente:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="dcd3a-142">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-142">Is prerendered into the page.</span></span>
   * <span data-ttu-id="dcd3a-143">Viene sottoposto a rendering come HTML statico nella pagina o se include le informazioni necessarie per il bootstrap di un' Blazor app dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-143">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="dcd3a-144">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="dcd3a-144">Render Mode</span></span> | <span data-ttu-id="dcd3a-145">Descrizione</span><span class="sxs-lookup"><span data-stu-id="dcd3a-145">Description</span></span> |
   | ---
<span data-ttu-id="dcd3a-146">title: "integra ASP.NET Core Razor componenti in Razor pagine e app MVC" autore: Descrizione: "informazioni sugli scenari data binding per i componenti e gli elementi DOM nelle Blazor app".</span><span class="sxs-lookup"><span data-stu-id="dcd3a-146">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="dcd3a-147">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dcd3a-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-148">'Blazor'</span></span>
- <span data-ttu-id="dcd3a-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-149">'Identity'</span></span>
- <span data-ttu-id="dcd3a-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="dcd3a-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-151">'Razor'</span></span>
- <span data-ttu-id="dcd3a-152">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="dcd3a-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dcd3a-153">title: "integra ASP.NET Core Razor componenti in Razor pagine e app MVC" autore: Descrizione: "informazioni sugli scenari data binding per i componenti e gli elementi DOM nelle Blazor app".</span><span class="sxs-lookup"><span data-stu-id="dcd3a-153">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="dcd3a-154">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dcd3a-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-155">'Blazor'</span></span>
- <span data-ttu-id="dcd3a-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-156">'Identity'</span></span>
- <span data-ttu-id="dcd3a-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="dcd3a-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-158">'Razor'</span></span>
- <span data-ttu-id="dcd3a-159">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="dcd3a-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dcd3a-160">title: "integra ASP.NET Core Razor componenti in Razor pagine e app MVC" autore: Descrizione: "informazioni sugli scenari data binding per i componenti e gli elementi DOM nelle Blazor app".</span><span class="sxs-lookup"><span data-stu-id="dcd3a-160">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="dcd3a-161">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dcd3a-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-162">'Blazor'</span></span>
- <span data-ttu-id="dcd3a-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-163">'Identity'</span></span>
- <span data-ttu-id="dcd3a-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="dcd3a-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-165">'Razor'</span></span>
- <span data-ttu-id="dcd3a-166">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="dcd3a-166">'SignalR' uid:</span></span> 

<span data-ttu-id="dcd3a-167">------ | Titolo---: "integra ASP.NET Core Razor componenti in Razor pagine e app MVC" autore: Descrizione: "informazioni sugli scenari data binding per i componenti e gli elementi DOM nelle Blazor app".</span><span class="sxs-lookup"><span data-stu-id="dcd3a-167">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="dcd3a-168">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dcd3a-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-169">'Blazor'</span></span>
- <span data-ttu-id="dcd3a-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-170">'Identity'</span></span>
- <span data-ttu-id="dcd3a-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="dcd3a-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-172">'Razor'</span></span>
- <span data-ttu-id="dcd3a-173">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="dcd3a-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dcd3a-174">title: "integra ASP.NET Core Razor componenti in Razor pagine e app MVC" autore: Descrizione: "informazioni sugli scenari data binding per i componenti e gli elementi DOM nelle Blazor app".</span><span class="sxs-lookup"><span data-stu-id="dcd3a-174">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="dcd3a-175">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dcd3a-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-176">'Blazor'</span></span>
- <span data-ttu-id="dcd3a-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-177">'Identity'</span></span>
- <span data-ttu-id="dcd3a-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="dcd3a-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-179">'Razor'</span></span>
- <span data-ttu-id="dcd3a-180">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="dcd3a-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dcd3a-181">title: "integra ASP.NET Core Razor componenti in Razor pagine e app MVC" autore: Descrizione: "informazioni sugli scenari data binding per i componenti e gli elementi DOM nelle Blazor app".</span><span class="sxs-lookup"><span data-stu-id="dcd3a-181">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="dcd3a-182">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dcd3a-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-183">'Blazor'</span></span>
- <span data-ttu-id="dcd3a-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-184">'Identity'</span></span>
- <span data-ttu-id="dcd3a-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="dcd3a-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-186">'Razor'</span></span>
- <span data-ttu-id="dcd3a-187">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="dcd3a-187">'SignalR' uid:</span></span> 

<span data-ttu-id="dcd3a-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Esegue il rendering del `App` componente in HTML statico e include un marcatore per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="dcd3a-189">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-189">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="dcd3a-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Esegue il rendering di un marcatore per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="dcd3a-191">L'output del `App` componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-191">Output from the `App` component isn't included.</span></span> <span data-ttu-id="dcd3a-192">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-192">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="dcd3a-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Esegue il rendering del `App` componente in HTML statico.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="dcd3a-194">Per ulteriori informazioni sull'helper tag dei componenti, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="dcd3a-194">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="dcd3a-195">Aggiungere una route con priorità bassa per la pagina *_Host. cshtml* alla configurazione dell'endpoint in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="dcd3a-195">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="dcd3a-196">Aggiungere componenti instradabili all'app.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-196">Add routable components to the app.</span></span> <span data-ttu-id="dcd3a-197">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-197">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="dcd3a-198">Per ulteriori informazioni sugli spazi dei nomi, vedere la sezione relativa agli [spazi dei nomi dei componenti](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="dcd3a-198">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="dcd3a-199">Usare componenti instradabili in un'app MVC</span><span class="sxs-lookup"><span data-stu-id="dcd3a-199">Use routable components in an MVC app</span></span>

<span data-ttu-id="dcd3a-200">*Questa sezione riguarda l'aggiunta di componenti che sono direttamente instradabili dalle richieste degli utenti.*</span><span class="sxs-lookup"><span data-stu-id="dcd3a-200">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="dcd3a-201">Per supportare componenti instradabili Razor nelle app MVC:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-201">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="dcd3a-202">Seguire le istruzioni nella sezione [preparare l'app](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="dcd3a-202">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="dcd3a-203">Aggiungere un file *app. Razor* alla radice del progetto con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-203">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="dcd3a-204">Aggiungere un file *_Host. cshtml* alla cartella *views/Home* con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-204">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="dcd3a-205">I componenti usano il file Shared *_Layout. cshtml* per il layout.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-205">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="dcd3a-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Configura se il `App` componente:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="dcd3a-207">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-207">Is prerendered into the page.</span></span>
   * <span data-ttu-id="dcd3a-208">Viene sottoposto a rendering come HTML statico nella pagina o se include le informazioni necessarie per il bootstrap di un' Blazor app dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-208">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="dcd3a-209">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="dcd3a-209">Render Mode</span></span> | <span data-ttu-id="dcd3a-210">Descrizione</span><span class="sxs-lookup"><span data-stu-id="dcd3a-210">Description</span></span> |
   | ---
<span data-ttu-id="dcd3a-211">title: "integra ASP.NET Core Razor componenti in Razor pagine e app MVC" autore: Descrizione: "informazioni sugli scenari data binding per i componenti e gli elementi DOM nelle Blazor app".</span><span class="sxs-lookup"><span data-stu-id="dcd3a-211">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="dcd3a-212">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dcd3a-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-213">'Blazor'</span></span>
- <span data-ttu-id="dcd3a-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-214">'Identity'</span></span>
- <span data-ttu-id="dcd3a-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="dcd3a-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-216">'Razor'</span></span>
- <span data-ttu-id="dcd3a-217">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="dcd3a-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dcd3a-218">title: "integra ASP.NET Core Razor componenti in Razor pagine e app MVC" autore: Descrizione: "informazioni sugli scenari data binding per i componenti e gli elementi DOM nelle Blazor app".</span><span class="sxs-lookup"><span data-stu-id="dcd3a-218">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="dcd3a-219">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dcd3a-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-220">'Blazor'</span></span>
- <span data-ttu-id="dcd3a-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-221">'Identity'</span></span>
- <span data-ttu-id="dcd3a-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="dcd3a-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-223">'Razor'</span></span>
- <span data-ttu-id="dcd3a-224">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="dcd3a-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dcd3a-225">title: "integra ASP.NET Core Razor componenti in Razor pagine e app MVC" autore: Descrizione: "informazioni sugli scenari data binding per i componenti e gli elementi DOM nelle Blazor app".</span><span class="sxs-lookup"><span data-stu-id="dcd3a-225">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="dcd3a-226">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dcd3a-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-227">'Blazor'</span></span>
- <span data-ttu-id="dcd3a-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-228">'Identity'</span></span>
- <span data-ttu-id="dcd3a-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="dcd3a-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-230">'Razor'</span></span>
- <span data-ttu-id="dcd3a-231">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="dcd3a-231">'SignalR' uid:</span></span> 

<span data-ttu-id="dcd3a-232">------ | Titolo---: "integra ASP.NET Core Razor componenti in Razor pagine e app MVC" autore: Descrizione: "informazioni sugli scenari data binding per i componenti e gli elementi DOM nelle Blazor app".</span><span class="sxs-lookup"><span data-stu-id="dcd3a-232">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="dcd3a-233">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dcd3a-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-234">'Blazor'</span></span>
- <span data-ttu-id="dcd3a-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-235">'Identity'</span></span>
- <span data-ttu-id="dcd3a-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="dcd3a-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-237">'Razor'</span></span>
- <span data-ttu-id="dcd3a-238">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="dcd3a-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dcd3a-239">title: "integra ASP.NET Core Razor componenti in Razor pagine e app MVC" autore: Descrizione: "informazioni sugli scenari data binding per i componenti e gli elementi DOM nelle Blazor app".</span><span class="sxs-lookup"><span data-stu-id="dcd3a-239">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="dcd3a-240">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dcd3a-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-241">'Blazor'</span></span>
- <span data-ttu-id="dcd3a-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-242">'Identity'</span></span>
- <span data-ttu-id="dcd3a-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="dcd3a-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-244">'Razor'</span></span>
- <span data-ttu-id="dcd3a-245">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="dcd3a-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dcd3a-246">title: "integra ASP.NET Core Razor componenti in Razor pagine e app MVC" autore: Descrizione: "informazioni sugli scenari data binding per i componenti e gli elementi DOM nelle Blazor app".</span><span class="sxs-lookup"><span data-stu-id="dcd3a-246">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="dcd3a-247">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dcd3a-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-248">'Blazor'</span></span>
- <span data-ttu-id="dcd3a-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-249">'Identity'</span></span>
- <span data-ttu-id="dcd3a-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="dcd3a-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dcd3a-251">'Razor'</span></span>
- <span data-ttu-id="dcd3a-252">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="dcd3a-252">'SignalR' uid:</span></span> 

<span data-ttu-id="dcd3a-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Esegue il rendering del `App` componente in HTML statico e include un marcatore per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="dcd3a-254">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-254">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="dcd3a-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Esegue il rendering di un marcatore per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="dcd3a-256">L'output del `App` componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-256">Output from the `App` component isn't included.</span></span> <span data-ttu-id="dcd3a-257">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-257">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="dcd3a-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Esegue il rendering del `App` componente in HTML statico.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="dcd3a-259">Per ulteriori informazioni sull'helper tag dei componenti, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="dcd3a-259">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="dcd3a-260">Aggiungere un'azione al controller Home:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-260">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="dcd3a-261">Aggiungere una route con priorità bassa per l'azione del controller che restituisce la vista *_Host. cshtml* alla configurazione dell'endpoint in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="dcd3a-261">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="dcd3a-262">Creare una cartella *pages* e aggiungere componenti instradabili all'app.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-262">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="dcd3a-263">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-263">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="dcd3a-264">Per ulteriori informazioni sugli spazi dei nomi, vedere la sezione relativa agli [spazi dei nomi dei componenti](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="dcd3a-264">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="dcd3a-265">Eseguire il rendering dei componenti da una pagina o da una vista</span><span class="sxs-lookup"><span data-stu-id="dcd3a-265">Render components from a page or view</span></span>

<span data-ttu-id="dcd3a-266">*Questa sezione riguarda l'aggiunta di componenti a pagine o viste, in cui i componenti non sono direttamente instradabili dalle richieste degli utenti.*</span><span class="sxs-lookup"><span data-stu-id="dcd3a-266">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="dcd3a-267">Per eseguire il rendering di un componente da una pagina o da una vista, usare l' [Helper Tag Component](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="dcd3a-267">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="dcd3a-268">Eseguire il rendering di componenti interattivi con stato</span><span class="sxs-lookup"><span data-stu-id="dcd3a-268">Render stateful interactive components</span></span>

<span data-ttu-id="dcd3a-269">I componenti interattivi con stato possono essere aggiunti a una Razor pagina o a una vista.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-269">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="dcd3a-270">Quando viene eseguito il rendering della pagina o della visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-270">When the page or view renders:</span></span>

* <span data-ttu-id="dcd3a-271">Il componente viene preeseguito con la pagina o la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-271">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="dcd3a-272">Lo stato iniziale del componente utilizzato per il prerendering viene perso.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-272">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="dcd3a-273">Quando viene stabilita la connessione, viene creato il nuovo stato del componente SignalR .</span><span class="sxs-lookup"><span data-stu-id="dcd3a-273">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="dcd3a-274">Nella pagina seguente viene Razor eseguito il rendering di un `Counter` componente:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-274">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="dcd3a-275">Per altre informazioni, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-275">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="dcd3a-276">Eseguire il rendering di componenti non interattivi</span><span class="sxs-lookup"><span data-stu-id="dcd3a-276">Render noninteractive components</span></span>

<span data-ttu-id="dcd3a-277">Nella pagina seguente Razor il componente viene sottoposto a `Counter` rendering statico con un valore iniziale specificato utilizzando un form.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-277">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="dcd3a-278">Poiché il componente viene sottoposto a rendering statico, il componente non è interattivo:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-278">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="dcd3a-279">Per altre informazioni, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-279">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="dcd3a-280">Spazi dei nomi dei componenti</span><span class="sxs-lookup"><span data-stu-id="dcd3a-280">Component namespaces</span></span>

<span data-ttu-id="dcd3a-281">Quando si usa una cartella personalizzata per archiviare i componenti dell'app, aggiungere lo spazio dei nomi che rappresenta la cartella alla pagina/visualizzazione o al file *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="dcd3a-281">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="dcd3a-282">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="dcd3a-282">In the following example:</span></span>

* <span data-ttu-id="dcd3a-283">Passare `MyAppNamespace` allo spazio dei nomi dell'app.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-283">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="dcd3a-284">Se una cartella denominata *Components* non viene utilizzata per conservare i componenti, passare `Components` alla cartella in cui si trovano i componenti.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-284">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="dcd3a-285">Il file *_ViewImports. cshtml* si trova nella cartella *pagine* di un' Razor app pagine o nella cartella *views* di un'app MVC.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-285">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="dcd3a-286">Per altre informazioni, vedere <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="dcd3a-286">For more information, see <xref:blazor/components#import-components>.</span></span>
