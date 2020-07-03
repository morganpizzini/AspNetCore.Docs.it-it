---
title: BlazorRouting ASP.NET Core
author: guardrex
description: Informazioni su come instradare le richieste nelle app e sul componente NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/routing
ms.openlocfilehash: c41736e7c5a3e59a08b579de54f9810381c8df1c
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944178"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="f791a-103">BlazorRouting ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f791a-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="f791a-104">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f791a-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f791a-105">Informazioni su come instradare le richieste e su come usare il <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente per creare collegamenti di navigazione nelle Blazor app.</span><span class="sxs-lookup"><span data-stu-id="f791a-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="f791a-106">Integrazione di routing endpoint ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f791a-106">ASP.NET Core endpoint routing integration</span></span>

Blazor Server<span data-ttu-id="f791a-107">è integrato in [ASP.net core routing dell'endpoint](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="f791a-107"> is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="f791a-108">Un'app ASP.NET Core è configurata in modo da accettare le connessioni in ingresso per i componenti interattivi con <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f791a-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="f791a-109">La configurazione più tipica consiste nel routing di tutte le richieste a una Razor pagina, che funge da host per la parte lato server dell' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="f791a-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="f791a-110">Per convenzione, la pagina *host* è in genere denominata `_Host.cshtml` .</span><span class="sxs-lookup"><span data-stu-id="f791a-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="f791a-111">La route specificata nel file host viene chiamata route di *fallback* perché funziona con una priorità bassa nella corrispondenza della route.</span><span class="sxs-lookup"><span data-stu-id="f791a-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="f791a-112">La route di fallback viene considerata quando altre route non corrispondono.</span><span class="sxs-lookup"><span data-stu-id="f791a-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="f791a-113">Ciò consente all'app di usare altri controller e pagine senza interferire con l' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="f791a-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="f791a-114">Modelli di route</span><span class="sxs-lookup"><span data-stu-id="f791a-114">Route templates</span></span>

<span data-ttu-id="f791a-115">Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente consente il routing a ogni componente con una route specificata.</span><span class="sxs-lookup"><span data-stu-id="f791a-115">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="f791a-116">Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente viene visualizzato nel `App.razor` file:</span><span class="sxs-lookup"><span data-stu-id="f791a-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="f791a-117">Quando `.razor` viene compilato un file con una `@page` direttiva, alla classe generata viene fornito un oggetto che <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifica il modello di route.</span><span class="sxs-lookup"><span data-stu-id="f791a-117">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="f791a-118">In fase di esecuzione, il <xref:Microsoft.AspNetCore.Components.RouteView> componente:</span><span class="sxs-lookup"><span data-stu-id="f791a-118">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="f791a-119">Riceve <xref:Microsoft.AspNetCore.Components.RouteData> da <xref:Microsoft.AspNetCore.Components.Routing.Router> insieme a tutti i parametri desiderati.</span><span class="sxs-lookup"><span data-stu-id="f791a-119">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="f791a-120">Esegue il rendering del componente specificato con il layout (o un layout predefinito facoltativo) utilizzando i parametri specificati.</span><span class="sxs-lookup"><span data-stu-id="f791a-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="f791a-121">Facoltativamente, è possibile specificare un <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parametro con una classe layout da utilizzare per i componenti che non specificano un layout.</span><span class="sxs-lookup"><span data-stu-id="f791a-121">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="f791a-122">I Blazor modelli predefiniti specificano il `MainLayout` componente.</span><span class="sxs-lookup"><span data-stu-id="f791a-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="f791a-123">`MainLayout.razor`si trova nella cartella del progetto del modello `Shared` .</span><span class="sxs-lookup"><span data-stu-id="f791a-123">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="f791a-124">Per ulteriori informazioni sui layout, vedere <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="f791a-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="f791a-125">È possibile applicare più modelli di route a un componente.</span><span class="sxs-lookup"><span data-stu-id="f791a-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="f791a-126">Il componente seguente risponde alle richieste per `/BlazorRoute` e `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="f791a-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="f791a-127">Per la corretta risoluzione degli URL, l'app deve includere un `<base>` tag nel `wwwroot/index.html` file ( Blazor WebAssembly ) o nel `Pages/_Host.cshtml` file ( Blazor Server ) con il percorso di base dell'applicazione specificato nell' `href` attributo ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="f791a-127">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="f791a-128">Per altre informazioni, vedere <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="f791a-128">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="f791a-129">Fornire contenuto personalizzato quando il contenuto non è stato trovato</span><span class="sxs-lookup"><span data-stu-id="f791a-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="f791a-130">Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente consente all'app di specificare contenuto personalizzato se non viene trovato contenuto per la route richiesta.</span><span class="sxs-lookup"><span data-stu-id="f791a-130">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="f791a-131">Nel `App.razor` file, impostare il contenuto personalizzato nel <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> parametro di modello del <xref:Microsoft.AspNetCore.Components.Routing.Router> componente:</span><span class="sxs-lookup"><span data-stu-id="f791a-131">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="f791a-132">Il contenuto dei `<NotFound>` tag può includere elementi arbitrari, ad esempio altri componenti interattivi.</span><span class="sxs-lookup"><span data-stu-id="f791a-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="f791a-133">Per applicare un layout predefinito al <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenuto, vedere <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="f791a-133">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="f791a-134">Routing a componenti da più assembly</span><span class="sxs-lookup"><span data-stu-id="f791a-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="f791a-135">Usare il <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parametro per specificare gli assembly aggiuntivi da <xref:Microsoft.AspNetCore.Components.Routing.Router> considerare per il componente durante la ricerca di componenti instradabili.</span><span class="sxs-lookup"><span data-stu-id="f791a-135">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="f791a-136">Gli assembly specificati sono considerati oltre all' `AppAssembly` assembly specificato da.</span><span class="sxs-lookup"><span data-stu-id="f791a-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="f791a-137">Nell'esempio seguente `Component1` è un componente instradabile definito in una libreria di classi a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="f791a-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="f791a-138">Nell' <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> esempio seguente viene restituito il supporto del routing per `Component1` :</span><span class="sxs-lookup"><span data-stu-id="f791a-138">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="f791a-139">Parametri di route</span><span class="sxs-lookup"><span data-stu-id="f791a-139">Route parameters</span></span>

<span data-ttu-id="f791a-140">Il router usa parametri di route per popolare i parametri del componente corrispondenti con lo stesso nome (senza distinzione tra maiuscole e minuscole):</span><span class="sxs-lookup"><span data-stu-id="f791a-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="f791a-141">I parametri facoltativi non sono supportati.</span><span class="sxs-lookup"><span data-stu-id="f791a-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="f791a-142">`@page`Nell'esempio precedente vengono applicate due direttive.</span><span class="sxs-lookup"><span data-stu-id="f791a-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="f791a-143">Il primo consente la navigazione al componente senza un parametro.</span><span class="sxs-lookup"><span data-stu-id="f791a-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="f791a-144">La seconda `@page` direttiva accetta il `{text}` parametro Route e assegna il valore alla `Text` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="f791a-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="f791a-145">Vincoli di route</span><span class="sxs-lookup"><span data-stu-id="f791a-145">Route constraints</span></span>

<span data-ttu-id="f791a-146">Un vincolo di route impone la corrispondenza tra i tipi in un segmento di route e un componente.</span><span class="sxs-lookup"><span data-stu-id="f791a-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="f791a-147">Nell'esempio seguente, la route per il `Users` componente corrisponde solo a se:</span><span class="sxs-lookup"><span data-stu-id="f791a-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="f791a-148">Un `Id` segmento di route è presente nell'URL della richiesta.</span><span class="sxs-lookup"><span data-stu-id="f791a-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="f791a-149">Il `Id` segmento è un numero intero ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="f791a-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="f791a-150">Sono disponibili i vincoli di route indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="f791a-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="f791a-151">Per ulteriori informazioni, vedere l'avviso sotto la tabella per i vincoli di route corrispondenti alla lingua inglese.</span><span class="sxs-lookup"><span data-stu-id="f791a-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="f791a-152">Vincolo</span><span class="sxs-lookup"><span data-stu-id="f791a-152">Constraint</span></span> | <span data-ttu-id="f791a-153">Esempio</span><span class="sxs-lookup"><span data-stu-id="f791a-153">Example</span></span>           | <span data-ttu-id="f791a-154">Esempi di corrispondenza</span><span class="sxs-lookup"><span data-stu-id="f791a-154">Example Matches</span></span>                                                                  | <span data-ttu-id="f791a-155">Invariante</span><span class="sxs-lookup"><span data-stu-id="f791a-155">Invariant</span></span><br><span data-ttu-id="f791a-156">culture</span><span class="sxs-lookup"><span data-stu-id="f791a-156">culture</span></span><br><span data-ttu-id="f791a-157">corrispondenti</span><span class="sxs-lookup"><span data-stu-id="f791a-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="f791a-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="f791a-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="f791a-159">No</span><span class="sxs-lookup"><span data-stu-id="f791a-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="f791a-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="f791a-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="f791a-161">Sì</span><span class="sxs-lookup"><span data-stu-id="f791a-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="f791a-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="f791a-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="f791a-163">Sì</span><span class="sxs-lookup"><span data-stu-id="f791a-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="f791a-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="f791a-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="f791a-165">Sì</span><span class="sxs-lookup"><span data-stu-id="f791a-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="f791a-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="f791a-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="f791a-167">Sì</span><span class="sxs-lookup"><span data-stu-id="f791a-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="f791a-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="f791a-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="f791a-169">No</span><span class="sxs-lookup"><span data-stu-id="f791a-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="f791a-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="f791a-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="f791a-171">Sì</span><span class="sxs-lookup"><span data-stu-id="f791a-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="f791a-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="f791a-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="f791a-173">Sì</span><span class="sxs-lookup"><span data-stu-id="f791a-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="f791a-174">I vincoli di route che verificano l'URL e vengono convertiti in un tipo CLR, ad esempio `int` o <xref:System.DateTime>, usano sempre le impostazioni cultura inglese non dipendenti da paese/area geografica,</span><span class="sxs-lookup"><span data-stu-id="f791a-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="f791a-175">presupponendo che l'URL sia non localizzabile.</span><span class="sxs-lookup"><span data-stu-id="f791a-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="f791a-176">Routing con URL contenenti punti</span><span class="sxs-lookup"><span data-stu-id="f791a-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="f791a-177">Nelle Blazor Server app la route predefinita in `_Host.cshtml` è `/` ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="f791a-177">In Blazor Server apps, the default route in `_Host.cshtml` is `/` (`@page "/"`).</span></span> <span data-ttu-id="f791a-178">Un URL di richiesta che contiene un punto ( `.` ) non corrisponde alla route predefinita perché l'URL viene visualizzato per richiedere un file.</span><span class="sxs-lookup"><span data-stu-id="f791a-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="f791a-179">Un' Blazor app restituisce una risposta *404 non trovata* per un file statico che non esiste.</span><span class="sxs-lookup"><span data-stu-id="f791a-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="f791a-180">Per usare le route che contengono un punto, configurare `_Host.cshtml` con il modello di route seguente:</span><span class="sxs-lookup"><span data-stu-id="f791a-180">To use routes that contain a dot, configure `_Host.cshtml` with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="f791a-181">Il `"/{**path}"` modello include:</span><span class="sxs-lookup"><span data-stu-id="f791a-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="f791a-182">Double-asterisco *catch-all* Syntax ( `**` ) per acquisire il percorso tra più limiti di cartelle senza codificare le barre ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="f791a-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="f791a-183">`path`nome del parametro di route.</span><span class="sxs-lookup"><span data-stu-id="f791a-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="f791a-184">La sintassi dei parametri *catch-all* ( `*` / `**` ) **non** è supportata in Razor Components ( `.razor` ).</span><span class="sxs-lookup"><span data-stu-id="f791a-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (`.razor`).</span></span>

<span data-ttu-id="f791a-185">Per altre informazioni, vedere <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="f791a-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="f791a-186">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="f791a-186">NavLink component</span></span>

<span data-ttu-id="f791a-187">Usare un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente al posto di elementi collegamento ipertestuale HTML ( `<a>` ) durante la creazione di collegamenti di navigazione.</span><span class="sxs-lookup"><span data-stu-id="f791a-187">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="f791a-188">Un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente si comporta come un `<a>` elemento, con la differenza che viene attivata o disattivata una `active` classe CSS a seconda che `href` corrisponda all'URL corrente.</span><span class="sxs-lookup"><span data-stu-id="f791a-188">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="f791a-189">La `active` classe consente a un utente di comprendere quale pagina è la pagina attiva tra i collegamenti di navigazione visualizzati.</span><span class="sxs-lookup"><span data-stu-id="f791a-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="f791a-190">Facoltativamente, assegnare un nome di classe CSS a <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> per applicare una classe CSS personalizzata al collegamento di cui è stato eseguito il rendering quando la route corrente corrisponde a `href` .</span><span class="sxs-lookup"><span data-stu-id="f791a-190">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="f791a-191">Il `NavMenu` componente seguente crea una [`Bootstrap`](https://getbootstrap.com/docs/) barra di navigazione che illustra come utilizzare i <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componenti di:</span><span class="sxs-lookup"><span data-stu-id="f791a-191">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="f791a-192">Sono disponibili due <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> opzioni che è possibile assegnare all' `Match` attributo dell' `<NavLink>` elemento:</span><span class="sxs-lookup"><span data-stu-id="f791a-192">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="f791a-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> È attivo quando corrisponde all'intero URL corrente.</span><span class="sxs-lookup"><span data-stu-id="f791a-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="f791a-194"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(*impostazione predefinita*): l'oggetto <xref:Microsoft.AspNetCore.Components.Routing.NavLink> è attivo quando corrisponde a qualsiasi prefisso dell'URL corrente.</span><span class="sxs-lookup"><span data-stu-id="f791a-194"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="f791a-195">Nell'esempio precedente la Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` corrisponde all'URL Home e riceve solo la `active` classe CSS nell'URL del percorso di base predefinito dell'app (ad esempio, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="f791a-195">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="f791a-196">Il secondo <xref:Microsoft.AspNetCore.Components.Routing.NavLink> riceve la `active` classe quando l'utente visita un URL con un `MyComponent` prefisso, ad esempio `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment` .</span><span class="sxs-lookup"><span data-stu-id="f791a-196">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="f791a-197"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Gli attributi dei componenti aggiuntivi vengono passati al tag di ancoraggio sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="f791a-197">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="f791a-198">Nell'esempio seguente il <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente include l' `target` attributo:</span><span class="sxs-lookup"><span data-stu-id="f791a-198">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="f791a-199">Viene eseguito il rendering del markup HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="f791a-199">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="f791a-200">Helper per lo stato di navigazione e URI</span><span class="sxs-lookup"><span data-stu-id="f791a-200">URI and navigation state helpers</span></span>

<span data-ttu-id="f791a-201">Usare <xref:Microsoft.AspNetCore.Components.NavigationManager> per lavorare con gli URI e la navigazione nel codice C#.</span><span class="sxs-lookup"><span data-stu-id="f791a-201">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="f791a-202"><xref:Microsoft.AspNetCore.Components.NavigationManager>fornisce l'evento e i metodi illustrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="f791a-202"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="f791a-203">Membro</span><span class="sxs-lookup"><span data-stu-id="f791a-203">Member</span></span> | <span data-ttu-id="f791a-204">Description</span><span class="sxs-lookup"><span data-stu-id="f791a-204">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="f791a-205">Ottiene l'URI assoluto corrente.</span><span class="sxs-lookup"><span data-stu-id="f791a-205">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="f791a-206">Ottiene l'URI di base (con una barra finale) che può essere anteposto ai percorsi URI relativi per produrre un URI assoluto.</span><span class="sxs-lookup"><span data-stu-id="f791a-206">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="f791a-207">In genere, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corrisponde all' `href` attributo sull'elemento del documento `<base>` in `wwwroot/index.html` ( Blazor WebAssembly ) o `Pages/_Host.cshtml` ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="f791a-207">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="f791a-208">Passa all'URI specificato.</span><span class="sxs-lookup"><span data-stu-id="f791a-208">Navigates to the specified URI.</span></span> <span data-ttu-id="f791a-209">Se `forceLoad` è `true` :</span><span class="sxs-lookup"><span data-stu-id="f791a-209">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="f791a-210">Il routing lato client viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="f791a-210">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="f791a-211">Il browser è forzato a caricare la nuova pagina dal server, indipendentemente dal fatto che l'URI venga normalmente gestito dal router lato client.</span><span class="sxs-lookup"><span data-stu-id="f791a-211">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="f791a-212">Un evento che viene attivato quando il percorso di navigazione viene modificato.</span><span class="sxs-lookup"><span data-stu-id="f791a-212">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="f791a-213">Converte un URI relativo in un URI assoluto.</span><span class="sxs-lookup"><span data-stu-id="f791a-213">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="f791a-214">Dato un URI di base (ad esempio, un URI restituito in precedenza da <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), converte un URI assoluto in un URI relativo al prefisso URI di base.</span><span class="sxs-lookup"><span data-stu-id="f791a-214">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="f791a-215">`Counter`Quando si seleziona il pulsante, il componente seguente passa al componente dell'app:</span><span class="sxs-lookup"><span data-stu-id="f791a-215">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="f791a-216">Il componente seguente gestisce un evento di modifica della posizione sottoscrivendo <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="f791a-216">The following component handles a location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="f791a-217">Il `HandleLocationChanged` metodo è unhooked quando `Dispose` viene chiamato dal Framework.</span><span class="sxs-lookup"><span data-stu-id="f791a-217">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="f791a-218">L'unhook del metodo consente Garbage Collection del componente.</span><span class="sxs-lookup"><span data-stu-id="f791a-218">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="f791a-219"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fornisce le informazioni seguenti sull'evento:</span><span class="sxs-lookup"><span data-stu-id="f791a-219"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="f791a-220"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: URL della nuova posizione.</span><span class="sxs-lookup"><span data-stu-id="f791a-220"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="f791a-221"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Se `true` , Blazor intercetta la navigazione dal browser.</span><span class="sxs-lookup"><span data-stu-id="f791a-221"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="f791a-222">Se `false` , <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> ha causato la navigazione.</span><span class="sxs-lookup"><span data-stu-id="f791a-222">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="f791a-223">Per ulteriori informazioni sull'eliminazione dei componenti, vedere <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="f791a-223">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>
