---
<span data-ttu-id="eb399-101">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-102">'Blazor'</span></span>
- <span data-ttu-id="eb399-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-103">'Identity'</span></span>
- <span data-ttu-id="eb399-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-105">'Razor'</span></span>
- <span data-ttu-id="eb399-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="eb399-107">BlazorRouting ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb399-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="eb399-108">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="eb399-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="eb399-109">Informazioni su come instradare le richieste e su come usare il `NavLink` componente per creare collegamenti di navigazione nelle Blazor app.</span><span class="sxs-lookup"><span data-stu-id="eb399-109">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="eb399-110">Integrazione di routing endpoint ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb399-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="eb399-111">Il server è integrato in [ASP.net core routing dell'endpoint](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="eb399-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="eb399-112">Un'app ASP.NET Core è configurata in modo da accettare le connessioni in ingresso per i componenti interattivi con `MapBlazorHub` in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="eb399-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="eb399-113">La configurazione più tipica consiste nel routing di tutte le richieste a una Razor pagina, che funge da host per la parte lato server dell' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="eb399-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="eb399-114">Per convenzione, la pagina *host* è in genere denominata *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eb399-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="eb399-115">La route specificata nel file host viene chiamata route di *fallback* perché funziona con una priorità bassa nella corrispondenza della route.</span><span class="sxs-lookup"><span data-stu-id="eb399-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="eb399-116">La route di fallback viene considerata quando altre route non corrispondono.</span><span class="sxs-lookup"><span data-stu-id="eb399-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="eb399-117">Ciò consente all'app di usare altri controller e pagine senza interferire con l' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="eb399-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="eb399-118">Modelli di route</span><span class="sxs-lookup"><span data-stu-id="eb399-118">Route templates</span></span>

<span data-ttu-id="eb399-119">Il `Router` componente consente il routing a ogni componente con una route specificata.</span><span class="sxs-lookup"><span data-stu-id="eb399-119">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="eb399-120">Il `Router` componente viene visualizzato nel file *app. Razor* :</span><span class="sxs-lookup"><span data-stu-id="eb399-120">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="eb399-121">Quando viene compilato un file *Razor* con una `@page` direttiva, alla classe generata viene fornito un oggetto che <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifica il modello di route.</span><span class="sxs-lookup"><span data-stu-id="eb399-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="eb399-122">In fase di esecuzione, il `RouteView` componente:</span><span class="sxs-lookup"><span data-stu-id="eb399-122">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="eb399-123">Riceve `RouteData` da `Router` insieme a tutti i parametri desiderati.</span><span class="sxs-lookup"><span data-stu-id="eb399-123">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="eb399-124">Esegue il rendering del componente specificato con il layout (o un layout predefinito facoltativo) utilizzando i parametri specificati.</span><span class="sxs-lookup"><span data-stu-id="eb399-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="eb399-125">Facoltativamente, è possibile specificare un `DefaultLayout` parametro con una classe layout da utilizzare per i componenti che non specificano un layout.</span><span class="sxs-lookup"><span data-stu-id="eb399-125">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="eb399-126">I Blazor modelli predefiniti specificano il `MainLayout` componente.</span><span class="sxs-lookup"><span data-stu-id="eb399-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="eb399-127">*MainLayout. Razor* si trova nella cartella *condivisa* del progetto modello.</span><span class="sxs-lookup"><span data-stu-id="eb399-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="eb399-128">Per ulteriori informazioni sui layout, vedere <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="eb399-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="eb399-129">È possibile applicare più modelli di route a un componente.</span><span class="sxs-lookup"><span data-stu-id="eb399-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="eb399-130">Il componente seguente risponde alle richieste per `/BlazorRoute` e `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="eb399-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="eb399-131">Per la corretta risoluzione degli URL, l'app deve includere un `<base>` tag nel file *wwwroot/index.html* ( Blazor webassembly) o nel file *pages/_Host. cshtml* ( Blazor Server) con il percorso di base dell'app specificato nell' `href` attributo ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="eb399-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="eb399-132">Per altre informazioni, vedere <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="eb399-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="eb399-133">Fornire contenuto personalizzato quando il contenuto non è stato trovato</span><span class="sxs-lookup"><span data-stu-id="eb399-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="eb399-134">Il `Router` componente consente all'app di specificare contenuto personalizzato se non viene trovato contenuto per la route richiesta.</span><span class="sxs-lookup"><span data-stu-id="eb399-134">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="eb399-135">Nel file *app. Razor* impostare il contenuto personalizzato nel `NotFound` parametro del modello del `Router` componente:</span><span class="sxs-lookup"><span data-stu-id="eb399-135">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="eb399-136">Il contenuto dei `<NotFound>` tag può includere elementi arbitrari, ad esempio altri componenti interattivi.</span><span class="sxs-lookup"><span data-stu-id="eb399-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="eb399-137">Per applicare un layout predefinito al `NotFound` contenuto, vedere <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="eb399-137">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="eb399-138">Routing a componenti da più assembly</span><span class="sxs-lookup"><span data-stu-id="eb399-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="eb399-139">Usare il `AdditionalAssemblies` parametro per specificare gli assembly aggiuntivi da `Router` considerare per il componente durante la ricerca di componenti instradabili.</span><span class="sxs-lookup"><span data-stu-id="eb399-139">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="eb399-140">Gli assembly specificati sono considerati oltre all' `AppAssembly` assembly specificato da.</span><span class="sxs-lookup"><span data-stu-id="eb399-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="eb399-141">Nell'esempio seguente `Component1` è un componente instradabile definito in una libreria di classi a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="eb399-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="eb399-142">Nell' `AdditionalAssemblies` esempio seguente viene restituito il supporto del routing per `Component1` :</span><span class="sxs-lookup"><span data-stu-id="eb399-142">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="eb399-143">Parametri di route</span><span class="sxs-lookup"><span data-stu-id="eb399-143">Route parameters</span></span>

<span data-ttu-id="eb399-144">Il router usa parametri di route per popolare i parametri del componente corrispondenti con lo stesso nome (senza distinzione tra maiuscole e minuscole):</span><span class="sxs-lookup"><span data-stu-id="eb399-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="eb399-145">I parametri facoltativi non sono supportati.</span><span class="sxs-lookup"><span data-stu-id="eb399-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="eb399-146">`@page`Nell'esempio precedente vengono applicate due direttive.</span><span class="sxs-lookup"><span data-stu-id="eb399-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="eb399-147">Il primo consente la navigazione al componente senza un parametro.</span><span class="sxs-lookup"><span data-stu-id="eb399-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="eb399-148">La seconda `@page` direttiva accetta il `{text}` parametro Route e assegna il valore alla `Text` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="eb399-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="eb399-149">Vincoli di route</span><span class="sxs-lookup"><span data-stu-id="eb399-149">Route constraints</span></span>

<span data-ttu-id="eb399-150">Un vincolo di route impone la corrispondenza tra i tipi in un segmento di route e un componente.</span><span class="sxs-lookup"><span data-stu-id="eb399-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="eb399-151">Nell'esempio seguente, la route per il `Users` componente corrisponde solo a se:</span><span class="sxs-lookup"><span data-stu-id="eb399-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="eb399-152">Un `Id` segmento di route è presente nell'URL della richiesta.</span><span class="sxs-lookup"><span data-stu-id="eb399-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="eb399-153">Il `Id` segmento è un numero intero ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="eb399-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="eb399-154">Sono disponibili i vincoli di route indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="eb399-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="eb399-155">Per ulteriori informazioni, vedere l'avviso sotto la tabella per i vincoli di route corrispondenti alla lingua inglese.</span><span class="sxs-lookup"><span data-stu-id="eb399-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="eb399-156">Vincolo</span><span class="sxs-lookup"><span data-stu-id="eb399-156">Constraint</span></span> | <span data-ttu-id="eb399-157">Esempio</span><span class="sxs-lookup"><span data-stu-id="eb399-157">Example</span></span>           | <span data-ttu-id="eb399-158">Esempi di corrispondenza</span><span class="sxs-lookup"><span data-stu-id="eb399-158">Example Matches</span></span>                                                                  | <span data-ttu-id="eb399-159">Invariante</span><span class="sxs-lookup"><span data-stu-id="eb399-159">Invariant</span></span><br><span data-ttu-id="eb399-160">culture</span><span class="sxs-lookup"><span data-stu-id="eb399-160">culture</span></span><br><span data-ttu-id="eb399-161">corrispondenti</span><span class="sxs-lookup"><span data-stu-id="eb399-161">matching</span></span> |
| ---
<span data-ttu-id="eb399-162">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-163">'Blazor'</span></span>
- <span data-ttu-id="eb399-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-164">'Identity'</span></span>
- <span data-ttu-id="eb399-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-166">'Razor'</span></span>
- <span data-ttu-id="eb399-167">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-168">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-169">'Blazor'</span></span>
- <span data-ttu-id="eb399-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-170">'Identity'</span></span>
- <span data-ttu-id="eb399-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-172">'Razor'</span></span>
- <span data-ttu-id="eb399-173">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-174">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-175">'Blazor'</span></span>
- <span data-ttu-id="eb399-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-176">'Identity'</span></span>
- <span data-ttu-id="eb399-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-178">'Razor'</span></span>
- <span data-ttu-id="eb399-179">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-179">'SignalR' uid:</span></span> 

<span data-ttu-id="eb399-180">----- | Titolo---:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-181">'Blazor'</span></span>
- <span data-ttu-id="eb399-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-182">'Identity'</span></span>
- <span data-ttu-id="eb399-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-184">'Razor'</span></span>
- <span data-ttu-id="eb399-185">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-186">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-187">'Blazor'</span></span>
- <span data-ttu-id="eb399-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-188">'Identity'</span></span>
- <span data-ttu-id="eb399-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-190">'Razor'</span></span>
- <span data-ttu-id="eb399-191">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-192">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-193">'Blazor'</span></span>
- <span data-ttu-id="eb399-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-194">'Identity'</span></span>
- <span data-ttu-id="eb399-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-196">'Razor'</span></span>
- <span data-ttu-id="eb399-197">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-198">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-199">'Blazor'</span></span>
- <span data-ttu-id="eb399-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-200">'Identity'</span></span>
- <span data-ttu-id="eb399-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-202">'Razor'</span></span>
- <span data-ttu-id="eb399-203">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-204">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-205">'Blazor'</span></span>
- <span data-ttu-id="eb399-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-206">'Identity'</span></span>
- <span data-ttu-id="eb399-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-208">'Razor'</span></span>
- <span data-ttu-id="eb399-209">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-210">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-211">'Blazor'</span></span>
- <span data-ttu-id="eb399-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-212">'Identity'</span></span>
- <span data-ttu-id="eb399-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-214">'Razor'</span></span>
- <span data-ttu-id="eb399-215">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-215">'SignalR' uid:</span></span> 

<span data-ttu-id="eb399-216">--------- | Titolo---:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-217">'Blazor'</span></span>
- <span data-ttu-id="eb399-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-218">'Identity'</span></span>
- <span data-ttu-id="eb399-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-220">'Razor'</span></span>
- <span data-ttu-id="eb399-221">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-222">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-223">'Blazor'</span></span>
- <span data-ttu-id="eb399-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-224">'Identity'</span></span>
- <span data-ttu-id="eb399-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-226">'Razor'</span></span>
- <span data-ttu-id="eb399-227">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-228">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-229">'Blazor'</span></span>
- <span data-ttu-id="eb399-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-230">'Identity'</span></span>
- <span data-ttu-id="eb399-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-232">'Razor'</span></span>
- <span data-ttu-id="eb399-233">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-234">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-235">'Blazor'</span></span>
- <span data-ttu-id="eb399-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-236">'Identity'</span></span>
- <span data-ttu-id="eb399-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-238">'Razor'</span></span>
- <span data-ttu-id="eb399-239">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-240">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-241">'Blazor'</span></span>
- <span data-ttu-id="eb399-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-242">'Identity'</span></span>
- <span data-ttu-id="eb399-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-244">'Razor'</span></span>
- <span data-ttu-id="eb399-245">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-246">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-247">'Blazor'</span></span>
- <span data-ttu-id="eb399-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-248">'Identity'</span></span>
- <span data-ttu-id="eb399-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-250">'Razor'</span></span>
- <span data-ttu-id="eb399-251">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-252">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-253">'Blazor'</span></span>
- <span data-ttu-id="eb399-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-254">'Identity'</span></span>
- <span data-ttu-id="eb399-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-256">'Razor'</span></span>
- <span data-ttu-id="eb399-257">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-258">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-259">'Blazor'</span></span>
- <span data-ttu-id="eb399-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-260">'Identity'</span></span>
- <span data-ttu-id="eb399-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-262">'Razor'</span></span>
- <span data-ttu-id="eb399-263">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-264">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-265">'Blazor'</span></span>
- <span data-ttu-id="eb399-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-266">'Identity'</span></span>
- <span data-ttu-id="eb399-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-268">'Razor'</span></span>
- <span data-ttu-id="eb399-269">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-270">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-271">'Blazor'</span></span>
- <span data-ttu-id="eb399-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-272">'Identity'</span></span>
- <span data-ttu-id="eb399-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-274">'Razor'</span></span>
- <span data-ttu-id="eb399-275">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-276">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-277">'Blazor'</span></span>
- <span data-ttu-id="eb399-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-278">'Identity'</span></span>
- <span data-ttu-id="eb399-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-280">'Razor'</span></span>
- <span data-ttu-id="eb399-281">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-282">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-283">'Blazor'</span></span>
- <span data-ttu-id="eb399-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-284">'Identity'</span></span>
- <span data-ttu-id="eb399-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-286">'Razor'</span></span>
- <span data-ttu-id="eb399-287">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-288">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-289">'Blazor'</span></span>
- <span data-ttu-id="eb399-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-290">'Identity'</span></span>
- <span data-ttu-id="eb399-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-292">'Razor'</span></span>
- <span data-ttu-id="eb399-293">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-294">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-295">'Blazor'</span></span>
- <span data-ttu-id="eb399-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-296">'Identity'</span></span>
- <span data-ttu-id="eb399-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-298">'Razor'</span></span>
- <span data-ttu-id="eb399-299">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-300">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-301">'Blazor'</span></span>
- <span data-ttu-id="eb399-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-302">'Identity'</span></span>
- <span data-ttu-id="eb399-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-304">'Razor'</span></span>
- <span data-ttu-id="eb399-305">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-306">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-307">'Blazor'</span></span>
- <span data-ttu-id="eb399-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-308">'Identity'</span></span>
- <span data-ttu-id="eb399-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-310">'Razor'</span></span>
- <span data-ttu-id="eb399-311">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-312">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-313">'Blazor'</span></span>
- <span data-ttu-id="eb399-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-314">'Identity'</span></span>
- <span data-ttu-id="eb399-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-316">'Razor'</span></span>
- <span data-ttu-id="eb399-317">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-318">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-319">'Blazor'</span></span>
- <span data-ttu-id="eb399-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-320">'Identity'</span></span>
- <span data-ttu-id="eb399-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-322">'Razor'</span></span>
- <span data-ttu-id="eb399-323">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-324">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-325">'Blazor'</span></span>
- <span data-ttu-id="eb399-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-326">'Identity'</span></span>
- <span data-ttu-id="eb399-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-328">'Razor'</span></span>
- <span data-ttu-id="eb399-329">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-330">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-331">'Blazor'</span></span>
- <span data-ttu-id="eb399-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-332">'Identity'</span></span>
- <span data-ttu-id="eb399-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-334">'Razor'</span></span>
- <span data-ttu-id="eb399-335">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-336">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-337">'Blazor'</span></span>
- <span data-ttu-id="eb399-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-338">'Identity'</span></span>
- <span data-ttu-id="eb399-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-340">'Razor'</span></span>
- <span data-ttu-id="eb399-341">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-342">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-343">'Blazor'</span></span>
- <span data-ttu-id="eb399-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-344">'Identity'</span></span>
- <span data-ttu-id="eb399-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-346">'Razor'</span></span>
- <span data-ttu-id="eb399-347">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-348">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-349">'Blazor'</span></span>
- <span data-ttu-id="eb399-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-350">'Identity'</span></span>
- <span data-ttu-id="eb399-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-352">'Razor'</span></span>
- <span data-ttu-id="eb399-353">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-354">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-355">'Blazor'</span></span>
- <span data-ttu-id="eb399-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-356">'Identity'</span></span>
- <span data-ttu-id="eb399-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-358">'Razor'</span></span>
- <span data-ttu-id="eb399-359">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-360">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-361">'Blazor'</span></span>
- <span data-ttu-id="eb399-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-362">'Identity'</span></span>
- <span data-ttu-id="eb399-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-364">'Razor'</span></span>
- <span data-ttu-id="eb399-365">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-366">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-367">'Blazor'</span></span>
- <span data-ttu-id="eb399-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-368">'Identity'</span></span>
- <span data-ttu-id="eb399-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-370">'Razor'</span></span>
- <span data-ttu-id="eb399-371">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-372">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-373">'Blazor'</span></span>
- <span data-ttu-id="eb399-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-374">'Identity'</span></span>
- <span data-ttu-id="eb399-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-376">'Razor'</span></span>
- <span data-ttu-id="eb399-377">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-378">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-379">'Blazor'</span></span>
- <span data-ttu-id="eb399-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-380">'Identity'</span></span>
- <span data-ttu-id="eb399-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-382">'Razor'</span></span>
- <span data-ttu-id="eb399-383">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-384">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-385">'Blazor'</span></span>
- <span data-ttu-id="eb399-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-386">'Identity'</span></span>
- <span data-ttu-id="eb399-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-388">'Razor'</span></span>
- <span data-ttu-id="eb399-389">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-390">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-391">'Blazor'</span></span>
- <span data-ttu-id="eb399-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-392">'Identity'</span></span>
- <span data-ttu-id="eb399-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-394">'Razor'</span></span>
- <span data-ttu-id="eb399-395">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-396">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-397">'Blazor'</span></span>
- <span data-ttu-id="eb399-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-398">'Identity'</span></span>
- <span data-ttu-id="eb399-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-400">'Razor'</span></span>
- <span data-ttu-id="eb399-401">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-402">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-403">'Blazor'</span></span>
- <span data-ttu-id="eb399-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-404">'Identity'</span></span>
- <span data-ttu-id="eb399-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-406">'Razor'</span></span>
- <span data-ttu-id="eb399-407">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-408">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-409">'Blazor'</span></span>
- <span data-ttu-id="eb399-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-410">'Identity'</span></span>
- <span data-ttu-id="eb399-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-412">'Razor'</span></span>
- <span data-ttu-id="eb399-413">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-414">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-415">'Blazor'</span></span>
- <span data-ttu-id="eb399-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-416">'Identity'</span></span>
- <span data-ttu-id="eb399-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-418">'Razor'</span></span>
- <span data-ttu-id="eb399-419">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-420">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-421">'Blazor'</span></span>
- <span data-ttu-id="eb399-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-422">'Identity'</span></span>
- <span data-ttu-id="eb399-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-424">'Razor'</span></span>
- <span data-ttu-id="eb399-425">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-426">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-427">'Blazor'</span></span>
- <span data-ttu-id="eb399-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-428">'Identity'</span></span>
- <span data-ttu-id="eb399-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-430">'Razor'</span></span>
- <span data-ttu-id="eb399-431">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-432">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-433">'Blazor'</span></span>
- <span data-ttu-id="eb399-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-434">'Identity'</span></span>
- <span data-ttu-id="eb399-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-436">'Razor'</span></span>
- <span data-ttu-id="eb399-437">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-438">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-439">'Blazor'</span></span>
- <span data-ttu-id="eb399-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-440">'Identity'</span></span>
- <span data-ttu-id="eb399-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-442">'Razor'</span></span>
- <span data-ttu-id="eb399-443">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-443">'SignalR' uid:</span></span> 

<span data-ttu-id="eb399-444">---------------------------------------- | :---titolo:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-445">'Blazor'</span></span>
- <span data-ttu-id="eb399-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-446">'Identity'</span></span>
- <span data-ttu-id="eb399-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-448">'Razor'</span></span>
- <span data-ttu-id="eb399-449">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-450">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-451">'Blazor'</span></span>
- <span data-ttu-id="eb399-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-452">'Identity'</span></span>
- <span data-ttu-id="eb399-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-454">'Razor'</span></span>
- <span data-ttu-id="eb399-455">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-456">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-457">'Blazor'</span></span>
- <span data-ttu-id="eb399-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-458">'Identity'</span></span>
- <span data-ttu-id="eb399-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-460">'Razor'</span></span>
- <span data-ttu-id="eb399-461">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-462">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-463">'Blazor'</span></span>
- <span data-ttu-id="eb399-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-464">'Identity'</span></span>
- <span data-ttu-id="eb399-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-466">'Razor'</span></span>
- <span data-ttu-id="eb399-467">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-468">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-469">'Blazor'</span></span>
- <span data-ttu-id="eb399-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-470">'Identity'</span></span>
- <span data-ttu-id="eb399-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-472">'Razor'</span></span>
- <span data-ttu-id="eb399-473">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-474">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-475">'Blazor'</span></span>
- <span data-ttu-id="eb399-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-476">'Identity'</span></span>
- <span data-ttu-id="eb399-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-478">'Razor'</span></span>
- <span data-ttu-id="eb399-479">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-480">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-481">'Blazor'</span></span>
- <span data-ttu-id="eb399-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-482">'Identity'</span></span>
- <span data-ttu-id="eb399-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-484">'Razor'</span></span>
- <span data-ttu-id="eb399-485">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-486">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-487">'Blazor'</span></span>
- <span data-ttu-id="eb399-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-488">'Identity'</span></span>
- <span data-ttu-id="eb399-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-490">'Razor'</span></span>
- <span data-ttu-id="eb399-491">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-492">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-493">'Blazor'</span></span>
- <span data-ttu-id="eb399-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-494">'Identity'</span></span>
- <span data-ttu-id="eb399-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-496">'Razor'</span></span>
- <span data-ttu-id="eb399-497">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-498">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-499">'Blazor'</span></span>
- <span data-ttu-id="eb399-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-500">'Identity'</span></span>
- <span data-ttu-id="eb399-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-502">'Razor'</span></span>
- <span data-ttu-id="eb399-503">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-504">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-505">'Blazor'</span></span>
- <span data-ttu-id="eb399-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-506">'Identity'</span></span>
- <span data-ttu-id="eb399-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-508">'Razor'</span></span>
- <span data-ttu-id="eb399-509">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-510">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-511">'Blazor'</span></span>
- <span data-ttu-id="eb399-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-512">'Identity'</span></span>
- <span data-ttu-id="eb399-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-514">'Razor'</span></span>
- <span data-ttu-id="eb399-515">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-516">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-517">'Blazor'</span></span>
- <span data-ttu-id="eb399-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-518">'Identity'</span></span>
- <span data-ttu-id="eb399-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-520">'Razor'</span></span>
- <span data-ttu-id="eb399-521">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-521">'SignalR' uid:</span></span> 

<span data-ttu-id="eb399-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Sì | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Sì | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Sì | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Sì | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Sì | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Sì |</span><span class="sxs-lookup"><span data-stu-id="eb399-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="eb399-523">I vincoli di route che verificano l'URL e vengono convertiti in un tipo CLR, ad esempio `int` o `DateTime`, usano sempre le impostazioni cultura inglese non dipendenti da paese/area geografica,</span><span class="sxs-lookup"><span data-stu-id="eb399-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="eb399-524">presupponendo che l'URL sia non localizzabile.</span><span class="sxs-lookup"><span data-stu-id="eb399-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="eb399-525">Routing con URL contenenti punti</span><span class="sxs-lookup"><span data-stu-id="eb399-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="eb399-526">Nelle Blazor app Server la route predefinita è *_Host. cshtml* è `/` ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="eb399-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="eb399-527">Un URL di richiesta che contiene un punto ( `.` ) non corrisponde alla route predefinita perché l'URL viene visualizzato per richiedere un file.</span><span class="sxs-lookup"><span data-stu-id="eb399-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="eb399-528">Un' Blazor app restituisce una risposta *404 non trovata* per un file statico che non esiste.</span><span class="sxs-lookup"><span data-stu-id="eb399-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="eb399-529">Per usare le route che contengono un punto, configurare *_Host. cshtml* con il modello di route seguente:</span><span class="sxs-lookup"><span data-stu-id="eb399-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="eb399-530">Il `"/{**path}"` modello include:</span><span class="sxs-lookup"><span data-stu-id="eb399-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="eb399-531">Double-asterisco *catch-all* Syntax ( `**` ) per acquisire il percorso tra più limiti di cartelle senza codificare le barre ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="eb399-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="eb399-532">`path`nome del parametro di route.</span><span class="sxs-lookup"><span data-stu-id="eb399-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="eb399-533">La sintassi dei parametri *catch-all* ( `*` / `**` ) **non** è supportata nei Razor componenti (*Razor*).</span><span class="sxs-lookup"><span data-stu-id="eb399-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="eb399-534">Per altre informazioni, vedere <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="eb399-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="eb399-535">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="eb399-535">NavLink component</span></span>

<span data-ttu-id="eb399-536">Usare un `NavLink` componente al posto di elementi collegamento ipertestuale HTML ( `<a>` ) durante la creazione di collegamenti di navigazione.</span><span class="sxs-lookup"><span data-stu-id="eb399-536">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="eb399-537">Un `NavLink` componente si comporta come un `<a>` elemento, con la differenza che viene attivata o disattivata una `active` classe CSS a seconda che `href` corrisponda all'URL corrente.</span><span class="sxs-lookup"><span data-stu-id="eb399-537">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="eb399-538">La `active` classe consente a un utente di comprendere quale pagina è la pagina attiva tra i collegamenti di navigazione visualizzati.</span><span class="sxs-lookup"><span data-stu-id="eb399-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="eb399-539">Il `NavMenu` componente seguente crea una barra di navigazione [bootstrap](https://getbootstrap.com/docs/) che illustra come usare i `NavLink` componenti di:</span><span class="sxs-lookup"><span data-stu-id="eb399-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="eb399-540">Sono disponibili due `NavLinkMatch` opzioni che è possibile assegnare all' `Match` attributo dell' `<NavLink>` elemento:</span><span class="sxs-lookup"><span data-stu-id="eb399-540">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="eb399-541">`NavLinkMatch.All`&ndash;L'oggetto `NavLink` è attivo quando corrisponde all'intero URL corrente.</span><span class="sxs-lookup"><span data-stu-id="eb399-541">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="eb399-542">`NavLinkMatch.Prefix`(*impostazione predefinita*) &ndash; L'oggetto `NavLink` è attivo quando corrisponde a qualsiasi prefisso dell'URL corrente.</span><span class="sxs-lookup"><span data-stu-id="eb399-542">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="eb399-543">Nell'esempio precedente la Home `NavLink` `href=""` corrisponde all'URL Home e riceve solo la `active` classe CSS nell'URL del percorso di base predefinito dell'app (ad esempio, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="eb399-543">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="eb399-544">Il secondo `NavLink` riceve la `active` classe quando l'utente visita un URL con un `MyComponent` prefisso, ad esempio `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment` .</span><span class="sxs-lookup"><span data-stu-id="eb399-544">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="eb399-545">`NavLink`Gli attributi dei componenti aggiuntivi vengono passati al tag di ancoraggio sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="eb399-545">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="eb399-546">Nell'esempio seguente il `NavLink` componente include l' `target` attributo:</span><span class="sxs-lookup"><span data-stu-id="eb399-546">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="eb399-547">Viene eseguito il rendering del markup HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="eb399-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="eb399-548">Helper per lo stato di navigazione e URI</span><span class="sxs-lookup"><span data-stu-id="eb399-548">URI and navigation state helpers</span></span>

<span data-ttu-id="eb399-549">Usare <xref:Microsoft.AspNetCore.Components.NavigationManager> per lavorare con gli URI e la navigazione nel codice C#.</span><span class="sxs-lookup"><span data-stu-id="eb399-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="eb399-550">`NavigationManager`fornisce l'evento e i metodi illustrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="eb399-550">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="eb399-551">Membro</span><span class="sxs-lookup"><span data-stu-id="eb399-551">Member</span></span> | <span data-ttu-id="eb399-552">Description</span><span class="sxs-lookup"><span data-stu-id="eb399-552">Description</span></span> |
| ---
<span data-ttu-id="eb399-553">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-554">'Blazor'</span></span>
- <span data-ttu-id="eb399-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-555">'Identity'</span></span>
- <span data-ttu-id="eb399-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-557">'Razor'</span></span>
- <span data-ttu-id="eb399-558">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-558">'SignalR' uid:</span></span> 

<span data-ttu-id="eb399-559">--- | Titolo---:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-560">'Blazor'</span></span>
- <span data-ttu-id="eb399-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-561">'Identity'</span></span>
- <span data-ttu-id="eb399-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-563">'Razor'</span></span>
- <span data-ttu-id="eb399-564">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-565">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-566">'Blazor'</span></span>
- <span data-ttu-id="eb399-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-567">'Identity'</span></span>
- <span data-ttu-id="eb399-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-569">'Razor'</span></span>
- <span data-ttu-id="eb399-570">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eb399-571">title:' ASP.NET Core Blazor routing ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="eb399-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eb399-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb399-572">'Blazor'</span></span>
- <span data-ttu-id="eb399-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb399-573">'Identity'</span></span>
- <span data-ttu-id="eb399-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb399-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="eb399-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb399-575">'Razor'</span></span>
- <span data-ttu-id="eb399-576">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="eb399-576">'SignalR' uid:</span></span> 

<span data-ttu-id="eb399-577">------ | | URI | Ottiene l'URI assoluto corrente.</span><span class="sxs-lookup"><span data-stu-id="eb399-577">------ | | Uri | Gets the current absolute URI.</span></span> <span data-ttu-id="eb399-578">| | BaseUri | Ottiene l'URI di base (con una barra finale) che può essere anteposto ai percorsi URI relativi per produrre un URI assoluto.</span><span class="sxs-lookup"><span data-stu-id="eb399-578">| | BaseUri | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="eb399-579">In genere, `BaseUri` corrisponde all' `href` attributo sull'elemento del documento `<base>` in *wwwroot/index.html* ( Blazor webassembly) o *pages/_Host. cshtml* ( Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="eb399-579">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="eb399-580">| | NavigateTo | Passa all'URI specificato.</span><span class="sxs-lookup"><span data-stu-id="eb399-580">| | NavigateTo | Navigates to the specified URI.</span></span> <span data-ttu-id="eb399-581">Se `forceLoad` è `true` :</span><span class="sxs-lookup"><span data-stu-id="eb399-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="eb399-582">Il routing lato client viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="eb399-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="eb399-583">Il browser è forzato a caricare la nuova pagina dal server, indipendentemente dal fatto che l'URI venga normalmente gestito dal router lato client.</span><span class="sxs-lookup"><span data-stu-id="eb399-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="eb399-584">| | LocationChanged | Un evento che viene attivato quando il percorso di navigazione viene modificato.</span><span class="sxs-lookup"><span data-stu-id="eb399-584">| | LocationChanged | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="eb399-585">| | ToAbsoluteUri | Converte un URI relativo in un URI assoluto.</span><span class="sxs-lookup"><span data-stu-id="eb399-585">| | ToAbsoluteUri | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="eb399-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Dato un URI di base (ad esempio, un URI restituito in precedenza da `GetBaseUri` ), converte un URI assoluto in un URI relativo al prefisso URI di base.</span><span class="sxs-lookup"><span data-stu-id="eb399-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="eb399-587">`Counter`Quando si seleziona il pulsante, il componente seguente passa al componente dell'app:</span><span class="sxs-lookup"><span data-stu-id="eb399-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="eb399-588">Il componente seguente gestisce un evento di modifica della posizione.</span><span class="sxs-lookup"><span data-stu-id="eb399-588">The following component handles a location changed event.</span></span> <span data-ttu-id="eb399-589">Il `HandleLocationChanged` metodo è unhooked quando `Dispose` viene chiamato dal Framework.</span><span class="sxs-lookup"><span data-stu-id="eb399-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="eb399-590">L'unhook del metodo consente Garbage Collection del componente.</span><span class="sxs-lookup"><span data-stu-id="eb399-590">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="eb399-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fornisce le informazioni seguenti sull'evento:</span><span class="sxs-lookup"><span data-stu-id="eb399-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="eb399-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash;URL della nuova posizione.</span><span class="sxs-lookup"><span data-stu-id="eb399-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="eb399-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash;Se `true` , Blazor intercetta la navigazione dal browser.</span><span class="sxs-lookup"><span data-stu-id="eb399-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="eb399-594">Se `false` , [NavigationManager. NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) ha causato la navigazione.</span><span class="sxs-lookup"><span data-stu-id="eb399-594">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="eb399-595">Per ulteriori informazioni sull'eliminazione dei componenti, vedere <xref:blazor/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="eb399-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
