---
title: routing Blazor di ASP.NET Core
author: guardrex
description: Informazioni su come instradare le richieste nelle app e sul componente NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 87579c88a37e0258921e199db2b5d8c7627f5499
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218895"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="9f449-103">ASP.NET routing Blazor core</span><span class="sxs-lookup"><span data-stu-id="9f449-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="9f449-104">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9f449-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="9f449-105">Informazioni su come instradare `NavLink` le richieste e su come usare il componente per creare collegamenti di navigazione nelle app Blazor.</span><span class="sxs-lookup"><span data-stu-id="9f449-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="9f449-106">integrazione del routing degli endpoint ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9f449-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="9f449-107">Blazor Server è integrato in [ASP.NET core Endpoint Routing](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="9f449-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="9f449-108">Un'app ASP.NET Core è configurata per `MapBlazorHub` `Startup.Configure`accettare connessioni in ingresso per componenti interattivi con:</span><span class="sxs-lookup"><span data-stu-id="9f449-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="9f449-109">La configurazione più tipica consiste nell'instradare tutte le richieste a una pagina Razor, che funge da host per la parte lato server dell'app Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="9f449-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="9f449-110">Per convenzione, la pagina *host* è in genere denominata *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9f449-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="9f449-111">La route specificata nel file host viene definita route di *fallback* perché opera con una priorità bassa nella corrispondenza della route.</span><span class="sxs-lookup"><span data-stu-id="9f449-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="9f449-112">La route di fallback viene considerata quando altre route non corrispondono.</span><span class="sxs-lookup"><span data-stu-id="9f449-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="9f449-113">Ciò consente all'app di utilizzare altri controller e pagine senza interferire con l'app Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="9f449-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="9f449-114">Modelli di percorso</span><span class="sxs-lookup"><span data-stu-id="9f449-114">Route templates</span></span>

