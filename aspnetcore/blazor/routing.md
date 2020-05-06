---
title: Routing Blazor ASP.NET Core
author: guardrex
description: Informazioni su come instradare le richieste nelle app e sul componente NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 017fd4d3ab45b75355dabb400ff0e5cbf7009d82
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771207"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="595d1-103">Routing di ASP.NET Core Blazer</span><span class="sxs-lookup"><span data-stu-id="595d1-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="595d1-104">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="595d1-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="595d1-105">Informazioni su come instradare le richieste e su `NavLink` come usare il componente per creare collegamenti di navigazione nelle app blazer.</span><span class="sxs-lookup"><span data-stu-id="595d1-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="595d1-106">Integrazione di routing endpoint ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="595d1-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="595d1-107">Il server blazer è integrato nel [Routing ASP.NET Core endpoint](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="595d1-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="595d1-108">Un'app ASP.NET Core è configurata in modo da accettare le connessioni in `MapBlazorHub` ingresso `Startup.Configure`per i componenti interattivi con in:</span><span class="sxs-lookup"><span data-stu-id="595d1-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="595d1-109">La configurazione più tipica consiste nel routing di tutte le richieste a una pagina Razor, che funge da host per la parte lato server dell'app del server Blaze.</span><span class="sxs-lookup"><span data-stu-id="595d1-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="595d1-110">Per convenzione, la pagina *host* è in genere denominata *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="595d1-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="595d1-111">La route specificata nel file host viene chiamata route di *fallback* perché funziona con una priorità bassa nella corrispondenza della route.</span><span class="sxs-lookup"><span data-stu-id="595d1-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="595d1-112">La route di fallback viene considerata quando altre route non corrispondono.</span><span class="sxs-lookup"><span data-stu-id="595d1-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="595d1-113">Ciò consente all'app di usare altri controller e pagine senza interferire con l'app del server Blaze.</span><span class="sxs-lookup"><span data-stu-id="595d1-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="595d1-114">Modelli di route</span><span class="sxs-lookup"><span data-stu-id="595d1-114">Route templates</span></span>

