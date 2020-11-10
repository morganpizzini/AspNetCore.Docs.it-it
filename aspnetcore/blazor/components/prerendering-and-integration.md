---
title: 'Pre-rendering e integrazione dei :::no-loc(Razor)::: componenti ASP.NET Core'
author: guardrex
description: 'Informazioni sugli :::no-loc(Razor)::: scenari di integrazione dei componenti per :::no-loc(Blazor)::: le app, incluso il prerendering dei :::no-loc(Razor)::: componenti nel server.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: affca6c9b585b91787f94a13144d07bedfefdd37
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431705"
---
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a><span data-ttu-id="b8f20-103">Pre-rendering e integrazione dei :::no-loc(Razor)::: componenti ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b8f20-103">Prerender and integrate ASP.NET Core :::no-loc(Razor)::: components</span></span>

<span data-ttu-id="b8f20-104">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b8f20-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b8f20-105">:::no-loc(Razor)::: i componenti possono essere integrati in :::no-loc(Razor)::: pagine e app MVC in una :::no-loc(Blazor WebAssembly)::: soluzione ospitata.</span><span class="sxs-lookup"><span data-stu-id="b8f20-105">:::no-loc(Razor)::: components can be integrated into :::no-loc(Razor)::: Pages and MVC apps in a hosted :::no-loc(Blazor WebAssembly)::: solution.</span></span> <span data-ttu-id="b8f20-106">Quando viene eseguito il rendering della pagina o della vista, è possibile eseguire il rendering dei componenti contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="b8f20-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="configuration"></a><span data-ttu-id="b8f20-107">Configurazione</span><span class="sxs-lookup"><span data-stu-id="b8f20-107">Configuration</span></span>

<span data-ttu-id="b8f20-108">Per configurare il prerendering per un' :::no-loc(Blazor WebAssembly)::: app:</span><span class="sxs-lookup"><span data-stu-id="b8f20-108">To set up prerendering for a :::no-loc(Blazor WebAssembly)::: app:</span></span>

1. <span data-ttu-id="b8f20-109">Ospitare l' :::no-loc(Blazor WebAssembly)::: app in un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b8f20-109">Host the :::no-loc(Blazor WebAssembly)::: app in an ASP.NET Core app.</span></span> <span data-ttu-id="b8f20-110">:::no-loc(Blazor WebAssembly):::È possibile aggiungere un'app autonoma a una soluzione ASP.NET Core oppure è possibile usare un'app ospitata :::no-loc(Blazor WebAssembly)::: creata dal :::no-loc(Blazor)::: modello di progetto ospitato.</span><span class="sxs-lookup"><span data-stu-id="b8f20-110">A standalone :::no-loc(Blazor WebAssembly)::: app can be added to an ASP.NET Core solution, or you can use a hosted :::no-loc(Blazor WebAssembly)::: app created from the :::no-loc(Blazor)::: Hosted project template.</span></span>

1. <span data-ttu-id="b8f20-111">Rimuovere il file statico predefinito `wwwroot/index.html` dal :::no-loc(Blazor WebAssembly)::: progetto client.</span><span class="sxs-lookup"><span data-stu-id="b8f20-111">Remove the default static `wwwroot/index.html` file from the :::no-loc(Blazor WebAssembly)::: client project.</span></span>