<span data-ttu-id="9f449-115">Il `Router` componente consente l'instradamento a ciascun componente con un percorso specificato.</span><span class="sxs-lookup"><span data-stu-id="9f449-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="9f449-116">Il `Router` componente viene visualizzato nel file *App.razor:*</span><span class="sxs-lookup"><span data-stu-id="9f449-116">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="9f449-117">Quando viene compilato un file *con estensione razor* con una `@page` direttiva, alla classe generata viene fornito un <xref:Microsoft.AspNetCore.Components.RouteAttribute> modello di route che specifica.</span><span class="sxs-lookup"><span data-stu-id="9f449-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="9f449-118">In fase `RouteView` di esecuzione, il componente:</span><span class="sxs-lookup"><span data-stu-id="9f449-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="9f449-119">Riceve il `RouteData` dal `Router` lungo con tutti i parametri desiderati.</span><span class="sxs-lookup"><span data-stu-id="9f449-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="9f449-120">Esegue il rendering del componente specificato con il relativo layout (o un layout predefinito facoltativo) utilizzando i parametri specificati.</span><span class="sxs-lookup"><span data-stu-id="9f449-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="9f449-121">Facoltativamente, è `DefaultLayout` possibile specificare un parametro con una classe di layout da utilizzare per i componenti che non specificano un layout.</span><span class="sxs-lookup"><span data-stu-id="9f449-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="9f449-122">I modelli Blazor `MainLayout` di default specificano il componente.</span><span class="sxs-lookup"><span data-stu-id="9f449-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="9f449-123">*MainLayout.razor* si trova nella cartella *Shared* del progetto modello.</span><span class="sxs-lookup"><span data-stu-id="9f449-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="9f449-124">Per ulteriori informazioni sui <xref:blazor/layouts>layout, vedere .</span><span class="sxs-lookup"><span data-stu-id="9f449-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="9f449-125">A un componente è possibile applicare più modelli di percorso.</span><span class="sxs-lookup"><span data-stu-id="9f449-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="9f449-126">Il componente seguente risponde `/BlazorRoute` alle `/DifferentBlazorRoute`richieste per e:</span><span class="sxs-lookup"><span data-stu-id="9f449-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="9f449-127">Affinché gli URL vengano risolti correttamente, l'app deve includere `<base>` un tag nel relativo file di *wwwroot/index.html* (Blazor WebAssembly) o nel file *Pages/_Host.cshtml* (Blazor Server) con il percorso di base dell'app specificato nell'attributo `href` ( ).`<base href="/">`</span><span class="sxs-lookup"><span data-stu-id="9f449-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="9f449-128">Per altre informazioni, vedere <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="9f449-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="9f449-129">Fornire contenuto personalizzato quando il contenuto non viene trovato</span><span class="sxs-lookup"><span data-stu-id="9f449-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="9f449-130">Il `Router` componente consente all'app di specificare contenuto personalizzato se il contenuto non viene trovato per la route richiesta.</span><span class="sxs-lookup"><span data-stu-id="9f449-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="9f449-131">Nel file *App.razor* impostare il `NotFound` contenuto personalizzato `Router` nel parametro template del componente:</span><span class="sxs-lookup"><span data-stu-id="9f449-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="9f449-132">Il contenuto `<NotFound>` dei tag può includere elementi arbitrari, ad esempio altri componenti interattivi.</span><span class="sxs-lookup"><span data-stu-id="9f449-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="9f449-133">Per applicare un `NotFound` layout predefinito <xref:blazor/layouts>al contenuto, vedere .</span><span class="sxs-lookup"><span data-stu-id="9f449-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="9f449-134">Indirizzamento ai componenti da più assiemi</span><span class="sxs-lookup"><span data-stu-id="9f449-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="9f449-135">Utilizzare `AdditionalAssemblies` il parametro per `Router` specificare assiemi aggiuntivi per il componente da considerare durante la ricerca di componenti instradabili.</span><span class="sxs-lookup"><span data-stu-id="9f449-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="9f449-136">Gli assembly specificati vengono `AppAssembly`considerati in aggiunta all'assembly specificato.</span><span class="sxs-lookup"><span data-stu-id="9f449-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="9f449-137">Nell'esempio seguente, `Component1` è un componente instradabile definito in una libreria di classi di riferimento.</span><span class="sxs-lookup"><span data-stu-id="9f449-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="9f449-138">L'esempio seguente determina il supporto del routing per:The following `AdditionalAssemblies` example results in routing support for: `Component1`</span><span class="sxs-lookup"><span data-stu-id="9f449-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="9f449-139">Parametri del percorso</span><span class="sxs-lookup"><span data-stu-id="9f449-139">Route parameters</span></span>

<span data-ttu-id="9f449-140">Il router utilizza i parametri di route per popolare i parametri del componente corrispondente con lo stesso nome (senza distinzione tra maiuscole e minuscole):</span><span class="sxs-lookup"><span data-stu-id="9f449-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="9f449-141">I parametri facoltativi non sono supportati.</span><span class="sxs-lookup"><span data-stu-id="9f449-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="9f449-142">Nell'esempio precedente vengono applicate due `@page` direttive.</span><span class="sxs-lookup"><span data-stu-id="9f449-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="9f449-143">Il primo consente la navigazione al componente senza un parametro.</span><span class="sxs-lookup"><span data-stu-id="9f449-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="9f449-144">La `@page` seconda direttiva accetta il `{text}` parametro `Text` route e assegna il valore alla proprietà.</span><span class="sxs-lookup"><span data-stu-id="9f449-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="9f449-145">Vincoli del percorso</span><span class="sxs-lookup"><span data-stu-id="9f449-145">Route constraints</span></span>

