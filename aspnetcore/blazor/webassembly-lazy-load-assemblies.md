---
title: Assembly di caricamento lazy in ASP.NET CoreBlazor WebAssembly
author: guardrex
description: Scopri come Lazy caricare gli assembly nelle Blazor WebAssembly app ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/16/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 0ce03badccad4e06aa3c316580ab82be38a806c6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013372"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="be8c8-103">Assembly di caricamento lazy in ASP.NET CoreBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="be8c8-103">Lazy load assemblies in ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="be8c8-104">Di [Safia Amodio](https://safia.rocks) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="be8c8-104">By [Safia Abdalla](https://safia.rocks) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="be8c8-105">Blazor WebAssemblyle prestazioni di avvio dell'app possono essere migliorate posticipando il caricamento di alcuni assembly di applicazioni fino a quando non sono necessarie, operazione denominata *caricamento lazy*.</span><span class="sxs-lookup"><span data-stu-id="be8c8-105">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they are required, which is called *lazy loading*.</span></span> <span data-ttu-id="be8c8-106">Ad esempio, gli assembly che vengono utilizzati solo per il rendering di un singolo componente possono essere impostati per il caricamento solo se l'utente passa a tale componente.</span><span class="sxs-lookup"><span data-stu-id="be8c8-106">For example, assemblies that are only used to render a single component can be set up to load only if the user navigates to that component.</span></span> <span data-ttu-id="be8c8-107">Dopo il caricamento, gli assembly vengono memorizzati nella cache sul lato client e sono disponibili per tutte le navigazioni future.</span><span class="sxs-lookup"><span data-stu-id="be8c8-107">After loading, the assemblies are cached client-side and are available for all future navigations.</span></span>

<span data-ttu-id="be8c8-108">Blazorla funzionalità di caricamento lazy consente di contrassegnare gli assembly dell'app per il caricamento lazy, che carica gli assembly in fase di esecuzione quando l'utente passa a una route specifica.</span><span class="sxs-lookup"><span data-stu-id="be8c8-108">Blazor's lazy loading feature allows you to mark app assemblies for lazy loading, which loads the assemblies during runtime when the user navigates to a particular route.</span></span> <span data-ttu-id="be8c8-109">La funzionalità è costituita dalle modifiche apportate al file di progetto e dalle modifiche apportate al router dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="be8c8-109">The feature consists of changes to the project file and changes to the application's router.</span></span>

> [!NOTE]
> <span data-ttu-id="be8c8-110">Il caricamento lazy dell'assembly non avvantaggia Blazor Server le app perché gli assembly non vengono scaricati nel client in un' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="be8c8-110">Assembly lazy loading doesn't benefit Blazor Server apps because assemblies aren't downloaded to the client in a Blazor Server app.</span></span>

## <a name="project-file"></a><span data-ttu-id="be8c8-111">File di progetto</span><span class="sxs-lookup"><span data-stu-id="be8c8-111">Project file</span></span>

