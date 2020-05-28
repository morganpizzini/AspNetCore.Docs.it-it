---
<span data-ttu-id="ce9b6-101">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-102">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-103">'Identity'</span></span>
- <span data-ttu-id="ce9b6-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-105">'Razor'</span></span>
- <span data-ttu-id="ce9b6-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="ce9b6-107">BlazorRouting ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce9b6-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="ce9b6-108">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ce9b6-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ce9b6-109">Informazioni su come instradare le richieste e su come usare il <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente per creare collegamenti di navigazione nelle Blazor app.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-109">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="ce9b6-110">Integrazione di routing endpoint ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce9b6-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="ce9b6-111">Il server è integrato in [ASP.net core routing dell'endpoint](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="ce9b6-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="ce9b6-112">Un'app ASP.NET Core è configurata in modo da accettare le connessioni in ingresso per i componenti interattivi con <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="ce9b6-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="ce9b6-113">La configurazione più tipica consiste nel routing di tutte le richieste a una Razor pagina, che funge da host per la parte lato server dell' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="ce9b6-114">Per convenzione, la pagina *host* è in genere denominata *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="ce9b6-115">La route specificata nel file host viene chiamata route di *fallback* perché funziona con una priorità bassa nella corrispondenza della route.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="ce9b6-116">La route di fallback viene considerata quando altre route non corrispondono.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="ce9b6-117">Ciò consente all'app di usare altri controller e pagine senza interferire con l' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="ce9b6-118">Modelli di route</span><span class="sxs-lookup"><span data-stu-id="ce9b6-118">Route templates</span></span>

<span data-ttu-id="ce9b6-119">Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente consente il routing a ogni componente con una route specificata.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-119">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="ce9b6-120">Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente viene visualizzato nel file *app. Razor* :</span><span class="sxs-lookup"><span data-stu-id="ce9b6-120">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="ce9b6-121">Quando viene compilato un file *Razor* con una `@page` direttiva, alla classe generata viene fornito un oggetto che <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifica il modello di route.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="ce9b6-122">In fase di esecuzione, il <xref:Microsoft.AspNetCore.Components.RouteView> componente:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-122">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="ce9b6-123">Riceve <xref:Microsoft.AspNetCore.Components.RouteData> da <xref:Microsoft.AspNetCore.Components.Routing.Router> insieme a tutti i parametri desiderati.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-123">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="ce9b6-124">Esegue il rendering del componente specificato con il layout (o un layout predefinito facoltativo) utilizzando i parametri specificati.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="ce9b6-125">Facoltativamente, è possibile specificare un <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parametro con una classe layout da utilizzare per i componenti che non specificano un layout.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-125">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="ce9b6-126">I Blazor modelli predefiniti specificano il `MainLayout` componente.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="ce9b6-127">*MainLayout. Razor* si trova nella cartella *condivisa* del progetto modello.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="ce9b6-128">Per ulteriori informazioni sui layout, vedere <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="ce9b6-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="ce9b6-129">È possibile applicare più modelli di route a un componente.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="ce9b6-130">Il componente seguente risponde alle richieste per `/BlazorRoute` e `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="ce9b6-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="ce9b6-131">Per la corretta risoluzione degli URL, l'app deve includere un `<base>` tag nel file *wwwroot/index.html* ( Blazor webassembly) o nel file *pages/_Host. cshtml* ( Blazor Server) con il percorso di base dell'app specificato nell' `href` attributo ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="ce9b6-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="ce9b6-132">Per altre informazioni, vedere <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="ce9b6-133">Fornire contenuto personalizzato quando il contenuto non è stato trovato</span><span class="sxs-lookup"><span data-stu-id="ce9b6-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="ce9b6-134">Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente consente all'app di specificare contenuto personalizzato se non viene trovato contenuto per la route richiesta.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-134">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="ce9b6-135">Nel file *app. Razor* impostare il contenuto personalizzato nel <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> parametro del modello del <xref:Microsoft.AspNetCore.Components.Routing.Router> componente:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-135">In the *App.razor* file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="ce9b6-136">Il contenuto dei `<NotFound>` tag può includere elementi arbitrari, ad esempio altri componenti interattivi.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="ce9b6-137">Per applicare un layout predefinito al <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenuto, vedere <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="ce9b6-137">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="ce9b6-138">Routing a componenti da più assembly</span><span class="sxs-lookup"><span data-stu-id="ce9b6-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="ce9b6-139">Usare il <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parametro per specificare gli assembly aggiuntivi da <xref:Microsoft.AspNetCore.Components.Routing.Router> considerare per il componente durante la ricerca di componenti instradabili.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-139">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="ce9b6-140">Gli assembly specificati sono considerati oltre all' `AppAssembly` assembly specificato da.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="ce9b6-141">Nell'esempio seguente `Component1` è un componente instradabile definito in una libreria di classi a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="ce9b6-142">Nell' <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> esempio seguente viene restituito il supporto del routing per `Component1` :</span><span class="sxs-lookup"><span data-stu-id="ce9b6-142">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="ce9b6-143">Parametri di route</span><span class="sxs-lookup"><span data-stu-id="ce9b6-143">Route parameters</span></span>