<span data-ttu-id="9f449-146">Un vincolo di route applica la corrispondenza dei tipi a un segmento di route a un componente.</span><span class="sxs-lookup"><span data-stu-id="9f449-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="9f449-147">Nell'esempio seguente, il `Users` percorso verso il componente corrisponde solo se:</span><span class="sxs-lookup"><span data-stu-id="9f449-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="9f449-148">Un `Id` segmento di route è presente nell'URL della richiesta.</span><span class="sxs-lookup"><span data-stu-id="9f449-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="9f449-149">Il `Id` segmento è`int`un numero intero ( ).</span><span class="sxs-lookup"><span data-stu-id="9f449-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="9f449-150">Sono disponibili i vincoli di route illustrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="9f449-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="9f449-151">Per i vincoli di route che corrispondono alle impostazioni cultura invarianti, vedere l'avviso sotto la tabella per ulteriori informazioni.</span><span class="sxs-lookup"><span data-stu-id="9f449-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="9f449-152">Vincolo</span><span class="sxs-lookup"><span data-stu-id="9f449-152">Constraint</span></span> | <span data-ttu-id="9f449-153">Esempio</span><span class="sxs-lookup"><span data-stu-id="9f449-153">Example</span></span>           | <span data-ttu-id="9f449-154">Esempi di corrispondenza</span><span class="sxs-lookup"><span data-stu-id="9f449-154">Example Matches</span></span>                                                                  | <span data-ttu-id="9f449-155">Invariante</span><span class="sxs-lookup"><span data-stu-id="9f449-155">Invariant</span></span><br><span data-ttu-id="9f449-156">culture</span><span class="sxs-lookup"><span data-stu-id="9f449-156">culture</span></span><br><span data-ttu-id="9f449-157">corrispondenti</span><span class="sxs-lookup"><span data-stu-id="9f449-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="9f449-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="9f449-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="9f449-159">No</span><span class="sxs-lookup"><span data-stu-id="9f449-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="9f449-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="9f449-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="9f449-161">Sì</span><span class="sxs-lookup"><span data-stu-id="9f449-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="9f449-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="9f449-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="9f449-163">Sì</span><span class="sxs-lookup"><span data-stu-id="9f449-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="9f449-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="9f449-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="9f449-165">Sì</span><span class="sxs-lookup"><span data-stu-id="9f449-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="9f449-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="9f449-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="9f449-167">Sì</span><span class="sxs-lookup"><span data-stu-id="9f449-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="9f449-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="9f449-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="9f449-169">No</span><span class="sxs-lookup"><span data-stu-id="9f449-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="9f449-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="9f449-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="9f449-171">Sì</span><span class="sxs-lookup"><span data-stu-id="9f449-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="9f449-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="9f449-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="9f449-173">Sì</span><span class="sxs-lookup"><span data-stu-id="9f449-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="9f449-174">I vincoli di route che verificano l'URL e vengono convertiti in un tipo CLR, ad esempio `int` o `DateTime`, usano sempre le impostazioni cultura inglese non dipendenti da paese/area geografica,</span><span class="sxs-lookup"><span data-stu-id="9f449-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="9f449-175">presupponendo che l'URL sia non localizzabile.</span><span class="sxs-lookup"><span data-stu-id="9f449-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="9f449-176">Routing con URL che contengono punti</span><span class="sxs-lookup"><span data-stu-id="9f449-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="9f449-177">Nelle app del server Blazor, la route `/` `@page "/"`predefinita in *_Host.cshtml* è ( ).</span><span class="sxs-lookup"><span data-stu-id="9f449-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="9f449-178">Un URL di richiesta`.`che contiene un punto ( ) non corrisponde alla route predefinita perché l'URL sembra richiedere un file.</span><span class="sxs-lookup"><span data-stu-id="9f449-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="9f449-179">Un'app Blazor restituisce una risposta *404 - Non trovata* per un file statico che non esiste.</span><span class="sxs-lookup"><span data-stu-id="9f449-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="9f449-180">Per utilizzare route che contengono un punto, configurare *_Host.cshtml* con il modello di route seguente:</span><span class="sxs-lookup"><span data-stu-id="9f449-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="9f449-181">Il `"/{**path}"` modello include:</span><span class="sxs-lookup"><span data-stu-id="9f449-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="9f449-182">Sintassi *catch-all* a`**`doppio asterisco ( ) per acquisire`/`il percorso attraverso più limiti di cartella senza codificare le barre ( ).</span><span class="sxs-lookup"><span data-stu-id="9f449-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="9f449-183">`path`nome del parametro del percorso.</span><span class="sxs-lookup"><span data-stu-id="9f449-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="9f449-184">La sintassi dei`*`/`**`parametri *Catch-all* ( ) **non** è supportata nei componenti Razor (*.razor*).</span><span class="sxs-lookup"><span data-stu-id="9f449-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="9f449-185">Per altre informazioni, vedere <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="9f449-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="9f449-186">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="9f449-186">NavLink component</span></span>