<span data-ttu-id="be8c8-112">Contrassegnare gli assembly per il caricamento lazy nel file di progetto dell'app ( `.csproj` ) usando l' `BlazorWebAssemblyLazyLoad` elemento.</span><span class="sxs-lookup"><span data-stu-id="be8c8-112">Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `BlazorWebAssemblyLazyLoad` item.</span></span> <span data-ttu-id="be8c8-113">Usare il nome dell'assembly senza `.dll` estensione.</span><span class="sxs-lookup"><span data-stu-id="be8c8-113">Use the assembly name without the `.dll` extension.</span></span> <span data-ttu-id="be8c8-114">Il Blazor Framework impedisce il caricamento degli assembly specificati da questo gruppo di elementi all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="be8c8-114">The Blazor framework prevents the assemblies specified by this item group from loading at app launch.</span></span> <span data-ttu-id="be8c8-115">Nell'esempio seguente viene contrassegnato un assembly personalizzato di grandi dimensioni ( `GrantImaharaRobotControls.dll` ) per il caricamento lazy.</span><span class="sxs-lookup"><span data-stu-id="be8c8-115">The following example marks a large custom assembly (`GrantImaharaRobotControls.dll`) for lazy loading.</span></span> <span data-ttu-id="be8c8-116">Se un assembly contrassegnato per il caricamento lazy presenta dipendenze, è necessario contrassegnarlo anche per il caricamento lazy nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="be8c8-116">If an assembly that's marked for lazy loading has dependencies, they must also be marked for lazy loading in the project file.</span></span>

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls" />
</ItemGroup>
```

<span data-ttu-id="be8c8-117">Solo gli assembly usati dall'app possono essere caricati in modalità differita.</span><span class="sxs-lookup"><span data-stu-id="be8c8-117">Only assemblies that are used by the app can be lazily loaded.</span></span> <span data-ttu-id="be8c8-118">Il linker rimuove gli assembly inutilizzati dall'output pubblicato.</span><span class="sxs-lookup"><span data-stu-id="be8c8-118">The linker strips unused assemblies from published output.</span></span>

## <a name="router-component"></a><span data-ttu-id="be8c8-119">Componente `Router`</span><span class="sxs-lookup"><span data-stu-id="be8c8-119">`Router` component</span></span>

<span data-ttu-id="be8c8-120">Blazoril `Router` componente indica gli assembly in cui vengono Blazor cercati i componenti instradabili.</span><span class="sxs-lookup"><span data-stu-id="be8c8-120">Blazor's `Router` component designates which assemblies Blazor searches for routable components.</span></span> <span data-ttu-id="be8c8-121">Il `Router` componente è inoltre responsabile del rendering del componente per la route in cui l'utente si sposta.</span><span class="sxs-lookup"><span data-stu-id="be8c8-121">The `Router` component is also responsible for rendering the component for the route where the user navigates.</span></span> <span data-ttu-id="be8c8-122">Il `Router` componente supporta una `OnNavigateAsync` funzionalità che può essere utilizzata in combinazione con il caricamento lazy.</span><span class="sxs-lookup"><span data-stu-id="be8c8-122">The `Router` component supports an `OnNavigateAsync` feature that can be used in conjunction with lazy loading.</span></span>

<span data-ttu-id="be8c8-123">Nel componente dell'app `Router` ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="be8c8-123">In the app's `Router` component (`App.razor`):</span></span>

* <span data-ttu-id="be8c8-124">Aggiungere un `OnNavigateAsync` callback.</span><span class="sxs-lookup"><span data-stu-id="be8c8-124">Add an `OnNavigateAsync` callback.</span></span> <span data-ttu-id="be8c8-125">Il `OnNavigateAsync` gestore viene richiamato quando l'utente:</span><span class="sxs-lookup"><span data-stu-id="be8c8-125">The `OnNavigateAsync` handler is invoked when the user:</span></span>
  * <span data-ttu-id="be8c8-126">Visita una route per la prima volta passando direttamente dal browser.</span><span class="sxs-lookup"><span data-stu-id="be8c8-126">Visits a route for the first time by navigating to it directly from their browser.</span></span>
  * <span data-ttu-id="be8c8-127">Passa a una nuova route usando un collegamento o una <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> chiamata.</span><span class="sxs-lookup"><span data-stu-id="be8c8-127">Navigates to a new route using a link or a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocation.</span></span>
* <span data-ttu-id="be8c8-128">Se gli assembly con caricamento lazy contengono componenti instradabili, aggiungere un [elenco](xref:System.Collections.Generic.List%601) \<<xref:System.Reflection.Assembly>> (ad esempio, denominato `lazyLoadedAssemblies` ) al componente.</span><span class="sxs-lookup"><span data-stu-id="be8c8-128">If lazy-loaded assemblies contain routable components, add a [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (for example, named `lazyLoadedAssemblies`) to the component.</span></span> <span data-ttu-id="be8c8-129">Gli assembly vengono passati di nuovo alla <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> raccolta, nel caso in cui gli assembly contengano componenti instradabili.</span><span class="sxs-lookup"><span data-stu-id="be8c8-129">The assemblies are passed back to the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection in case the assemblies contain routable components.</span></span> <span data-ttu-id="be8c8-130">Il Framework cerca gli assembly per le route e aggiorna la raccolta di route se vengono trovate nuove route.</span><span class="sxs-lookup"><span data-stu-id="be8c8-130">The framework searches the assemblies for routes and updates the route collection if any new routes are found.</span></span>

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

<span data-ttu-id="be8c8-131">Se il `OnNavigateAsync` callback genera un'eccezione non gestita, viene richiamata l' [ Blazor interfaccia utente dell'errore](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) .</span><span class="sxs-lookup"><span data-stu-id="be8c8-131">If the `OnNavigateAsync` callback throws an unhandled exception, the [Blazor error UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) is invoked.</span></span>

### <a name="assembly-load-logic-in-onnavigateasync"></a><span data-ttu-id="be8c8-132">Logica di caricamento dell'assembly in`OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="be8c8-132">Assembly load logic in `OnNavigateAsync`</span></span>