<span data-ttu-id="ce9b6-144">Il router usa parametri di route per popolare i parametri del componente corrispondenti con lo stesso nome (senza distinzione tra maiuscole e minuscole):</span><span class="sxs-lookup"><span data-stu-id="ce9b6-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="ce9b6-145">I parametri facoltativi non sono supportati.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="ce9b6-146">`@page`Nell'esempio precedente vengono applicate due direttive.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="ce9b6-147">Il primo consente la navigazione al componente senza un parametro.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="ce9b6-148">La seconda `@page` direttiva accetta il `{text}` parametro Route e assegna il valore alla `Text` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="ce9b6-149">Vincoli di route</span><span class="sxs-lookup"><span data-stu-id="ce9b6-149">Route constraints</span></span>

<span data-ttu-id="ce9b6-150">Un vincolo di route impone la corrispondenza tra i tipi in un segmento di route e un componente.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="ce9b6-151">Nell'esempio seguente, la route per il `Users` componente corrisponde solo a se:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="ce9b6-152">Un `Id` segmento di route è presente nell'URL della richiesta.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="ce9b6-153">Il `Id` segmento è un numero intero ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="ce9b6-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="ce9b6-154">Sono disponibili i vincoli di route indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="ce9b6-155">Per ulteriori informazioni, vedere l'avviso sotto la tabella per i vincoli di route corrispondenti alla lingua inglese.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="ce9b6-156">Vincolo</span><span class="sxs-lookup"><span data-stu-id="ce9b6-156">Constraint</span></span> | <span data-ttu-id="ce9b6-157">Esempio</span><span class="sxs-lookup"><span data-stu-id="ce9b6-157">Example</span></span>           | <span data-ttu-id="ce9b6-158">Esempi di corrispondenza</span><span class="sxs-lookup"><span data-stu-id="ce9b6-158">Example Matches</span></span>                                                                  | <span data-ttu-id="ce9b6-159">Invariante</span><span class="sxs-lookup"><span data-stu-id="ce9b6-159">Invariant</span></span><br><span data-ttu-id="ce9b6-160">culture</span><span class="sxs-lookup"><span data-stu-id="ce9b6-160">culture</span></span><br><span data-ttu-id="ce9b6-161">corrispondenti</span><span class="sxs-lookup"><span data-stu-id="ce9b6-161">matching</span></span> |
| ---
<span data-ttu-id="ce9b6-162">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-163">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-164">'Identity'</span></span>
- <span data-ttu-id="ce9b6-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-166">'Razor'</span></span>
- <span data-ttu-id="ce9b6-167">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-168">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-169">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-170">'Identity'</span></span>
- <span data-ttu-id="ce9b6-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-172">'Razor'</span></span>
- <span data-ttu-id="ce9b6-173">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-174">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-175">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-176">'Identity'</span></span>
- <span data-ttu-id="ce9b6-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-178">'Razor'</span></span>
- <span data-ttu-id="ce9b6-179">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-179">'SignalR' uid:</span></span> 