1. <span data-ttu-id="b8f20-112">Eliminare la riga seguente in `Program.Main` nel progetto client:</span><span class="sxs-lookup"><span data-stu-id="b8f20-112">Delete the following line in `Program.Main` in the client project:</span></span>

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. <span data-ttu-id="b8f20-113">Aggiungere un `Pages/_Host.cshtml` file al progetto server.</span><span class="sxs-lookup"><span data-stu-id="b8f20-113">Add a `Pages/_Host.cshtml` file to the server project.</span></span> <span data-ttu-id="b8f20-114">È possibile ottenere un `_Host.cshtml` file da un'app creata dal :::no-loc(Blazor Server)::: modello con il `dotnet new blazorserver -o :::no-loc(Blazor):::Server` comando in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="b8f20-114">You can obtain a `_Host.cshtml` file from an app created from the :::no-loc(Blazor Server)::: template with the `dotnet new blazorserver -o :::no-loc(Blazor):::Server` command in a command shell.</span></span> <span data-ttu-id="b8f20-115">Dopo aver posizionato il `Pages/_Host.cshtml` file nell'app server della soluzione ospitata :::no-loc(Blazor WebAssembly)::: , apportare le modifiche seguenti al file:</span><span class="sxs-lookup"><span data-stu-id="b8f20-115">After placing the `Pages/_Host.cshtml` file into the server app of the hosted :::no-loc(Blazor WebAssembly)::: solution, make the following changes to the file:</span></span>

   * <span data-ttu-id="b8f20-116">Impostare lo spazio dei nomi sulla cartella dell'app Server `Pages` (ad esempio, `@namespace :::no-loc(Blazor):::Hosted.Server.Pages` ).</span><span class="sxs-lookup"><span data-stu-id="b8f20-116">Set the namespace to the server app's `Pages` folder (for example, `@namespace :::no-loc(Blazor):::Hosted.Server.Pages`).</span></span>
   * <span data-ttu-id="b8f20-117">Impostare una [`@using`](xref:mvc/views/razor#using) direttiva per il progetto client (ad esempio, `@using :::no-loc(Blazor):::Hosted.Client` ).</span><span class="sxs-lookup"><span data-stu-id="b8f20-117">Set an [`@using`](xref:mvc/views/razor#using) directive for the client project (for example, `@using :::no-loc(Blazor):::Hosted.Client`).</span></span>
   * <span data-ttu-id="b8f20-118">Aggiornare i collegamenti del foglio di stile in modo che puntino al foglio di stile dell'app webassembly.</span><span class="sxs-lookup"><span data-stu-id="b8f20-118">Update the stylesheet links to point to the WebAssembly app's stylesheet.</span></span> <span data-ttu-id="b8f20-119">Nell'esempio seguente lo spazio dei nomi dell'app client è `:::no-loc(Blazor):::Hosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="b8f20-119">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href=":::no-loc(Blazor):::Hosted.Client.styles.css" rel="stylesheet" />
     ```

   * <span data-ttu-id="b8f20-120">Aggiornare l'oggetto `render-mode` dell' [Helper Tag componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) per eseguire il prerendering del `App` componente radice:</span><span class="sxs-lookup"><span data-stu-id="b8f20-120">Update the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to prerender the root `App` component:</span></span>

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * <span data-ttu-id="b8f20-121">Aggiornare l' :::no-loc(Blazor)::: origine dello script per usare lo script sul lato client :::no-loc(Blazor WebAssembly)::: :</span><span class="sxs-lookup"><span data-stu-id="b8f20-121">Update the :::no-loc(Blazor)::: script source to use the client-side :::no-loc(Blazor WebAssembly)::: script:</span></span>

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. <span data-ttu-id="b8f20-122">In `Startup.Configure` ( `Startup.cs` ) del progetto server:</span><span class="sxs-lookup"><span data-stu-id="b8f20-122">In `Startup.Configure` (`Startup.cs`) of the server project:</span></span>

   * <span data-ttu-id="b8f20-123">Chiamare il `UseDeveloperExceptionPage` Generatore di app nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="b8f20-123">Call `UseDeveloperExceptionPage` on the app builder in the Development environment.</span></span>
   * <span data-ttu-id="b8f20-124">Chiamare `Use:::no-loc(Blazor):::FrameworkFiles` il generatore di app.</span><span class="sxs-lookup"><span data-stu-id="b8f20-124">Call `Use:::no-loc(Blazor):::FrameworkFiles` on the app builder.</span></span>
   * <span data-ttu-id="b8f20-125">Modificare il fallback dalla `index.html` pagina ( `endpoints.MapFallbackToFile("index.html");` ) nella `_Host.cshtml` pagina.</span><span class="sxs-lookup"><span data-stu-id="b8f20-125">Change the fallback from the `index.html` page (`endpoints.MapFallbackToFile("index.html");`) to the `_Host.cshtml` page.</span></span>

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseWebAssemblyDebugging();
       }
       else
       {
           app.UseExceptionHandler("/Error");
           app.UseHsts();
       }

       app.UseHttpsRedirection();
       app.Use:::no-loc(Blazor):::FrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.Map:::no-loc(Razor):::Pages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a><span data-ttu-id="b8f20-126">Eseguire il rendering dei componenti in una pagina o in una vista con l'helper Tag Component</span><span class="sxs-lookup"><span data-stu-id="b8f20-126">Render components in a page or view with the Component Tag Helper</span></span>