<span data-ttu-id="9f449-187">Utilizzare `NavLink` un componente al posto`<a>`degli elementi dei collegamenti ipertestuali HTML ( ) durante la creazione dei collegamenti di navigazione.</span><span class="sxs-lookup"><span data-stu-id="9f449-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="9f449-188">Un `NavLink` componente si comporta `<a>` come un elemento, `active` ad eccezione `href` del fatto che attiva o disattiva una classe CSS in base al fatto che corrisponda all'URL corrente.</span><span class="sxs-lookup"><span data-stu-id="9f449-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="9f449-189">La `active` classe consente a un utente di comprendere quale pagina è la pagina attiva tra i collegamenti di navigazione visualizzati.</span><span class="sxs-lookup"><span data-stu-id="9f449-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="9f449-190">Il `NavMenu` componente seguente crea una barra di `NavLink` navigazione [Bootstrap](https://getbootstrap.com/docs/) che dimostra come utilizzare i componenti:</span><span class="sxs-lookup"><span data-stu-id="9f449-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="9f449-191">Sono disponibili `NavLinkMatch` due opzioni che `Match` è possibile `<NavLink>` assegnare all'attributo dell'elemento:</span><span class="sxs-lookup"><span data-stu-id="9f449-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="9f449-192">`NavLinkMatch.All`&ndash; L'oggetto `NavLink` è attivo quando corrisponde all'intero URL corrente.</span><span class="sxs-lookup"><span data-stu-id="9f449-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="9f449-193">`NavLinkMatch.Prefix`(*predefinito*) &ndash; L'oggetto `NavLink` è attivo quando corrisponde a qualsiasi prefisso dell'URL corrente.</span><span class="sxs-lookup"><span data-stu-id="9f449-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="9f449-194">Nell'esempio precedente, Home `NavLink` `href=""` corrisponde all'URL principale `active` e riceve solo la classe CSS all'URL `https://localhost:5001/`del percorso di base predefinito dell'app, ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="9f449-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="9f449-195">Il `NavLink` secondo riceve `active` la classe quando l'utente visita qualsiasi URL con un `MyComponent` prefisso (ad esempio, `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="9f449-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="9f449-196">Gli `NavLink` attributi aggiuntivi del componente vengono passati al tag di ancoraggio sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="9f449-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="9f449-197">Nell'esempio seguente, `NavLink` il `target` componente include l'attributo:</span><span class="sxs-lookup"><span data-stu-id="9f449-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="9f449-198">Viene eseguito il rendering del markup HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="9f449-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="9f449-199">URI e helper dello stato di navigazione</span><span class="sxs-lookup"><span data-stu-id="9f449-199">URI and navigation state helpers</span></span>