<span data-ttu-id="595d1-115">Il `Router` componente consente il routing a ogni componente con una route specificata.</span><span class="sxs-lookup"><span data-stu-id="595d1-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="595d1-116">Il `Router` componente viene visualizzato nel file *app. Razor* :</span><span class="sxs-lookup"><span data-stu-id="595d1-116">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="595d1-117">Quando viene compilato un file *Razor* con `@page` una direttiva, alla classe generata viene fornito un oggetto <xref:Microsoft.AspNetCore.Components.RouteAttribute> che specifica il modello di route.</span><span class="sxs-lookup"><span data-stu-id="595d1-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="595d1-118">In fase di esecuzione `RouteView` , il componente:</span><span class="sxs-lookup"><span data-stu-id="595d1-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="595d1-119">Riceve `RouteData` da `Router` insieme a tutti i parametri desiderati.</span><span class="sxs-lookup"><span data-stu-id="595d1-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="595d1-120">Esegue il rendering del componente specificato con il layout (o un layout predefinito facoltativo) utilizzando i parametri specificati.</span><span class="sxs-lookup"><span data-stu-id="595d1-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="595d1-121">Facoltativamente, è possibile specificare `DefaultLayout` un parametro con una classe layout da utilizzare per i componenti che non specificano un layout.</span><span class="sxs-lookup"><span data-stu-id="595d1-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="595d1-122">I modelli di Blazer predefiniti specificano il `MainLayout` componente.</span><span class="sxs-lookup"><span data-stu-id="595d1-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="595d1-123">*MainLayout. Razor* si trova nella cartella *condivisa* del progetto modello.</span><span class="sxs-lookup"><span data-stu-id="595d1-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="595d1-124">Per ulteriori informazioni sui layout, vedere <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="595d1-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="595d1-125">È possibile applicare più modelli di route a un componente.</span><span class="sxs-lookup"><span data-stu-id="595d1-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="595d1-126">Il componente seguente risponde alle richieste per `/BlazorRoute` e: `/DifferentBlazorRoute`</span><span class="sxs-lookup"><span data-stu-id="595d1-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="595d1-127">Per la corretta risoluzione degli URL, è necessario che l' `<base>` app includa un tag nel file *wwwroot/index.html* (webassembly Blazer) o nel file *pages/_Host. cshtml* (server Blazer) con il percorso `href` di base`<base href="/">`dell'app specificato nell'attributo ().</span><span class="sxs-lookup"><span data-stu-id="595d1-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="595d1-128">Per altre informazioni, vedere <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="595d1-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="595d1-129">Fornire contenuto personalizzato quando il contenuto non è stato trovato</span><span class="sxs-lookup"><span data-stu-id="595d1-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="595d1-130">Il `Router` componente consente all'app di specificare contenuto personalizzato se non viene trovato contenuto per la route richiesta.</span><span class="sxs-lookup"><span data-stu-id="595d1-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="595d1-131">Nel file *app. Razor* impostare il contenuto personalizzato nel parametro del `NotFound` modello del `Router` componente:</span><span class="sxs-lookup"><span data-stu-id="595d1-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="595d1-132">Il contenuto dei `<NotFound>` tag può includere elementi arbitrari, ad esempio altri componenti interattivi.</span><span class="sxs-lookup"><span data-stu-id="595d1-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="595d1-133">Per applicare un layout predefinito al `NotFound` contenuto, vedere <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="595d1-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="595d1-134">Routing a componenti da più assembly</span><span class="sxs-lookup"><span data-stu-id="595d1-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="595d1-135">Usare il `AdditionalAssemblies` parametro per specificare gli assembly aggiuntivi da `Router` considerare per il componente durante la ricerca di componenti instradabili.</span><span class="sxs-lookup"><span data-stu-id="595d1-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="595d1-136">Gli `AppAssembly`assembly specificati sono considerati oltre all'assembly specificato da.</span><span class="sxs-lookup"><span data-stu-id="595d1-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="595d1-137">Nell'esempio seguente `Component1` è un componente instradabile definito in una libreria di classi a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="595d1-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="595d1-138">Nell'esempio `AdditionalAssemblies` seguente viene restituito il supporto del `Component1`routing per:</span><span class="sxs-lookup"><span data-stu-id="595d1-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="595d1-139">Parametri di route</span><span class="sxs-lookup"><span data-stu-id="595d1-139">Route parameters</span></span>

<span data-ttu-id="595d1-140">Il router usa parametri di route per popolare i parametri del componente corrispondenti con lo stesso nome (senza distinzione tra maiuscole e minuscole):</span><span class="sxs-lookup"><span data-stu-id="595d1-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="595d1-141">I parametri facoltativi non sono supportati.</span><span class="sxs-lookup"><span data-stu-id="595d1-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="595d1-142">Nell' `@page` esempio precedente vengono applicate due direttive.</span><span class="sxs-lookup"><span data-stu-id="595d1-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="595d1-143">Il primo consente la navigazione al componente senza un parametro.</span><span class="sxs-lookup"><span data-stu-id="595d1-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="595d1-144">La seconda `@page` direttiva accetta il `{text}` parametro Route e assegna il valore alla `Text` proprietà.</span><span class="sxs-lookup"><span data-stu-id="595d1-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="595d1-145">Vincoli di route</span><span class="sxs-lookup"><span data-stu-id="595d1-145">Route constraints</span></span>