<span data-ttu-id="be8c8-133">`OnNavigateAsync`dispone di un `NavigationContext` parametro che fornisce informazioni sull'evento di spostamento asincrono corrente, inclusi il percorso di destinazione ( `Path` ) e il token di annullamento ( `CancellationToken` ):</span><span class="sxs-lookup"><span data-stu-id="be8c8-133">`OnNavigateAsync` has a `NavigationContext` parameter that provides information about the current asynchronous navigation event, including the target path (`Path`) and the cancellation token (`CancellationToken`):</span></span>

* <span data-ttu-id="be8c8-134">La `Path` proprietà è il percorso di destinazione dell'utente relativo al percorso di base dell'applicazione, ad esempio `/robot` .</span><span class="sxs-lookup"><span data-stu-id="be8c8-134">The `Path` property is the user's destination path relative to the app's base path, such as `/robot`.</span></span>
* <span data-ttu-id="be8c8-135">L'oggetto `CancellationToken` può essere utilizzato per osservare l'annullamento dell'attività asincrona.</span><span class="sxs-lookup"><span data-stu-id="be8c8-135">The `CancellationToken` can be used to observe the cancellation of the asynchronous task.</span></span> <span data-ttu-id="be8c8-136">`OnNavigateAsync`Annulla automaticamente l'attività di spostamento attualmente in esecuzione quando l'utente passa a una pagina diversa.</span><span class="sxs-lookup"><span data-stu-id="be8c8-136">`OnNavigateAsync` automatically cancels the currently running navigation task when the user navigates to a different page.</span></span>

<span data-ttu-id="be8c8-137">All'interno di `OnNavigateAsync` implementare la logica per determinare gli assembly da caricare.</span><span class="sxs-lookup"><span data-stu-id="be8c8-137">Inside `OnNavigateAsync`, implement logic to determine the assemblies to load.</span></span> <span data-ttu-id="be8c8-138">Le opzioni includono:</span><span class="sxs-lookup"><span data-stu-id="be8c8-138">Options include:</span></span>

* <span data-ttu-id="be8c8-139">Controlli condizionali all'interno del `OnNavigateAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="be8c8-139">Conditional checks inside the `OnNavigateAsync` method.</span></span>
* <span data-ttu-id="be8c8-140">Tabella di ricerca che esegue il mapping delle route ai nomi degli assembly, inseriti nel componente o implementati all'interno del [`@code`](xref:mvc/views/razor#code) blocco.</span><span class="sxs-lookup"><span data-stu-id="be8c8-140">A lookup table that maps routes to assembly names, either injected into the component or implemented within the [`@code`](xref:mvc/views/razor#code) block.</span></span>

<span data-ttu-id="be8c8-141">`LazyAssemblyLoader`è un servizio singleton fornito dal Framework per il caricamento di assembly.</span><span class="sxs-lookup"><span data-stu-id="be8c8-141">`LazyAssemblyLoader` is a framework-provided singleton service for loading assemblies.</span></span> <span data-ttu-id="be8c8-142">Inserire `LazyAssemblyLoader` nel `Router` componente:</span><span class="sxs-lookup"><span data-stu-id="be8c8-142">Inject `LazyAssemblyLoader` into the `Router` component:</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

<span data-ttu-id="be8c8-143">`LazyAssemblyLoader`Fornisce il `LoadAssembliesAsync` metodo che:</span><span class="sxs-lookup"><span data-stu-id="be8c8-143">The `LazyAssemblyLoader` provides the `LoadAssembliesAsync` method that:</span></span>

* <span data-ttu-id="be8c8-144">Usa l'interoperabilità JS per recuperare assembly tramite una chiamata di rete.</span><span class="sxs-lookup"><span data-stu-id="be8c8-144">Uses JS interop to fetch assemblies via a network call.</span></span>
* <span data-ttu-id="be8c8-145">Carica gli assembly nel runtime in esecuzione su webassembly nel browser.</span><span class="sxs-lookup"><span data-stu-id="be8c8-145">Loads assemblies into the runtime executing on WebAssembly in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="be8c8-146">L'implementazione del caricamento lazy del Framework supporta il prerendering sul server.</span><span class="sxs-lookup"><span data-stu-id="be8c8-146">The framework's lazy loading implementation supports prerendering on the server.</span></span> <span data-ttu-id="be8c8-147">Durante il prerendering, si presuppone che tutti gli assembly, inclusi quelli contrassegnati per il caricamento lazy, siano caricati.</span><span class="sxs-lookup"><span data-stu-id="be8c8-147">During prerendering, all assemblies, including those marked for lazy loading, are assumed to be loaded.</span></span>

### <a name="user-interaction-with-navigating-content"></a><span data-ttu-id="be8c8-148">Interazione dell'utente con il `<Navigating>` contenuto</span><span class="sxs-lookup"><span data-stu-id="be8c8-148">User interaction with `<Navigating>` content</span></span>