<span data-ttu-id="ce9b6-180">----- | Titolo---:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-181">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-182">'Identity'</span></span>
- <span data-ttu-id="ce9b6-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-184">'Razor'</span></span>
- <span data-ttu-id="ce9b6-185">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-186">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-187">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-188">'Identity'</span></span>
- <span data-ttu-id="ce9b6-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-190">'Razor'</span></span>
- <span data-ttu-id="ce9b6-191">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-192">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-193">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-194">'Identity'</span></span>
- <span data-ttu-id="ce9b6-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-196">'Razor'</span></span>
- <span data-ttu-id="ce9b6-197">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-198">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-199">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-200">'Identity'</span></span>
- <span data-ttu-id="ce9b6-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-202">'Razor'</span></span>
- <span data-ttu-id="ce9b6-203">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-204">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-205">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-206">'Identity'</span></span>
- <span data-ttu-id="ce9b6-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-208">'Razor'</span></span>
- <span data-ttu-id="ce9b6-209">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-210">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-211">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-212">'Identity'</span></span>
- <span data-ttu-id="ce9b6-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-214">'Razor'</span></span>
- <span data-ttu-id="ce9b6-215">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-215">'SignalR' uid:</span></span> 

<span data-ttu-id="ce9b6-216">--------- | Titolo---:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-217">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-218">'Identity'</span></span>
- <span data-ttu-id="ce9b6-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-220">'Razor'</span></span>
- <span data-ttu-id="ce9b6-221">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-222">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-223">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-224">'Identity'</span></span>
- <span data-ttu-id="ce9b6-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-226">'Razor'</span></span>
- <span data-ttu-id="ce9b6-227">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-228">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-229">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-230">'Identity'</span></span>
- <span data-ttu-id="ce9b6-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-232">'Razor'</span></span>
- <span data-ttu-id="ce9b6-233">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-234">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-235">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-236">'Identity'</span></span>
- <span data-ttu-id="ce9b6-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-238">'Razor'</span></span>
- <span data-ttu-id="ce9b6-239">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-240">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-241">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-242">'Identity'</span></span>
- <span data-ttu-id="ce9b6-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-244">'Razor'</span></span>
- <span data-ttu-id="ce9b6-245">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-246">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-247">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-248">'Identity'</span></span>
- <span data-ttu-id="ce9b6-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-250">'Razor'</span></span>
- <span data-ttu-id="ce9b6-251">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-252">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-253">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-254">'Identity'</span></span>
- <span data-ttu-id="ce9b6-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-256">'Razor'</span></span>
- <span data-ttu-id="ce9b6-257">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-258">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-259">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-260">'Identity'</span></span>
- <span data-ttu-id="ce9b6-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-262">'Razor'</span></span>
- <span data-ttu-id="ce9b6-263">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-264">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-265">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-266">'Identity'</span></span>
- <span data-ttu-id="ce9b6-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-268">'Razor'</span></span>
- <span data-ttu-id="ce9b6-269">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-270">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-271">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-272">'Identity'</span></span>
- <span data-ttu-id="ce9b6-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-274">'Razor'</span></span>
- <span data-ttu-id="ce9b6-275">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-276">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-277">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-278">'Identity'</span></span>
- <span data-ttu-id="ce9b6-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-280">'Razor'</span></span>
- <span data-ttu-id="ce9b6-281">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-282">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-283">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-284">'Identity'</span></span>
- <span data-ttu-id="ce9b6-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-286">'Razor'</span></span>
- <span data-ttu-id="ce9b6-287">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-288">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-289">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-290">'Identity'</span></span>
- <span data-ttu-id="ce9b6-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-292">'Razor'</span></span>
- <span data-ttu-id="ce9b6-293">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-294">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-295">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-296">'Identity'</span></span>
- <span data-ttu-id="ce9b6-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-298">'Razor'</span></span>
- <span data-ttu-id="ce9b6-299">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-300">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-301">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-302">'Identity'</span></span>
- <span data-ttu-id="ce9b6-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-304">'Razor'</span></span>
- <span data-ttu-id="ce9b6-305">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-306">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-307">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-308">'Identity'</span></span>
- <span data-ttu-id="ce9b6-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-310">'Razor'</span></span>
- <span data-ttu-id="ce9b6-311">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-312">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-313">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-314">'Identity'</span></span>
- <span data-ttu-id="ce9b6-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-316">'Razor'</span></span>
- <span data-ttu-id="ce9b6-317">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-318">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-319">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-320">'Identity'</span></span>
- <span data-ttu-id="ce9b6-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-322">'Razor'</span></span>
- <span data-ttu-id="ce9b6-323">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-324">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-325">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-326">'Identity'</span></span>
- <span data-ttu-id="ce9b6-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-328">'Razor'</span></span>
- <span data-ttu-id="ce9b6-329">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-330">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-331">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-332">'Identity'</span></span>
- <span data-ttu-id="ce9b6-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-334">'Razor'</span></span>
- <span data-ttu-id="ce9b6-335">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-336">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-337">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-338">'Identity'</span></span>
- <span data-ttu-id="ce9b6-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-340">'Razor'</span></span>
- <span data-ttu-id="ce9b6-341">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-342">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-343">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-344">'Identity'</span></span>
- <span data-ttu-id="ce9b6-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-346">'Razor'</span></span>
- <span data-ttu-id="ce9b6-347">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-348">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-349">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-350">'Identity'</span></span>
- <span data-ttu-id="ce9b6-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-352">'Razor'</span></span>
- <span data-ttu-id="ce9b6-353">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-354">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-355">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-356">'Identity'</span></span>
- <span data-ttu-id="ce9b6-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-358">'Razor'</span></span>
- <span data-ttu-id="ce9b6-359">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-360">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-361">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-362">'Identity'</span></span>
- <span data-ttu-id="ce9b6-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-364">'Razor'</span></span>
- <span data-ttu-id="ce9b6-365">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-366">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-367">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-368">'Identity'</span></span>
- <span data-ttu-id="ce9b6-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-370">'Razor'</span></span>
- <span data-ttu-id="ce9b6-371">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-372">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-373">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-374">'Identity'</span></span>
- <span data-ttu-id="ce9b6-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-376">'Razor'</span></span>
- <span data-ttu-id="ce9b6-377">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-378">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-379">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-380">'Identity'</span></span>
- <span data-ttu-id="ce9b6-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-382">'Razor'</span></span>
- <span data-ttu-id="ce9b6-383">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-384">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-385">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-386">'Identity'</span></span>
- <span data-ttu-id="ce9b6-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-388">'Razor'</span></span>
- <span data-ttu-id="ce9b6-389">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-390">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-391">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-392">'Identity'</span></span>
- <span data-ttu-id="ce9b6-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-394">'Razor'</span></span>
- <span data-ttu-id="ce9b6-395">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-396">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-397">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-398">'Identity'</span></span>
- <span data-ttu-id="ce9b6-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-400">'Razor'</span></span>
- <span data-ttu-id="ce9b6-401">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-402">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-403">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-404">'Identity'</span></span>
- <span data-ttu-id="ce9b6-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-406">'Razor'</span></span>
- <span data-ttu-id="ce9b6-407">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-408">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-409">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-410">'Identity'</span></span>
- <span data-ttu-id="ce9b6-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-412">'Razor'</span></span>
- <span data-ttu-id="ce9b6-413">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-414">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-415">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-416">'Identity'</span></span>
- <span data-ttu-id="ce9b6-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-418">'Razor'</span></span>
- <span data-ttu-id="ce9b6-419">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-420">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-421">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-422">'Identity'</span></span>
- <span data-ttu-id="ce9b6-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-424">'Razor'</span></span>
- <span data-ttu-id="ce9b6-425">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-426">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-427">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-428">'Identity'</span></span>
- <span data-ttu-id="ce9b6-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-430">'Razor'</span></span>
- <span data-ttu-id="ce9b6-431">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-432">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-433">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-434">'Identity'</span></span>
- <span data-ttu-id="ce9b6-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-436">'Razor'</span></span>
- <span data-ttu-id="ce9b6-437">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-438">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-439">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-440">'Identity'</span></span>
- <span data-ttu-id="ce9b6-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-442">'Razor'</span></span>
- <span data-ttu-id="ce9b6-443">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-443">'SignalR' uid:</span></span> 