<span data-ttu-id="595d1-146">Un vincolo di route impone la corrispondenza tra i tipi in un segmento di route e un componente.</span><span class="sxs-lookup"><span data-stu-id="595d1-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="595d1-147">Nell'esempio seguente, la route per il `Users` componente corrisponde solo a se:</span><span class="sxs-lookup"><span data-stu-id="595d1-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="595d1-148">Un `Id` segmento di route è presente nell'URL della richiesta.</span><span class="sxs-lookup"><span data-stu-id="595d1-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="595d1-149">Il `Id` segmento è un numero intero`int`().</span><span class="sxs-lookup"><span data-stu-id="595d1-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="595d1-150">Sono disponibili i vincoli di route indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="595d1-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="595d1-151">Per ulteriori informazioni, vedere l'avviso sotto la tabella per i vincoli di route corrispondenti alla lingua inglese.</span><span class="sxs-lookup"><span data-stu-id="595d1-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="595d1-152">Vincolo</span><span class="sxs-lookup"><span data-stu-id="595d1-152">Constraint</span></span> | <span data-ttu-id="595d1-153">Esempio</span><span class="sxs-lookup"><span data-stu-id="595d1-153">Example</span></span>           | <span data-ttu-id="595d1-154">Esempi di corrispondenza</span><span class="sxs-lookup"><span data-stu-id="595d1-154">Example Matches</span></span>                                                                  | <span data-ttu-id="595d1-155">Invariante</span><span class="sxs-lookup"><span data-stu-id="595d1-155">Invariant</span></span><br><span data-ttu-id="595d1-156">culture</span><span class="sxs-lookup"><span data-stu-id="595d1-156">culture</span></span><br><span data-ttu-id="595d1-157">corrispondenti</span><span class="sxs-lookup"><span data-stu-id="595d1-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="595d1-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="595d1-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="595d1-159">No</span><span class="sxs-lookup"><span data-stu-id="595d1-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="595d1-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="595d1-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="595d1-161">Sì</span><span class="sxs-lookup"><span data-stu-id="595d1-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="595d1-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="595d1-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="595d1-163">Sì</span><span class="sxs-lookup"><span data-stu-id="595d1-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="595d1-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="595d1-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="595d1-165">Sì</span><span class="sxs-lookup"><span data-stu-id="595d1-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="595d1-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="595d1-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="595d1-167">Sì</span><span class="sxs-lookup"><span data-stu-id="595d1-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="595d1-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="595d1-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="595d1-169">No</span><span class="sxs-lookup"><span data-stu-id="595d1-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="595d1-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="595d1-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="595d1-171">Sì</span><span class="sxs-lookup"><span data-stu-id="595d1-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="595d1-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="595d1-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="595d1-173">Sì</span><span class="sxs-lookup"><span data-stu-id="595d1-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="595d1-174">I vincoli di route che verificano l'URL e vengono convertiti in un tipo CLR, ad esempio `int` o `DateTime`, usano sempre le impostazioni cultura inglese non dipendenti da paese/area geografica,</span><span class="sxs-lookup"><span data-stu-id="595d1-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="595d1-175">presupponendo che l'URL sia non localizzabile.</span><span class="sxs-lookup"><span data-stu-id="595d1-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="595d1-176">Routing con URL contenenti punti</span><span class="sxs-lookup"><span data-stu-id="595d1-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="595d1-177">Nelle app del server blazer, la route predefinita in *_Host. cshtml* `/` è`@page "/"`().</span><span class="sxs-lookup"><span data-stu-id="595d1-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="595d1-178">Un URL di richiesta che contiene un punto`.`() non corrisponde alla route predefinita perché l'URL viene visualizzato per richiedere un file.</span><span class="sxs-lookup"><span data-stu-id="595d1-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="595d1-179">Un'app Blazer restituisce una risposta *404 non trovata* per un file statico che non esiste.</span><span class="sxs-lookup"><span data-stu-id="595d1-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="595d1-180">Per usare le route che contengono un punto, configurare *_Host. cshtml* con il modello di route seguente:</span><span class="sxs-lookup"><span data-stu-id="595d1-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="595d1-181">Il `"/{**path}"` modello include:</span><span class="sxs-lookup"><span data-stu-id="595d1-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="595d1-182">Double-asterisco *catch-all* Syntax (`**`) per acquisire il percorso tra più limiti di cartelle senza codificare le barre`/`().</span><span class="sxs-lookup"><span data-stu-id="595d1-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="595d1-183">`path`nome del parametro di route.</span><span class="sxs-lookup"><span data-stu-id="595d1-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="595d1-184">La sintassi dei parametri *catch-all* (`*`/`**`) **non** è supportata nei componenti Razor (*Razor*).</span><span class="sxs-lookup"><span data-stu-id="595d1-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="595d1-185">Per altre informazioni, vedere <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="595d1-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="595d1-186">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="595d1-186">NavLink component</span></span>