<span data-ttu-id="be8c8-149">Durante il caricamento degli assembly, che possono richiedere alcuni secondi, il `Router` componente può indicare all'utente che è in corso una transizione di pagina:</span><span class="sxs-lookup"><span data-stu-id="be8c8-149">While loading assemblies, which can take several seconds, the `Router` component can indicate to the user that a page transition is occurring:</span></span>

* <span data-ttu-id="be8c8-150">Aggiungere una [`@using`](xref:mvc/views/razor#using) direttiva per lo <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="be8c8-150">Add an [`@using`](xref:mvc/views/razor#using) directive for the <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.</span></span>
* <span data-ttu-id="be8c8-151">Aggiungere un `<Navigating>` tag al componente con markup da visualizzare durante gli eventi di transizione di pagina.</span><span class="sxs-lookup"><span data-stu-id="be8c8-151">Add a `<Navigating>` tag to the component with markup to display during page transition events.</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

### <a name="handle-cancellations-in-onnavigateasync"></a><span data-ttu-id="be8c8-152">Gestisci annullamenti in`OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="be8c8-152">Handle cancellations in `OnNavigateAsync`</span></span>

<span data-ttu-id="be8c8-153">L' `NavigationContext` oggetto passato al `OnNavigateAsync` callback contiene un oggetto `CancellationToken` impostato quando si verifica un nuovo evento di navigazione.</span><span class="sxs-lookup"><span data-stu-id="be8c8-153">The `NavigationContext` object passed to the `OnNavigateAsync` callback contains a `CancellationToken` that's set when a new navigation event occurs.</span></span> <span data-ttu-id="be8c8-154">Il `OnNavigateAsync` callback deve generare quando questo token di annullamento è impostato in modo da evitare di continuare a eseguire il `OnNavigateAsync` callback in una navigazione obsoleta.</span><span class="sxs-lookup"><span data-stu-id="be8c8-154">The `OnNavigateAsync` callback must throw when this cancellation token is set to avoid continuing to run the `OnNavigateAsync` callback on a outdated navigation.</span></span>

<span data-ttu-id="be8c8-155">Se un utente passa a instradare un oggetto e quindi immediatamente alla Route B, l'app non deve continuare a eseguire il `OnNavigateAsync` callback per il routing a:</span><span class="sxs-lookup"><span data-stu-id="be8c8-155">If a user navigates to Route A and then immediately to Route B, the app shouldn't continue running the `OnNavigateAsync` callback for Route A:</span></span>

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="be8c8-156">Se il token di annullamento in `NavigationContext` viene annullato può generare un comportamento imprevisto, ad esempio il rendering di un componente da una navigazione precedente.</span><span class="sxs-lookup"><span data-stu-id="be8c8-156">Not throwing if the cancellation token in `NavigationContext` is canceled can result in unintended behavior, such as rendering a component from a previous navigation.</span></span>

### <a name="complete-example"></a><span data-ttu-id="be8c8-157">Esempio completo</span><span class="sxs-lookup"><span data-stu-id="be8c8-157">Complete example</span></span>

<span data-ttu-id="be8c8-158">Il componente completo seguente `Router` illustra il caricamento dell' `GrantImaharaRobotControls.dll` assembly quando l'utente passa a `/robot` .</span><span class="sxs-lookup"><span data-stu-id="be8c8-158">The following complete `Router` component demonstrates loading the `GrantImaharaRobotControls.dll` assembly when the user navigates to `/robot`.</span></span> <span data-ttu-id="be8c8-159">Durante le transizioni di pagina, all'utente viene visualizzato un messaggio con stile.</span><span class="sxs-lookup"><span data-stu-id="be8c8-159">During page transitions, a styled message is displayed to the user.</span></span>

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="be8c8-160">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="be8c8-160">Troubleshoot</span></span>

* <span data-ttu-id="be8c8-161">Se si verifica un rendering imprevisto (ad esempio, viene eseguito il rendering di un componente di una navigazione precedente), verificare che il codice venga generato se è impostato il token di annullamento.</span><span class="sxs-lookup"><span data-stu-id="be8c8-161">If unexpected rendering occurs (for example, a component from a previous navigation is rendered), confirm that the code throws if the cancellation token is set.</span></span>
* <span data-ttu-id="be8c8-162">Se gli assembly vengono ancora caricati all'avvio dell'applicazione, verificare che l'assembly sia contrassegnato come Lazy Loaded nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="be8c8-162">If assemblies are still loaded at application start, check that the assembly is marked as lazy loaded in the project file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="be8c8-163">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="be8c8-163">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices>