<span data-ttu-id="ce9b6-444">---------------------------------------- | :---titolo:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-445">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-446">'Identity'</span></span>
- <span data-ttu-id="ce9b6-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-448">'Razor'</span></span>
- <span data-ttu-id="ce9b6-449">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-450">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-451">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-452">'Identity'</span></span>
- <span data-ttu-id="ce9b6-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-454">'Razor'</span></span>
- <span data-ttu-id="ce9b6-455">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-456">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-457">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-458">'Identity'</span></span>
- <span data-ttu-id="ce9b6-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-460">'Razor'</span></span>
- <span data-ttu-id="ce9b6-461">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-462">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-463">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-464">'Identity'</span></span>
- <span data-ttu-id="ce9b6-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-466">'Razor'</span></span>
- <span data-ttu-id="ce9b6-467">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-468">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-469">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-470">'Identity'</span></span>
- <span data-ttu-id="ce9b6-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-472">'Razor'</span></span>
- <span data-ttu-id="ce9b6-473">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-474">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-475">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-476">'Identity'</span></span>
- <span data-ttu-id="ce9b6-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-478">'Razor'</span></span>
- <span data-ttu-id="ce9b6-479">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-480">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-481">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-482">'Identity'</span></span>
- <span data-ttu-id="ce9b6-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-484">'Razor'</span></span>
- <span data-ttu-id="ce9b6-485">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-486">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-487">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-488">'Identity'</span></span>
- <span data-ttu-id="ce9b6-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-490">'Razor'</span></span>
- <span data-ttu-id="ce9b6-491">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-492">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-493">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-494">'Identity'</span></span>
- <span data-ttu-id="ce9b6-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-496">'Razor'</span></span>
- <span data-ttu-id="ce9b6-497">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-498">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-499">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-500">'Identity'</span></span>
- <span data-ttu-id="ce9b6-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-502">'Razor'</span></span>
- <span data-ttu-id="ce9b6-503">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-504">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-505">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-506">'Identity'</span></span>
- <span data-ttu-id="ce9b6-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-508">'Razor'</span></span>
- <span data-ttu-id="ce9b6-509">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-510">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-511">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-512">'Identity'</span></span>
- <span data-ttu-id="ce9b6-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-514">'Razor'</span></span>
- <span data-ttu-id="ce9b6-515">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-516">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-517">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-518">'Identity'</span></span>
- <span data-ttu-id="ce9b6-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-520">'Razor'</span></span>
- <span data-ttu-id="ce9b6-521">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-521">'SignalR' uid:</span></span> 