<span data-ttu-id="9f449-200">Usare <xref:Microsoft.AspNetCore.Components.NavigationManager> per usare gli URI e la navigazione nel codice C.</span><span class="sxs-lookup"><span data-stu-id="9f449-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="9f449-201">`NavigationManager`fornisce l'evento e i metodi illustrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="9f449-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="9f449-202">Membro</span><span class="sxs-lookup"><span data-stu-id="9f449-202">Member</span></span> | <span data-ttu-id="9f449-203">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9f449-203">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="9f449-204">Uri</span><span class="sxs-lookup"><span data-stu-id="9f449-204">Uri</span></span> | <span data-ttu-id="9f449-205">Ottiene l'URI assoluto corrente.</span><span class="sxs-lookup"><span data-stu-id="9f449-205">Gets the current absolute URI.</span></span> |
| <span data-ttu-id="9f449-206">BaseUri</span><span class="sxs-lookup"><span data-stu-id="9f449-206">BaseUri</span></span> | <span data-ttu-id="9f449-207">Ottiene l'URI di base (con una barra finale) che può essere anteposto ai percorsi URI relativi per produrre un URI assoluto.</span><span class="sxs-lookup"><span data-stu-id="9f449-207">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="9f449-208">In `BaseUri` genere, `href` corrisponde all'attributo `<base>` nell'elementoBlazor del documento in *wwwroot/index.html* (Blazor WebAssembly) o *Pages/_Host.cshtml* ( Server).</span><span class="sxs-lookup"><span data-stu-id="9f449-208">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <span data-ttu-id="9f449-209">NavigateTo</span><span class="sxs-lookup"><span data-stu-id="9f449-209">NavigateTo</span></span> | <span data-ttu-id="9f449-210">Passa all'URI specificato.</span><span class="sxs-lookup"><span data-stu-id="9f449-210">Navigates to the specified URI.</span></span> <span data-ttu-id="9f449-211">Se `forceLoad` `true`è:</span><span class="sxs-lookup"><span data-stu-id="9f449-211">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="9f449-212">Il routing lato client viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="9f449-212">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="9f449-213">Il browser è costretto a caricare la nuova pagina dal server, indipendentemente dal fatto che l'URI venga normalmente gestito dal router sul lato client.</span><span class="sxs-lookup"><span data-stu-id="9f449-213">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <span data-ttu-id="9f449-214">LocationChanged</span><span class="sxs-lookup"><span data-stu-id="9f449-214">LocationChanged</span></span> | <span data-ttu-id="9f449-215">Evento che viene generato quando la posizione di navigazione viene modificata.</span><span class="sxs-lookup"><span data-stu-id="9f449-215">An event that fires when the navigation location has changed.</span></span> |
| <span data-ttu-id="9f449-216">AAbsoluteUri</span><span class="sxs-lookup"><span data-stu-id="9f449-216">ToAbsoluteUri</span></span> | <span data-ttu-id="9f449-217">Converte un URI relativo in un URI assoluto.</span><span class="sxs-lookup"><span data-stu-id="9f449-217">Converts a relative URI into an absolute URI.</span></span> |
| <span data-ttu-id="9f449-218"><span style="word-break:normal;word-wrap:normal">Percorso di base</span></span><span class="sxs-lookup"><span data-stu-id="9f449-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span></span> | <span data-ttu-id="9f449-219">Dato un URI di base (ad esempio, un URI restituito in precedenza da `GetBaseUri`), converte un URI assoluto in un URI relativo al prefisso URI di base.</span><span class="sxs-lookup"><span data-stu-id="9f449-219">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="9f449-220">Il componente seguente consente di `Counter` passare al componente dell'app quando viene selezionato il pulsante:</span><span class="sxs-lookup"><span data-stu-id="9f449-220">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="9f449-221">Il componente seguente gestisce un evento di modifica della posizione.</span><span class="sxs-lookup"><span data-stu-id="9f449-221">The following component handles a location changed event.</span></span> <span data-ttu-id="9f449-222">Il `HandleLocationChanged` metodo viene unhooked quando `Dispose` viene chiamato dal framework.</span><span class="sxs-lookup"><span data-stu-id="9f449-222">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="9f449-223">L'annullamento dell'hook del metodo consente la procedura di Garbage Collection del componente.</span><span class="sxs-lookup"><span data-stu-id="9f449-223">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implement IDisposable
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

<span data-ttu-id="9f449-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fornisce le seguenti informazioni sull'evento:</span><span class="sxs-lookup"><span data-stu-id="9f449-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="9f449-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash; URL del nuovo percorso.</span><span class="sxs-lookup"><span data-stu-id="9f449-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="9f449-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; Se `true` Blazor , è stata intercettata la navigazione dal browser.</span><span class="sxs-lookup"><span data-stu-id="9f449-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="9f449-227">Se `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) ha causato la navigazione.</span><span class="sxs-lookup"><span data-stu-id="9f449-227">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="9f449-228">Per ulteriori informazioni sullo <xref:blazor/lifecycle#component-disposal-with-idisposable>smaltimento dei componenti, vedere .</span><span class="sxs-lookup"><span data-stu-id="9f449-228">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