<span data-ttu-id="595d1-187">Usare un `NavLink` componente al posto di elementi collegamento ipertestuale`<a>`HTML () durante la creazione di collegamenti di navigazione.</span><span class="sxs-lookup"><span data-stu-id="595d1-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="595d1-188">Un `NavLink` componente si comporta come un `<a>` elemento, con la differenza che viene attivata `active` o disattivata una classe `href` CSS a seconda che corrisponda all'URL corrente.</span><span class="sxs-lookup"><span data-stu-id="595d1-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="595d1-189">La `active` classe consente a un utente di comprendere quale pagina è la pagina attiva tra i collegamenti di navigazione visualizzati.</span><span class="sxs-lookup"><span data-stu-id="595d1-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="595d1-190">Il componente `NavMenu` seguente crea una barra di navigazione [bootstrap](https://getbootstrap.com/docs/) che illustra come usare `NavLink` i componenti di:</span><span class="sxs-lookup"><span data-stu-id="595d1-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="595d1-191">Sono disponibili due `NavLinkMatch` opzioni che è possibile assegnare all' `Match` attributo dell' `<NavLink>` elemento:</span><span class="sxs-lookup"><span data-stu-id="595d1-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="595d1-192">`NavLinkMatch.All`&ndash; L' `NavLink` oggetto è attivo quando corrisponde all'intero URL corrente.</span><span class="sxs-lookup"><span data-stu-id="595d1-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="595d1-193">`NavLinkMatch.Prefix`(*impostazione predefinita*) &ndash; L' `NavLink` oggetto è attivo quando corrisponde a qualsiasi prefisso dell'URL corrente.</span><span class="sxs-lookup"><span data-stu-id="595d1-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="595d1-194">Nell'esempio precedente la Home `NavLink` `href=""` corrisponde all'URL Home e riceve solo la `active` classe CSS nell'URL del percorso di base predefinito dell'app (ad esempio, `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="595d1-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="595d1-195">Il secondo `NavLink` riceve la `active` classe quando l'utente visita un URL con un `MyComponent` prefisso, `https://localhost:5001/MyComponent` ad esempio e `https://localhost:5001/MyComponent/AnotherSegment`.</span><span class="sxs-lookup"><span data-stu-id="595d1-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="595d1-196">Gli `NavLink` attributi dei componenti aggiuntivi vengono passati al tag di ancoraggio sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="595d1-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="595d1-197">Nell'esempio seguente il `NavLink` componente include l' `target` attributo:</span><span class="sxs-lookup"><span data-stu-id="595d1-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="595d1-198">Viene eseguito il rendering del markup HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="595d1-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="595d1-199">Helper per lo stato di navigazione e URI</span><span class="sxs-lookup"><span data-stu-id="595d1-199">URI and navigation state helpers</span></span>