<span data-ttu-id="ce9b6-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Sì | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Sì | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Sì | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Sì | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Sì | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Sì |</span><span class="sxs-lookup"><span data-stu-id="ce9b6-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="ce9b6-523">I vincoli di route che verificano l'URL e vengono convertiti in un tipo CLR, ad esempio `int` o <xref:System.DateTime>, usano sempre le impostazioni cultura inglese non dipendenti da paese/area geografica,</span><span class="sxs-lookup"><span data-stu-id="ce9b6-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="ce9b6-524">presupponendo che l'URL sia non localizzabile.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="ce9b6-525">Routing con URL contenenti punti</span><span class="sxs-lookup"><span data-stu-id="ce9b6-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="ce9b6-526">Nelle Blazor app Server la route predefinita è *_Host. cshtml* è `/` ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="ce9b6-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="ce9b6-527">Un URL di richiesta che contiene un punto ( `.` ) non corrisponde alla route predefinita perché l'URL viene visualizzato per richiedere un file.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="ce9b6-528">Un' Blazor app restituisce una risposta *404 non trovata* per un file statico che non esiste.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="ce9b6-529">Per usare le route che contengono un punto, configurare *_Host. cshtml* con il modello di route seguente:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="ce9b6-530">Il `"/{**path}"` modello include:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="ce9b6-531">Double-asterisco *catch-all* Syntax ( `**` ) per acquisire il percorso tra più limiti di cartelle senza codificare le barre ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="ce9b6-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="ce9b6-532">`path`nome del parametro di route.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="ce9b6-533">La sintassi dei parametri *catch-all* ( `*` / `**` ) **non** è supportata nei Razor componenti (*Razor*).</span><span class="sxs-lookup"><span data-stu-id="ce9b6-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="ce9b6-534">Per altre informazioni, vedere <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="ce9b6-535">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="ce9b6-535">NavLink component</span></span>