<span data-ttu-id="b8f20-127">L'helper Tag Component supporta due modalità di rendering per il rendering di un componente da un' :::no-loc(Blazor WebAssembly)::: app in una pagina o in una visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="b8f20-127">The Component Tag Helper supports two render modes for rendering a component from a :::no-loc(Blazor WebAssembly)::: app in a page or view:</span></span>

* <span data-ttu-id="b8f20-128">`WebAssembly`: Esegue il rendering di un marcatore per un' :::no-loc(Blazor WebAssembly)::: app da usare per includere un componente interattivo quando viene caricato nel browser.</span><span class="sxs-lookup"><span data-stu-id="b8f20-128">`WebAssembly`: Renders a marker for a :::no-loc(Blazor WebAssembly)::: app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="b8f20-129">Il componente non è preeseguito.</span><span class="sxs-lookup"><span data-stu-id="b8f20-129">The component isn't prerendered.</span></span> <span data-ttu-id="b8f20-130">Questa opzione rende più semplice il rendering di :::no-loc(Blazor WebAssembly)::: componenti diversi in pagine diverse.</span><span class="sxs-lookup"><span data-stu-id="b8f20-130">This option makes it easier to render different :::no-loc(Blazor WebAssembly)::: components on different pages.</span></span>
* <span data-ttu-id="b8f20-131">`WebAssemblyPrerendered`: Esegue il rendering del componente in HTML statico e include un marcatore per un'app da usare in un :::no-loc(Blazor WebAssembly)::: secondo momento per rendere il componente interattivo quando viene caricato nel browser.</span><span class="sxs-lookup"><span data-stu-id="b8f20-131">`WebAssemblyPrerendered`: Prerenders the component into static HTML and includes a marker for a :::no-loc(Blazor WebAssembly)::: app for later use to make the component interactive when loaded in the browser.</span></span>