<span data-ttu-id="595d1-200">Usare <xref:Microsoft.AspNetCore.Components.NavigationManager> per lavorare con gli URI e la navigazione nel codice C#.</span><span class="sxs-lookup"><span data-stu-id="595d1-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="595d1-201">`NavigationManager`fornisce l'evento e i metodi illustrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="595d1-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="595d1-202">Membro</span><span class="sxs-lookup"><span data-stu-id="595d1-202">Member</span></span> | <span data-ttu-id="595d1-203">Description</span><span class="sxs-lookup"><span data-stu-id="595d1-203">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="595d1-204">Uri</span><span class="sxs-lookup"><span data-stu-id="595d1-204">Uri</span></span> | <span data-ttu-id="595d1-205">Ottiene l'URI assoluto corrente.</span><span class="sxs-lookup"><span data-stu-id="595d1-205">Gets the current absolute URI.</span></span> |
| <span data-ttu-id="595d1-206">BaseUri</span><span class="sxs-lookup"><span data-stu-id="595d1-206">BaseUri</span></span> | <span data-ttu-id="595d1-207">Ottiene l'URI di base (con una barra finale) che può essere anteposto ai percorsi URI relativi per produrre un URI assoluto.</span><span class="sxs-lookup"><span data-stu-id="595d1-207">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="595d1-208">In genere `BaseUri` , corrisponde all' `href` attributo sull' `<base>` elemento del documento in *wwwroot/index.html* Blazor (webassembly) o *pages/_Host. cshtml* (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="595d1-208">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <span data-ttu-id="595d1-209">NavigateTo</span><span class="sxs-lookup"><span data-stu-id="595d1-209">NavigateTo</span></span> | <span data-ttu-id="595d1-210">Passa all'URI specificato.</span><span class="sxs-lookup"><span data-stu-id="595d1-210">Navigates to the specified URI.</span></span> <span data-ttu-id="595d1-211">Se `forceLoad` è `true`:</span><span class="sxs-lookup"><span data-stu-id="595d1-211">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="595d1-212">Il routing lato client viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="595d1-212">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="595d1-213">Il browser è forzato a caricare la nuova pagina dal server, indipendentemente dal fatto che l'URI venga normalmente gestito dal router lato client.</span><span class="sxs-lookup"><span data-stu-id="595d1-213">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <span data-ttu-id="595d1-214">LocationChanged</span><span class="sxs-lookup"><span data-stu-id="595d1-214">LocationChanged</span></span> | <span data-ttu-id="595d1-215">Un evento che viene attivato quando il percorso di navigazione viene modificato.</span><span class="sxs-lookup"><span data-stu-id="595d1-215">An event that fires when the navigation location has changed.</span></span> |
| <span data-ttu-id="595d1-216">ToAbsoluteUri</span><span class="sxs-lookup"><span data-stu-id="595d1-216">ToAbsoluteUri</span></span> | <span data-ttu-id="595d1-217">Converte un URI relativo in un URI assoluto.</span><span class="sxs-lookup"><span data-stu-id="595d1-217">Converts a relative URI into an absolute URI.</span></span> |
| <span data-ttu-id="595d1-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span><span class="sxs-lookup"><span data-stu-id="595d1-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span></span> | <span data-ttu-id="595d1-219">Dato un URI di base (ad esempio, un URI restituito in `GetBaseUri`precedenza da), converte un URI assoluto in un URI relativo al prefisso URI di base.</span><span class="sxs-lookup"><span data-stu-id="595d1-219">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="595d1-220">Quando si seleziona il pulsante, il componente seguente `Counter` passa al componente dell'app:</span><span class="sxs-lookup"><span data-stu-id="595d1-220">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="595d1-221">Il componente seguente gestisce un evento di modifica della posizione.</span><span class="sxs-lookup"><span data-stu-id="595d1-221">The following component handles a location changed event.</span></span> <span data-ttu-id="595d1-222">Il `HandleLocationChanged` metodo è unhooked quando `Dispose` viene chiamato dal Framework.</span><span class="sxs-lookup"><span data-stu-id="595d1-222">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="595d1-223">L'unhook del metodo consente Garbage Collection del componente.</span><span class="sxs-lookup"><span data-stu-id="595d1-223">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="595d1-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fornisce le informazioni seguenti sull'evento:</span><span class="sxs-lookup"><span data-stu-id="595d1-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="595d1-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash; URL della nuova posizione.</span><span class="sxs-lookup"><span data-stu-id="595d1-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="595d1-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; Se `true`, Blazor intercetta la navigazione dal browser.</span><span class="sxs-lookup"><span data-stu-id="595d1-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="595d1-227">Se `false`, [NavigationManager. NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) ha causato la navigazione.</span><span class="sxs-lookup"><span data-stu-id="595d1-227">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="595d1-228">Per ulteriori informazioni sull'eliminazione dei componenti, <xref:blazor/lifecycle#component-disposal-with-idisposable>vedere.</span><span class="sxs-lookup"><span data-stu-id="595d1-228">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