<span data-ttu-id="ce9b6-536">Usare un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente al posto di elementi collegamento ipertestuale HTML ( `<a>` ) durante la creazione di collegamenti di navigazione.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-536">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="ce9b6-537">Un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente si comporta come un `<a>` elemento, con la differenza che viene attivata o disattivata una `active` classe CSS a seconda che `href` corrisponda all'URL corrente.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-537">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="ce9b6-538">La `active` classe consente a un utente di comprendere quale pagina è la pagina attiva tra i collegamenti di navigazione visualizzati.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="ce9b6-539">Il `NavMenu` componente seguente crea una barra di navigazione [bootstrap](https://getbootstrap.com/docs/) che illustra come usare i <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componenti di:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="ce9b6-540">Sono disponibili due <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> opzioni che è possibile assegnare all' `Match` attributo dell' `<NavLink>` elemento:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-540">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="ce9b6-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> È attivo quando corrisponde all'intero URL corrente.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="ce9b6-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(*impostazione predefinita*): l'oggetto <xref:Microsoft.AspNetCore.Components.Routing.NavLink> è attivo quando corrisponde a qualsiasi prefisso dell'URL corrente.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="ce9b6-543">Nell'esempio precedente la Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` corrisponde all'URL Home e riceve solo la `active` classe CSS nell'URL del percorso di base predefinito dell'app (ad esempio, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="ce9b6-543">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="ce9b6-544">Il secondo <xref:Microsoft.AspNetCore.Components.Routing.NavLink> riceve la `active` classe quando l'utente visita un URL con un `MyComponent` prefisso, ad esempio `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment` .</span><span class="sxs-lookup"><span data-stu-id="ce9b6-544">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="ce9b6-545"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Gli attributi dei componenti aggiuntivi vengono passati al tag di ancoraggio sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-545">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="ce9b6-546">Nell'esempio seguente il <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente include l' `target` attributo:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-546">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="ce9b6-547">Viene eseguito il rendering del markup HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="ce9b6-548">Helper per lo stato di navigazione e URI</span><span class="sxs-lookup"><span data-stu-id="ce9b6-548">URI and navigation state helpers</span></span>

<span data-ttu-id="ce9b6-549">Usare <xref:Microsoft.AspNetCore.Components.NavigationManager> per lavorare con gli URI e la navigazione nel codice C#.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="ce9b6-550"><xref:Microsoft.AspNetCore.Components.NavigationManager>fornisce l'evento e i metodi illustrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-550"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="ce9b6-551">Membro</span><span class="sxs-lookup"><span data-stu-id="ce9b6-551">Member</span></span> | <span data-ttu-id="ce9b6-552">Descrizione</span><span class="sxs-lookup"><span data-stu-id="ce9b6-552">Description</span></span> |
| ---
<span data-ttu-id="ce9b6-553">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-554">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-555">'Identity'</span></span>
- <span data-ttu-id="ce9b6-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-557">'Razor'</span></span>
- <span data-ttu-id="ce9b6-558">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-558">'SignalR' uid:</span></span> 

<span data-ttu-id="ce9b6-559">--- | Titolo---:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-560">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-561">'Identity'</span></span>
- <span data-ttu-id="ce9b6-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-563">'Razor'</span></span>
- <span data-ttu-id="ce9b6-564">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-565">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-566">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-567">'Identity'</span></span>
- <span data-ttu-id="ce9b6-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-569">'Razor'</span></span>
- <span data-ttu-id="ce9b6-570">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ce9b6-571">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ce9b6-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-572">'Blazor'</span></span>
- <span data-ttu-id="ce9b6-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-573">'Identity'</span></span>
- <span data-ttu-id="ce9b6-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="ce9b6-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ce9b6-575">'Razor'</span></span>
- <span data-ttu-id="ce9b6-576">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ce9b6-576">'SignalR' uid:</span></span> 

<span data-ttu-id="ce9b6-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Ottiene l'URI assoluto corrente.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Gets the current absolute URI.</span></span> <span data-ttu-id="ce9b6-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Ottiene l'URI di base (con una barra finale) che può essere anteposto ai percorsi URI relativi per produrre un URI assoluto.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="ce9b6-579">In genere, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corrisponde all' `href` attributo sull'elemento del documento `<base>` in *wwwroot/index.html* ( Blazor webassembly) o *pages/_Host. cshtml* ( Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="ce9b6-579">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="ce9b6-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Passa all'URI specificato.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navigates to the specified URI.</span></span> <span data-ttu-id="ce9b6-581">Se `forceLoad` è `true` :</span><span class="sxs-lookup"><span data-stu-id="ce9b6-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="ce9b6-582">Il routing lato client viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="ce9b6-583">Il browser è forzato a caricare la nuova pagina dal server, indipendentemente dal fatto che l'URI venga normalmente gestito dal router lato client.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="ce9b6-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Un evento che viene attivato quando il percorso di navigazione viene modificato.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="ce9b6-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Converte un URI relativo in un URI assoluto.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="ce9b6-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Dato un URI di base (ad esempio, un URI restituito in precedenza da <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), converte un URI assoluto in un URI relativo al prefisso URI di base.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="ce9b6-587">`Counter`Quando si seleziona il pulsante, il componente seguente passa al componente dell'app:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="ce9b6-588">Il componente seguente gestisce un evento di modifica della posizione impostando <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ce9b6-588">The following component handles a location changed event by setting <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ce9b6-589">Il `HandleLocationChanged` metodo è unhooked quando `Dispose` viene chiamato dal Framework.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="ce9b6-590">L'unhook del metodo consente Garbage Collection del componente.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-590">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="ce9b6-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fornisce le informazioni seguenti sull'evento:</span><span class="sxs-lookup"><span data-stu-id="ce9b6-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="ce9b6-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: URL della nuova posizione.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="ce9b6-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Se `true` , Blazor intercetta la navigazione dal browser.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="ce9b6-594">Se `false` , <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> ha causato la navigazione.</span><span class="sxs-lookup"><span data-stu-id="ce9b6-594">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="ce9b6-595">Per ulteriori informazioni sull'eliminazione dei componenti, vedere <xref:blazor/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="ce9b6-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