<span data-ttu-id="b8f20-132">Nell'esempio di :::no-loc(Razor)::: pagine seguente `Counter` viene eseguito il rendering del componente in una pagina.</span><span class="sxs-lookup"><span data-stu-id="b8f20-132">In the following :::no-loc(Razor)::: Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="b8f20-133">Per rendere interattivo il componente, lo :::no-loc(Blazor WebAssembly)::: script viene incluso nella [sezione di rendering](xref:mvc/views/layout#sections)della pagina.</span><span class="sxs-lookup"><span data-stu-id="b8f20-133">To make the component interactive, the :::no-loc(Blazor WebAssembly)::: script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span> <span data-ttu-id="b8f20-134">Per evitare di usare lo spazio dei nomi completo per il `Counter` componente con l'helper Tag Component ( `{APP ASSEMBLY}.Pages.Counter` ), aggiungere una [`@using`](xref:mvc/views/razor#using) direttiva per lo `Pages` spazio dei nomi dell'app client.</span><span class="sxs-lookup"><span data-stu-id="b8f20-134">To avoid using the full namespace for the `Counter` component with the Component Tag Helper (`{APP ASSEMBLY}.Pages.Counter`), add an [`@using`](xref:mvc/views/razor#using) directive for the client app's `Pages` namespace.</span></span> <span data-ttu-id="b8f20-135">Nell'esempio seguente lo spazio dei nomi dell'app client è `:::no-loc(Blazor):::Hosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="b8f20-135">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

```cshtml
...
@using :::no-loc(Blazor):::Hosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="b8f20-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Configura se il componente:</span><span class="sxs-lookup"><span data-stu-id="b8f20-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="b8f20-137">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="b8f20-137">Is prerendered into the page.</span></span>
* <span data-ttu-id="b8f20-138">Viene sottoposto a rendering come HTML statico nella pagina o se include le informazioni necessarie per il bootstrap di un' :::no-loc(Blazor)::: app dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="b8f20-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

<span data-ttu-id="b8f20-139">Per ulteriori informazioni sull'helper Tag Component, incluso il passaggio di parametri e <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configurazione, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="b8f20-139">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

<span data-ttu-id="b8f20-140">Nell'esempio precedente è necessario che il layout dell'app Server ( `_Layout.cshtml` ) includa una [sezione di rendering](xref:mvc/views/layout#sections) ( <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.RenderSection%2A> ) per lo script all'interno del tag di chiusura `</body>` :</span><span class="sxs-lookup"><span data-stu-id="b8f20-140">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="b8f20-141">Il `_Layout.cshtml` file si trova nella `Pages/Shared` cartella in un' :::no-loc(Razor)::: app di pagine o `Views/Shared` in una cartella in un'app MVC.</span><span class="sxs-lookup"><span data-stu-id="b8f20-141">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a :::no-loc(Razor)::: Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="b8f20-142">Se l'app deve anche applicare uno stile ai componenti con gli stili nell' :::no-loc(Blazor WebAssembly)::: app, includere gli stili dell'app nel `_Layout.cshtml` file.</span><span class="sxs-lookup"><span data-stu-id="b8f20-142">If the app should also style components with the styles in the :::no-loc(Blazor WebAssembly)::: app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="b8f20-143">Nell'esempio seguente lo spazio dei nomi dell'app client è `:::no-loc(Blazor):::Hosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="b8f20-143">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href=":::no-loc(Blazor):::Hosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a><span data-ttu-id="b8f20-144">Eseguire il rendering dei componenti in una pagina o in una vista con un selettore CSS</span><span class="sxs-lookup"><span data-stu-id="b8f20-144">Render components in a page or view with a CSS selector</span></span>

<span data-ttu-id="b8f20-145">Aggiungere componenti radice al progetto *client* in `Program.Main` ( `Program.cs` ).</span><span class="sxs-lookup"><span data-stu-id="b8f20-145">Add root components to the *Client* project in `Program.Main` (`Program.cs`).</span></span> <span data-ttu-id="b8f20-146">Nell'esempio seguente il `Counter` componente viene dichiarato come componente radice con un selettore CSS che seleziona l'elemento con l'oggetto `id` che corrisponde a `my-counter` .</span><span class="sxs-lookup"><span data-stu-id="b8f20-146">In the following example, the `Counter` component is declared as a root component with a CSS selector that selects the element with the `id` that matches `my-counter`.</span></span> <span data-ttu-id="b8f20-147">Nell'esempio seguente lo spazio dei nomi dell'app client è `:::no-loc(Blazor):::Hosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="b8f20-147">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

```csharp
using :::no-loc(Blazor):::Hosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

<span data-ttu-id="b8f20-148">Nell'esempio di :::no-loc(Razor)::: pagine seguente `Counter` viene eseguito il rendering del componente in una pagina.</span><span class="sxs-lookup"><span data-stu-id="b8f20-148">In the following :::no-loc(Razor)::: Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="b8f20-149">Per rendere interattivo il componente, lo :::no-loc(Blazor WebAssembly)::: script viene incluso nella [sezione di rendering](xref:mvc/views/layout#sections)della pagina:</span><span class="sxs-lookup"><span data-stu-id="b8f20-149">To make the component interactive, the :::no-loc(Blazor WebAssembly)::: script is included in the page's [render section](xref:mvc/views/layout#sections):</span></span>

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="b8f20-150">Nell'esempio precedente è necessario che il layout dell'app Server ( `_Layout.cshtml` ) includa una [sezione di rendering](xref:mvc/views/layout#sections) ( <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.RenderSection%2A> ) per lo script all'interno del tag di chiusura `</body>` :</span><span class="sxs-lookup"><span data-stu-id="b8f20-150">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="b8f20-151">Il `_Layout.cshtml` file si trova nella `Pages/Shared` cartella in un' :::no-loc(Razor)::: app di pagine o `Views/Shared` in una cartella in un'app MVC.</span><span class="sxs-lookup"><span data-stu-id="b8f20-151">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a :::no-loc(Razor)::: Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="b8f20-152">Se l'app deve anche applicare uno stile ai componenti con gli stili nell' :::no-loc(Blazor WebAssembly)::: app, includere gli stili dell'app nel `_Layout.cshtml` file.</span><span class="sxs-lookup"><span data-stu-id="b8f20-152">If the app should also style components with the styles in the :::no-loc(Blazor WebAssembly)::: app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="b8f20-153">Nell'esempio seguente lo spazio dei nomi dell'app client è `:::no-loc(Blazor):::Hosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="b8f20-153">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href=":::no-loc(Blazor):::Hosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b8f20-154">L'integrazione di :::no-loc(Razor)::: componenti in :::no-loc(Razor)::: pagine e app MVC in una :::no-loc(Blazor WebAssembly)::: soluzione ospitata è supportata in ASP.NET Core in .NET 5 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="b8f20-154">Integrating :::no-loc(Razor)::: components into :::no-loc(Razor)::: Pages and MVC apps in a hosted :::no-loc(Blazor WebAssembly)::: solution is supported in ASP.NET Core in .NET 5 or later.</span></span> <span data-ttu-id="b8f20-155">Selezionare una versione di .NET 5 o successiva di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="b8f20-155">Select a .NET 5 or later version of this article.</span></span>

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="b8f20-156">:::no-loc(Razor)::: i componenti possono essere integrati in :::no-loc(Razor)::: pagine e app MVC in un' :::no-loc(Blazor Server)::: app.</span><span class="sxs-lookup"><span data-stu-id="b8f20-156">:::no-loc(Razor)::: components can be integrated into :::no-loc(Razor)::: Pages and MVC apps in a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="b8f20-157">Quando viene eseguito il rendering della pagina o della vista, è possibile eseguire il rendering dei componenti contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="b8f20-157">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="b8f20-158">Dopo aver [configurato l'app](#configuration), usare le istruzioni riportate nelle sezioni seguenti a seconda dei requisiti dell'app:</span><span class="sxs-lookup"><span data-stu-id="b8f20-158">After [configuring the app](#configuration), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="b8f20-159">Componenti instradabili: per i componenti che sono direttamente instradabili dalle richieste degli utenti.</span><span class="sxs-lookup"><span data-stu-id="b8f20-159">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="b8f20-160">Seguire queste linee guida quando i visitatori devono essere in grado di effettuare una richiesta HTTP nel browser per un componente con una [`@page`](xref:mvc/views/razor#page) direttiva.</span><span class="sxs-lookup"><span data-stu-id="b8f20-160">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="b8f20-161">Usare componenti instradabili in un' :::no-loc(Razor)::: app pagine</span><span class="sxs-lookup"><span data-stu-id="b8f20-161">Use routable components in a :::no-loc(Razor)::: Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="b8f20-162">Usare componenti instradabili in un'app MVC</span><span class="sxs-lookup"><span data-stu-id="b8f20-162">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="b8f20-163">[Eseguire il rendering dei componenti da una pagina o vista](#render-components-from-a-page-or-view): per i componenti che non sono direttamente instradabili dalle richieste degli utenti.</span><span class="sxs-lookup"><span data-stu-id="b8f20-163">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="b8f20-164">Seguire queste linee guida quando l'app incorpora i componenti in pagine e visualizzazioni esistenti con l' [Helper tag dei componenti](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="b8f20-164">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="configuration"></a><span data-ttu-id="b8f20-165">Configurazione</span><span class="sxs-lookup"><span data-stu-id="b8f20-165">Configuration</span></span>

<span data-ttu-id="b8f20-166">Un' :::no-loc(Razor)::: app MVC o pagine esistente può integrare i :::no-loc(Razor)::: componenti in pagine e visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="b8f20-166">An existing :::no-loc(Razor)::: Pages or MVC app can integrate :::no-loc(Razor)::: components into pages and views:</span></span>

1. <span data-ttu-id="b8f20-167">Nel file di layout dell'app ( `_Layout.cshtml` ):</span><span class="sxs-lookup"><span data-stu-id="b8f20-167">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="b8f20-168">Aggiungere il `<base>` tag seguente all' `<head>` elemento:</span><span class="sxs-lookup"><span data-stu-id="b8f20-168">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="b8f20-169">Il `href` valore (il *percorso di base dell'app* ) nell'esempio precedente presuppone che l'app si trovi nel percorso URL radice ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="b8f20-169">The `href` value (the *app base path* ) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="b8f20-170">Se l'app è un'applicazione secondaria, seguire le istruzioni nella sezione *percorso di base* dell'applicazione dell' <xref:blazor/host-and-deploy/index#app-base-path> articolo.</span><span class="sxs-lookup"><span data-stu-id="b8f20-170">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="b8f20-171">Il `_Layout.cshtml` file si trova nella `Pages/Shared` cartella in un' :::no-loc(Razor)::: app di pagine o `Views/Shared` in una cartella in un'app MVC.</span><span class="sxs-lookup"><span data-stu-id="b8f20-171">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a :::no-loc(Razor)::: Pages app or `Views/Shared` folder in an MVC app.</span></span>

   * <span data-ttu-id="b8f20-172">Aggiungere un `<script>` tag per lo `blazor.server.js` script immediatamente prima della `Scripts` sezione Render:</span><span class="sxs-lookup"><span data-stu-id="b8f20-172">Add a `<script>` tag for the `blazor.server.js` script immediately before the `Scripts` render section:</span></span>

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     <span data-ttu-id="b8f20-173">Il Framework aggiunge lo `blazor.server.js` script all'app.</span><span class="sxs-lookup"><span data-stu-id="b8f20-173">The framework adds the `blazor.server.js` script to the app.</span></span> <span data-ttu-id="b8f20-174">Non è necessario aggiungere manualmente lo script all'app.</span><span class="sxs-lookup"><span data-stu-id="b8f20-174">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="b8f20-175">Aggiungere un `_Imports.razor` file alla cartella radice del progetto con il contenuto seguente (modificare l'ultimo spazio dei nomi, `MyAppNamespace` , nello spazio dei nomi dell'app):</span><span class="sxs-lookup"><span data-stu-id="b8f20-175">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="b8f20-176">In `Startup.ConfigureServices` registrare il :::no-loc(Blazor Server)::: servizio:</span><span class="sxs-lookup"><span data-stu-id="b8f20-176">In `Startup.ConfigureServices`, register the :::no-loc(Blazor Server)::: service:</span></span>

   ```csharp
   services.AddServerSide:::no-loc(Blazor):::();
   ```

1. <span data-ttu-id="b8f20-177">In `Startup.Configure` aggiungere l' :::no-loc(Blazor)::: endpoint dell'hub a `app.UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="b8f20-177">In `Startup.Configure`, add the :::no-loc(Blazor)::: Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.Map:::no-loc(Blazor):::Hub();
   ```

1. <span data-ttu-id="b8f20-178">Integrare i componenti in qualsiasi pagina o visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="b8f20-178">Integrate components into any page or view.</span></span> <span data-ttu-id="b8f20-179">Per ulteriori informazioni, vedere la sezione [rendering dei componenti da una pagina o vista](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="b8f20-179">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a><span data-ttu-id="b8f20-180">Usare componenti instradabili in un' :::no-loc(Razor)::: app pagine</span><span class="sxs-lookup"><span data-stu-id="b8f20-180">Use routable components in a :::no-loc(Razor)::: Pages app</span></span>

<span data-ttu-id="b8f20-181">*Questa sezione riguarda l'aggiunta di componenti che sono direttamente instradabili dalle richieste degli utenti.*</span><span class="sxs-lookup"><span data-stu-id="b8f20-181">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="b8f20-182">Per supportare componenti instradabili :::no-loc(Razor)::: nelle :::no-loc(Razor)::: app di pagine:</span><span class="sxs-lookup"><span data-stu-id="b8f20-182">To support routable :::no-loc(Razor)::: components in :::no-loc(Razor)::: Pages apps:</span></span>

1. <span data-ttu-id="b8f20-183">Seguire le istruzioni riportate nella sezione di [configurazione](#configuration) .</span><span class="sxs-lookup"><span data-stu-id="b8f20-183">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="b8f20-184">Aggiungere un `App.razor` file alla radice del progetto con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="b8f20-184">Add an `App.razor` file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="b8f20-185">Aggiungere un `_Host.cshtml` file alla `Pages` cartella con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="b8f20-185">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="b8f20-186">I componenti utilizzano il `_Layout.cshtml` file condiviso per il layout.</span><span class="sxs-lookup"><span data-stu-id="b8f20-186">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="b8f20-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Configura se il `App` componente:</span><span class="sxs-lookup"><span data-stu-id="b8f20-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="b8f20-188">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="b8f20-188">Is prerendered into the page.</span></span>
   * <span data-ttu-id="b8f20-189">Viene sottoposto a rendering come HTML statico nella pagina o se include le informazioni necessarie per il bootstrap di un' :::no-loc(Blazor)::: app dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="b8f20-189">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

   <span data-ttu-id="b8f20-190">Per ulteriori informazioni sull'helper Tag Component, incluso il passaggio di parametri e <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configurazione, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="b8f20-190">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="b8f20-191">Aggiungere una route con priorità bassa per la `_Host.cshtml` pagina alla configurazione dell'endpoint in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="b8f20-191">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="b8f20-192">Aggiungere componenti instradabili all'app.</span><span class="sxs-lookup"><span data-stu-id="b8f20-192">Add routable components to the app.</span></span> <span data-ttu-id="b8f20-193">Esempio:</span><span class="sxs-lookup"><span data-stu-id="b8f20-193">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="b8f20-194">Per ulteriori informazioni sugli spazi dei nomi, vedere la sezione relativa agli [spazi dei nomi dei componenti](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="b8f20-194">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="b8f20-195">Usare componenti instradabili in un'app MVC</span><span class="sxs-lookup"><span data-stu-id="b8f20-195">Use routable components in an MVC app</span></span>

<span data-ttu-id="b8f20-196">*Questa sezione riguarda l'aggiunta di componenti che sono direttamente instradabili dalle richieste degli utenti.*</span><span class="sxs-lookup"><span data-stu-id="b8f20-196">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="b8f20-197">Per supportare componenti instradabili :::no-loc(Razor)::: nelle app MVC:</span><span class="sxs-lookup"><span data-stu-id="b8f20-197">To support routable :::no-loc(Razor)::: components in MVC apps:</span></span>

1. <span data-ttu-id="b8f20-198">Seguire le istruzioni riportate nella sezione di [configurazione](#configuration) .</span><span class="sxs-lookup"><span data-stu-id="b8f20-198">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="b8f20-199">Aggiungere un `App.razor` file alla radice del progetto con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="b8f20-199">Add an `App.razor` file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="b8f20-200">Aggiungere un `_Host.cshtml` file alla `Views/Home` cartella con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="b8f20-200">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="b8f20-201">I componenti utilizzano il `_Layout.cshtml` file condiviso per il layout.</span><span class="sxs-lookup"><span data-stu-id="b8f20-201">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="b8f20-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Configura se il `App` componente:</span><span class="sxs-lookup"><span data-stu-id="b8f20-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="b8f20-203">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="b8f20-203">Is prerendered into the page.</span></span>
   * <span data-ttu-id="b8f20-204">Viene sottoposto a rendering come HTML statico nella pagina o se include le informazioni necessarie per il bootstrap di un' :::no-loc(Blazor)::: app dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="b8f20-204">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

   <span data-ttu-id="b8f20-205">Per ulteriori informazioni sull'helper Tag Component, incluso il passaggio di parametri e <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configurazione, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="b8f20-205">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="b8f20-206">Aggiungere un'azione al controller Home:</span><span class="sxs-lookup"><span data-stu-id="b8f20-206">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult :::no-loc(Blazor):::()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="b8f20-207">Aggiungere una route con priorità bassa per l'azione del controller che restituisce la `_Host.cshtml` visualizzazione alla configurazione dell'endpoint in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="b8f20-207">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController(":::no-loc(Blazor):::", "Home");
   });
   ```

1. <span data-ttu-id="b8f20-208">Creare una `Pages` cartella e aggiungere componenti instradabili all'app.</span><span class="sxs-lookup"><span data-stu-id="b8f20-208">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="b8f20-209">Esempio:</span><span class="sxs-lookup"><span data-stu-id="b8f20-209">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="b8f20-210">Per ulteriori informazioni sugli spazi dei nomi, vedere la sezione relativa agli [spazi dei nomi dei componenti](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="b8f20-210">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="b8f20-211">Eseguire il rendering dei componenti da una pagina o da una vista</span><span class="sxs-lookup"><span data-stu-id="b8f20-211">Render components from a page or view</span></span>

<span data-ttu-id="b8f20-212">*Questa sezione riguarda l'aggiunta di componenti a pagine o viste, in cui i componenti non sono direttamente instradabili dalle richieste degli utenti.*</span><span class="sxs-lookup"><span data-stu-id="b8f20-212">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="b8f20-213">Per eseguire il rendering di un componente da una pagina o da una vista, usare l' [Helper Tag Component](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="b8f20-213">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="b8f20-214">Eseguire il rendering di componenti interattivi con stato</span><span class="sxs-lookup"><span data-stu-id="b8f20-214">Render stateful interactive components</span></span>

<span data-ttu-id="b8f20-215">I componenti interattivi con stato possono essere aggiunti a una :::no-loc(Razor)::: pagina o a una vista.</span><span class="sxs-lookup"><span data-stu-id="b8f20-215">Stateful interactive components can be added to a :::no-loc(Razor)::: page or view.</span></span>

<span data-ttu-id="b8f20-216">Quando viene eseguito il rendering della pagina o della visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="b8f20-216">When the page or view renders:</span></span>

* <span data-ttu-id="b8f20-217">Il componente viene preeseguito con la pagina o la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="b8f20-217">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="b8f20-218">Lo stato iniziale del componente utilizzato per il prerendering viene perso.</span><span class="sxs-lookup"><span data-stu-id="b8f20-218">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="b8f20-219">Quando viene stabilita la connessione, viene creato il nuovo stato del componente :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="b8f20-219">New component state is created when the :::no-loc(SignalR)::: connection is established.</span></span>

<span data-ttu-id="b8f20-220">Nella pagina seguente viene :::no-loc(Razor)::: eseguito il rendering di un `Counter` componente:</span><span class="sxs-lookup"><span data-stu-id="b8f20-220">The following :::no-loc(Razor)::: page renders a `Counter` component:</span></span>

```cshtml
<h1>My :::no-loc(Razor)::: Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="b8f20-221">Per altre informazioni, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="b8f20-221">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="b8f20-222">Eseguire il rendering di componenti non interattivi</span><span class="sxs-lookup"><span data-stu-id="b8f20-222">Render noninteractive components</span></span>

<span data-ttu-id="b8f20-223">Nella pagina seguente :::no-loc(Razor)::: il componente viene sottoposto a `Counter` rendering statico con un valore iniziale specificato utilizzando un form.</span><span class="sxs-lookup"><span data-stu-id="b8f20-223">In the following :::no-loc(Razor)::: page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="b8f20-224">Poiché il componente viene sottoposto a rendering statico, il componente non è interattivo:</span><span class="sxs-lookup"><span data-stu-id="b8f20-224">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My :::no-loc(Razor)::: Page</h1>

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

<span data-ttu-id="b8f20-225">Per altre informazioni, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="b8f20-225">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="b8f20-226">Spazi dei nomi dei componenti</span><span class="sxs-lookup"><span data-stu-id="b8f20-226">Component namespaces</span></span>

<span data-ttu-id="b8f20-227">Quando si usa una cartella personalizzata per conservare i componenti dell'app, aggiungere lo spazio dei nomi che rappresenta la cartella alla pagina/visualizzazione o al `_ViewImports.cshtml` file.</span><span class="sxs-lookup"><span data-stu-id="b8f20-227">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="b8f20-228">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="b8f20-228">In the following example:</span></span>

* <span data-ttu-id="b8f20-229">Passare `MyAppNamespace` allo spazio dei nomi dell'app.</span><span class="sxs-lookup"><span data-stu-id="b8f20-229">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="b8f20-230">Se non viene usata una cartella denominata `Components` per conservare i componenti, passare `Components` alla cartella in cui si trovano i componenti.</span><span class="sxs-lookup"><span data-stu-id="b8f20-230">If a folder named `Components` isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="b8f20-231">Il `_ViewImports.cshtml` file si trova nella `Pages` cartella dell' :::no-loc(Razor)::: app pagine o nella `Views` cartella di un'app MVC.</span><span class="sxs-lookup"><span data-stu-id="b8f20-231">The `_ViewImports.cshtml` file is located in the `Pages` folder of a :::no-loc(Razor)::: Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="b8f20-232">Per altre informazioni, vedere <xref:blazor/components/index#namespaces>.</span><span class="sxs-lookup"><span data-stu-id="b8f20-232">For more information, see <xref:blazor/components/index#namespaces>.</span></span>

::: zone-end
