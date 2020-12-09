---
title: BlazorRouting ASP.NET Core
author: guardrex
description: Informazioni su come instradare le richieste nelle app e sul componente NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/17/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: 3bfd623a206f260d24e2c9009acdb3b205b7ab2d
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855404"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="c4e4d-103">BlazorRouting ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c4e4d-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="c4e4d-104">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c4e4d-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c4e4d-105">Informazioni su come instradare le richieste e su come usare il <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente per creare collegamenti di navigazione nelle Blazor app.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="c4e4d-106">Integrazione di routing endpoint ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c4e4d-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="c4e4d-107">Blazor Server è integrato in [ASP.net core routing dell'endpoint](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="c4e4d-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="c4e4d-108">Un'app ASP.NET Core è configurata in modo da accettare le connessioni in ingresso per i componenti interattivi con <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="c4e4d-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="c4e4d-109">La configurazione più tipica consiste nel routing di tutte le richieste a una Razor pagina, che funge da host per la parte lato server dell' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="c4e4d-110">Per convenzione, la pagina *host* è in genere denominata `_Host.cshtml` .</span><span class="sxs-lookup"><span data-stu-id="c4e4d-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="c4e4d-111">La route specificata nel file host viene chiamata route di *fallback* perché funziona con una priorità bassa nella corrispondenza della route.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="c4e4d-112">La route di fallback viene considerata quando altre route non corrispondono.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="c4e4d-113">Ciò consente all'app di usare altri controller e pagine senza interferire con l' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-113">This allows the app to use other controllers and pages without interfering with the Blazor Server app.</span></span>

<span data-ttu-id="c4e4d-114">Per informazioni sulla configurazione <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> di per l'hosting di server URL non radice, vedere <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="c4e4d-114">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="route-templates"></a><span data-ttu-id="c4e4d-115">Modelli di route</span><span class="sxs-lookup"><span data-stu-id="c4e4d-115">Route templates</span></span>

<span data-ttu-id="c4e4d-116">Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente consente il routing a ogni componente con una route specificata.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="c4e4d-117">Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente viene visualizzato nel `App.razor` file:</span><span class="sxs-lookup"><span data-stu-id="c4e4d-117">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="c4e4d-118">Quando `.razor` viene compilato un file con una `@page` direttiva, alla classe generata viene fornito un oggetto che <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifica il modello di route.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-118">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="c4e4d-119">Quando l'app viene avviata, l'assembly specificato come `AppAssembly` viene analizzato per raccogliere informazioni su tutti i componenti che hanno un <xref:Microsoft.AspNetCore.Components.RouteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="c4e4d-119">When the app boots, the assembly specified as the `AppAssembly` is scanned to gather information about all of the components that have a <xref:Microsoft.AspNetCore.Components.RouteAttribute>.</span></span>

<span data-ttu-id="c4e4d-120">In fase di esecuzione, il <xref:Microsoft.AspNetCore.Components.RouteView> componente:</span><span class="sxs-lookup"><span data-stu-id="c4e4d-120">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="c4e4d-121">Riceve <xref:Microsoft.AspNetCore.Components.RouteData> da <xref:Microsoft.AspNetCore.Components.Routing.Router> insieme a tutti i parametri desiderati.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-121">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="c4e4d-122">Esegue il rendering del componente specificato con il layout (o un layout predefinito facoltativo) utilizzando i parametri specificati.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-122">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="c4e4d-123">Facoltativamente, è possibile specificare un <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parametro con una classe layout da utilizzare per i componenti che non specificano un layout.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-123">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="c4e4d-124">I Blazor modelli predefiniti specificano il `MainLayout` componente.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-124">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="c4e4d-125">`MainLayout.razor` si trova nella cartella del progetto del modello `Shared` .</span><span class="sxs-lookup"><span data-stu-id="c4e4d-125">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="c4e4d-126">Per ulteriori informazioni sui layout, vedere <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="c4e4d-126">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="c4e4d-127">È possibile applicare più modelli di route a un componente.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-127">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="c4e4d-128">Il componente seguente risponde alle richieste per `/BlazorRoute` e `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="c4e4d-128">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="c4e4d-129">Per la corretta risoluzione degli URL, l'app deve includere un `<base>` tag nel `wwwroot/index.html` file ( Blazor WebAssembly ) o nel `Pages/_Host.cshtml` file ( Blazor Server ) con il percorso di base dell'applicazione specificato nell' `href` attributo ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="c4e4d-129">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="c4e4d-130">Per altre informazioni, vedere <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-130">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="c4e4d-131">Fornire contenuto personalizzato quando il contenuto non è stato trovato</span><span class="sxs-lookup"><span data-stu-id="c4e4d-131">Provide custom content when content isn't found</span></span>

<span data-ttu-id="c4e4d-132">Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente consente all'app di specificare contenuto personalizzato se non viene trovato contenuto per la route richiesta.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-132">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="c4e4d-133">Nel `App.razor` file, impostare il contenuto personalizzato nel <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> parametro di modello del <xref:Microsoft.AspNetCore.Components.Routing.Router> componente:</span><span class="sxs-lookup"><span data-stu-id="c4e4d-133">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="c4e4d-134">Il contenuto dei `<NotFound>` tag può includere elementi arbitrari, ad esempio altri componenti interattivi.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-134">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="c4e4d-135">Per applicare un layout predefinito al <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenuto, vedere <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="c4e4d-135">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="c4e4d-136">Routing a componenti da più assembly</span><span class="sxs-lookup"><span data-stu-id="c4e4d-136">Route to components from multiple assemblies</span></span>

<span data-ttu-id="c4e4d-137">Usare il <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parametro per specificare gli assembly aggiuntivi da <xref:Microsoft.AspNetCore.Components.Routing.Router> considerare per il componente durante la ricerca di componenti instradabili.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-137">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="c4e4d-138">Gli assembly specificati sono considerati oltre all' `AppAssembly` assembly specificato da.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-138">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="c4e4d-139">Nell'esempio seguente `Component1` è un componente instradabile definito in una libreria di classi a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-139">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="c4e4d-140">Nell' <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> esempio seguente viene restituito il supporto del routing per `Component1` :</span><span class="sxs-lookup"><span data-stu-id="c4e4d-140">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="c4e4d-141">Parametri di route</span><span class="sxs-lookup"><span data-stu-id="c4e4d-141">Route parameters</span></span>

<span data-ttu-id="c4e4d-142">Il router usa parametri di route per popolare i parametri del componente corrispondenti con lo stesso nome (senza distinzione tra maiuscole e minuscole).</span><span class="sxs-lookup"><span data-stu-id="c4e4d-142">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c4e4d-143">Sono supportati i parametri facoltativi.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-143">Optional parameters are supported.</span></span> <span data-ttu-id="c4e4d-144">Nell'esempio seguente, il `text` parametro facoltativo assegna il valore del segmento di route alla proprietà del componente `Text` .</span><span class="sxs-lookup"><span data-stu-id="c4e4d-144">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="c4e4d-145">Se il segmento non è presente, il valore di `Text` viene impostato su `fantastic` :</span><span class="sxs-lookup"><span data-stu-id="c4e4d-145">If the segment isn't present, the value of `Text` is set to `fantastic`:</span></span>

```razor
@page "/RouteParameter/{text?}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="c4e4d-146">I parametri facoltativi non sono supportati.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-146">Optional parameters aren't supported.</span></span> <span data-ttu-id="c4e4d-147">`@page`Nell'esempio precedente vengono applicate due direttive.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-147">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="c4e4d-148">Il primo consente la navigazione al componente senza un parametro.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-148">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="c4e4d-149">La seconda `@page` direttiva accetta il `{text}` parametro Route e assegna il valore alla `Text` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-149">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="c4e4d-150">Usare on [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) anziché [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) per consentire l'esplorazione delle app allo stesso componente con un valore di parametro facoltativo diverso.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-150">Use on [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="c4e4d-151">In base all'esempio precedente, usare `OnParametersSet` quando l'utente deve essere in grado di spostarsi da `/RouteParameter` a `/RouteParameter/awesome` o da `/RouteParameter/awesome` a `/RouteParameter` :</span><span class="sxs-lookup"><span data-stu-id="c4e4d-151">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/awesome` or from `/RouteParameter/awesome` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="c4e4d-152">Vincoli di route</span><span class="sxs-lookup"><span data-stu-id="c4e4d-152">Route constraints</span></span>

<span data-ttu-id="c4e4d-153">Un vincolo di route impone la corrispondenza tra i tipi in un segmento di route e un componente.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-153">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="c4e4d-154">Nell'esempio seguente, la route per il `Users` componente corrisponde solo a se:</span><span class="sxs-lookup"><span data-stu-id="c4e4d-154">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="c4e4d-155">Un `Id` segmento di route è presente nell'URL della richiesta.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-155">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="c4e4d-156">Il `Id` segmento è un numero intero ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="c4e4d-156">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="c4e4d-157">Sono disponibili i vincoli di route indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-157">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="c4e4d-158">Per ulteriori informazioni, vedere l'avviso sotto la tabella per i vincoli di route corrispondenti alla lingua inglese.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-158">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="c4e4d-159">Vincolo</span><span class="sxs-lookup"><span data-stu-id="c4e4d-159">Constraint</span></span> | <span data-ttu-id="c4e4d-160">Esempio</span><span class="sxs-lookup"><span data-stu-id="c4e4d-160">Example</span></span>           | <span data-ttu-id="c4e4d-161">Esempi di corrispondenza</span><span class="sxs-lookup"><span data-stu-id="c4e4d-161">Example Matches</span></span>                                                                  | <span data-ttu-id="c4e4d-162">Invariante</span><span class="sxs-lookup"><span data-stu-id="c4e4d-162">Invariant</span></span><br><span data-ttu-id="c4e4d-163">culture</span><span class="sxs-lookup"><span data-stu-id="c4e4d-163">culture</span></span><br><span data-ttu-id="c4e4d-164">corrispondenti</span><span class="sxs-lookup"><span data-stu-id="c4e4d-164">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="c4e4d-165">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="c4e4d-165">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="c4e4d-166">No</span><span class="sxs-lookup"><span data-stu-id="c4e4d-166">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="c4e4d-167">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="c4e4d-167">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="c4e4d-168">Sì</span><span class="sxs-lookup"><span data-stu-id="c4e4d-168">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="c4e4d-169">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="c4e4d-169">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="c4e4d-170">Sì</span><span class="sxs-lookup"><span data-stu-id="c4e4d-170">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="c4e4d-171">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="c4e4d-171">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="c4e4d-172">Sì</span><span class="sxs-lookup"><span data-stu-id="c4e4d-172">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="c4e4d-173">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="c4e4d-173">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="c4e4d-174">Sì</span><span class="sxs-lookup"><span data-stu-id="c4e4d-174">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="c4e4d-175">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="c4e4d-175">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="c4e4d-176">No</span><span class="sxs-lookup"><span data-stu-id="c4e4d-176">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="c4e4d-177">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="c4e4d-177">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="c4e4d-178">Sì</span><span class="sxs-lookup"><span data-stu-id="c4e4d-178">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="c4e4d-179">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="c4e4d-179">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="c4e4d-180">Sì</span><span class="sxs-lookup"><span data-stu-id="c4e4d-180">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="c4e4d-181">I vincoli di route che verificano l'URL e vengono convertiti in un tipo CLR, ad esempio `int` o <xref:System.DateTime>, usano sempre le impostazioni cultura inglese non dipendenti da paese/area geografica,</span><span class="sxs-lookup"><span data-stu-id="c4e4d-181">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="c4e4d-182">presupponendo che l'URL sia non localizzabile.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-182">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="c4e4d-183">Routing con URL contenenti punti</span><span class="sxs-lookup"><span data-stu-id="c4e4d-183">Routing with URLs that contain dots</span></span>

<span data-ttu-id="c4e4d-184">Per Blazor WebAssembly le app e ospitate Blazor Server , il modello di route predefinito sul lato server presuppone che se l'ultimo segmento di un URL della richiesta contiene un punto ( `.` ) richiesto da un file, ad esempio `https://localhost.com:5001/example/some.thing` .</span><span class="sxs-lookup"><span data-stu-id="c4e4d-184">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested (for example, `https://localhost.com:5001/example/some.thing`).</span></span> <span data-ttu-id="c4e4d-185">Senza alcuna configurazione aggiuntiva, un'app restituisce una risposta *404 non trovata* se questa era destinata al routing a un componente.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-185">Without additional configuration, an app returns a *404 - Not Found* response if this was meant to route to a component.</span></span> <span data-ttu-id="c4e4d-186">Per usare una route con uno o più parametri che contengono un punto, l'app deve configurare la route con un modello personalizzato.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-186">To use a route with one or more parameters that contains a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="c4e4d-187">Si consideri il `Example` componente seguente che può ricevere un parametro di route dall'ultimo segmento dell'URL:</span><span class="sxs-lookup"><span data-stu-id="c4e4d-187">Consider the following `Example` component that can receive a route parameter from the last segment of the URL:</span></span>

```razor
@page "/example"
@page "/example/{param}"

<p>
    Param: @Param
</p>

@code {
    [Parameter]
    public string Param { get; set; }
}
```

<span data-ttu-id="c4e4d-188">Per consentire all'app *Server* di una soluzione ospitata Blazor WebAssembly di instradare la richiesta con un punto nel `param` parametro, aggiungere un modello di route per il file di fallback con il parametro facoltativo in `Startup.Configure` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="c4e4d-188">To permit the *Server* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` parameter, add a fallback file route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="c4e4d-189">Per configurare un' Blazor Server app per instradare la richiesta con un punto nel `param` parametro, aggiungere un modello di route della pagina di fallback con il parametro facoltativo in `Startup.Configure` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="c4e4d-189">To configure a Blazor Server app to route the request with a dot in the `param` parameter, add a fallback page route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="c4e4d-190">Per altre informazioni, vedere <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-190">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="c4e4d-191">Parametri di route catch-all</span><span class="sxs-lookup"><span data-stu-id="c4e4d-191">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c4e4d-192">*Questa sezione si applica a ASP.NET Core in .NET 5 Release Candidate 1 (RC1) o versioni successive.*</span><span class="sxs-lookup"><span data-stu-id="c4e4d-192">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="c4e4d-193">I parametri di route catch-all, che acquisiscono percorsi tra più limiti di cartella, sono supportati nei componenti di.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-193">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="c4e4d-194">Il parametro della route catch-all deve essere:</span><span class="sxs-lookup"><span data-stu-id="c4e4d-194">The catch-all route parameter must be:</span></span>

* <span data-ttu-id="c4e4d-195">Denominato in modo che corrisponda al nome del segmento di route.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-195">Named to match the route segment name.</span></span> <span data-ttu-id="c4e4d-196">La denominazione non distingue tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-196">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="c4e4d-197">Tipo `string`.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-197">A `string` type.</span></span> <span data-ttu-id="c4e4d-198">Il Framework non fornisce il cast automatico.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-198">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="c4e4d-199">Alla fine dell'URL.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-199">At the end of the URL.</span></span>

```razor
@page "/page/{*pageRoute}"

@code {
    [Parameter]
    public string PageRoute { get; set; }
}
```

<span data-ttu-id="c4e4d-200">Per l'URL `/page/this/is/a/test` con un modello di route di `/page/{*pageRoute}` , il valore di `PageRoute` è impostato su `this/is/a/test` .</span><span class="sxs-lookup"><span data-stu-id="c4e4d-200">For the URL `/page/this/is/a/test` with a route template of `/page/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="c4e4d-201">Le barre e i segmenti del percorso acquisito vengono decodificati.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-201">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="c4e4d-202">Per un modello di route di `/page/{*pageRoute}` , l'URL `/page/this/is/a%2Ftest%2A` restituisce `this/is/a/test*` .</span><span class="sxs-lookup"><span data-stu-id="c4e4d-202">For a route template of `/page/{*pageRoute}`, the URL `/page/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="c4e4d-203">I parametri di route catch-all sono supportati in ASP.NET Core 5,0 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-203">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span>

::: moniker-end

## <a name="navlink-component"></a><span data-ttu-id="c4e4d-204">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="c4e4d-204">NavLink component</span></span>

<span data-ttu-id="c4e4d-205">Usare un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente al posto di elementi collegamento ipertestuale HTML ( `<a>` ) durante la creazione di collegamenti di navigazione.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-205">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="c4e4d-206">Un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente si comporta come un `<a>` elemento, con la differenza che viene attivata o disattivata una `active` classe CSS a seconda che `href` corrisponda all'URL corrente.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-206">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="c4e4d-207">La `active` classe consente a un utente di comprendere quale pagina è la pagina attiva tra i collegamenti di navigazione visualizzati.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-207">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="c4e4d-208">Facoltativamente, assegnare un nome di classe CSS a <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> per applicare una classe CSS personalizzata al collegamento di cui è stato eseguito il rendering quando la route corrente corrisponde a `href` .</span><span class="sxs-lookup"><span data-stu-id="c4e4d-208">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="c4e4d-209">Il `NavMenu` componente seguente crea una [`Bootstrap`](https://getbootstrap.com/docs/) barra di navigazione che illustra come utilizzare i <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componenti di:</span><span class="sxs-lookup"><span data-stu-id="c4e4d-209">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="c4e4d-210">Sono disponibili due <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> opzioni che è possibile assegnare all' `Match` attributo dell' `<NavLink>` elemento:</span><span class="sxs-lookup"><span data-stu-id="c4e4d-210">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="c4e4d-211"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> È attivo quando corrisponde all'intero URL corrente.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-211"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="c4e4d-212"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*impostazione predefinita*): l'oggetto <xref:Microsoft.AspNetCore.Components.Routing.NavLink> è attivo quando corrisponde a qualsiasi prefisso dell'URL corrente.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-212"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="c4e4d-213">Nell'esempio precedente la Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` corrisponde all'URL Home e riceve solo la `active` classe CSS nell'URL del percorso di base predefinito dell'app (ad esempio, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="c4e4d-213">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="c4e4d-214">Il secondo <xref:Microsoft.AspNetCore.Components.Routing.NavLink> riceve la `active` classe quando l'utente visita un URL con un `MyComponent` prefisso, ad esempio `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment` .</span><span class="sxs-lookup"><span data-stu-id="c4e4d-214">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="c4e4d-215"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Gli attributi dei componenti aggiuntivi vengono passati al tag di ancoraggio sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-215">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="c4e4d-216">Nell'esempio seguente il <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente include l' `target` attributo:</span><span class="sxs-lookup"><span data-stu-id="c4e4d-216">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="c4e4d-217">Viene eseguito il rendering del markup HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="c4e4d-217">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> <span data-ttu-id="c4e4d-218">A causa del modo in cui Blazor viene eseguito il rendering del contenuto figlio, `NavLink` i componenti di rendering all'interno di un `for` ciclo richiedono una variabile di indice locale se la variabile del ciclo di incremento viene utilizzata nel `NavLink` contenuto del componente (figlio):</span><span class="sxs-lookup"><span data-stu-id="c4e4d-218">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> <span data-ttu-id="c4e4d-219">L'uso di una variabile di indice in questo scenario è un requisito per **qualsiasi** componente figlio che usa una variabile di ciclo nel [contenuto figlio](xref:blazor/components/index#child-content), non solo nel `NavLink` componente.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-219">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="c4e4d-220">In alternativa, usare un `foreach` ciclo con <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="c4e4d-220">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="c4e4d-221">Helper per lo stato di navigazione e URI</span><span class="sxs-lookup"><span data-stu-id="c4e4d-221">URI and navigation state helpers</span></span>

<span data-ttu-id="c4e4d-222">Usare <xref:Microsoft.AspNetCore.Components.NavigationManager> per lavorare con gli URI e la navigazione nel codice C#.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-222">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="c4e4d-223"><xref:Microsoft.AspNetCore.Components.NavigationManager> fornisce l'evento e i metodi illustrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-223"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="c4e4d-224">Membro</span><span class="sxs-lookup"><span data-stu-id="c4e4d-224">Member</span></span> | <span data-ttu-id="c4e4d-225">Descrizione</span><span class="sxs-lookup"><span data-stu-id="c4e4d-225">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="c4e4d-226">Ottiene l'URI assoluto corrente.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-226">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="c4e4d-227">Ottiene l'URI di base (con una barra finale) che può essere anteposto ai percorsi URI relativi per produrre un URI assoluto.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-227">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="c4e4d-228">In genere, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corrisponde all' `href` attributo sull'elemento del documento `<base>` in `wwwroot/index.html` ( Blazor WebAssembly ) o `Pages/_Host.cshtml` ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="c4e4d-228">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="c4e4d-229">Passa all'URI specificato.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-229">Navigates to the specified URI.</span></span> <span data-ttu-id="c4e4d-230">Se `forceLoad` è `true` :</span><span class="sxs-lookup"><span data-stu-id="c4e4d-230">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="c4e4d-231">Il routing lato client viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-231">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="c4e4d-232">Il browser è forzato a caricare la nuova pagina dal server, indipendentemente dal fatto che l'URI venga normalmente gestito dal router lato client.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-232">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="c4e4d-233">Un evento che viene attivato quando il percorso di navigazione viene modificato.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-233">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="c4e4d-234">Converte un URI relativo in un URI assoluto.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-234">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="c4e4d-235">Dato un URI di base (ad esempio, un URI restituito in precedenza da <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), converte un URI assoluto in un URI relativo al prefisso URI di base.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-235">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="c4e4d-236">`Counter`Quando si seleziona il pulsante, il componente seguente passa al componente dell'app:</span><span class="sxs-lookup"><span data-stu-id="c4e4d-236">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

<span data-ttu-id="c4e4d-237">Il componente seguente gestisce un evento di modifica della posizione sottoscrivendo <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="c4e4d-237">The following component handles a location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="c4e4d-238">Il `HandleLocationChanged` metodo è unhooked quando `Dispose` viene chiamato dal Framework.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-238">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="c4e4d-239">L'unhook del metodo consente Garbage Collection del componente.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-239">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="c4e4d-240"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> fornisce le informazioni seguenti sull'evento:</span><span class="sxs-lookup"><span data-stu-id="c4e4d-240"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="c4e4d-241"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: URL della nuova posizione.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-241"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="c4e4d-242"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Se `true` , Blazor intercetta la navigazione dal browser.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-242"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="c4e4d-243">Se `false` , <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> ha causato la navigazione.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-243">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="c4e4d-244">Per ulteriori informazioni sull'eliminazione dei componenti, vedere <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="c4e4d-244">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="c4e4d-245">Stringa di query e parametri di analisi</span><span class="sxs-lookup"><span data-stu-id="c4e4d-245">Query string and parse parameters</span></span>

<span data-ttu-id="c4e4d-246">La stringa di query di una richiesta può essere ottenuta <xref:Microsoft.AspNetCore.Components.NavigationManager> dalla <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> proprietà di:</span><span class="sxs-lookup"><span data-stu-id="c4e4d-246">The query string of a request can be obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager>'s <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="c4e4d-247">Per analizzare i parametri della stringa di query:</span><span class="sxs-lookup"><span data-stu-id="c4e4d-247">To parse a query string's parameters:</span></span>

* <span data-ttu-id="c4e4d-248">Aggiungere un riferimento al pacchetto per [Microsoft. AspNetCore. webutilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span><span class="sxs-lookup"><span data-stu-id="c4e4d-248">Add a package reference for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="c4e4d-249">Ottenere il valore dopo aver analizzato la stringa di query con <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="c4e4d-249">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

<span data-ttu-id="c4e4d-250">Il segnaposto `{KEY}` nell'esempio precedente è la chiave del parametro della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="c4e4d-250">The placeholder `{KEY}` in the preceding example is the query string parameter key.</span></span> <span data-ttu-id="c4e4d-251">Ad esempio, la coppia chiave-valore dell'URL `?ship=Tardis` utilizza una chiave di `ship` .</span><span class="sxs-lookup"><span data-stu-id="c4e4d-251">For example, the URL key-value pair `?ship=Tardis` uses a key of `ship`.</span></span>
