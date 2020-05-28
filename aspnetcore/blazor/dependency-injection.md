---
<span data-ttu-id="22bf5-101">title: "ASP.NET Core Blazor Injection Dependency Injection" Author: Description: "vedere come le Blazor app possono inserire i servizi in componenti".</span><span class="sxs-lookup"><span data-stu-id="22bf5-101">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="22bf5-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="22bf5-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="22bf5-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-103">'Blazor'</span></span>
- <span data-ttu-id="22bf5-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="22bf5-104">'Identity'</span></span>
- <span data-ttu-id="22bf5-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="22bf5-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="22bf5-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-106">'Razor'</span></span>
- <span data-ttu-id="22bf5-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="22bf5-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="22bf5-108">BlazorInserimento di dipendenze ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="22bf5-108">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="22bf5-109">Di [Rainer Stropek](https://www.timecockpit.com) e [Mike entusiasmanti](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="22bf5-109">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

Blazor<span data-ttu-id="22bf5-110">supporta l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="22bf5-110"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="22bf5-111">Le app possono usare i servizi predefiniti inserendoli in componenti.</span><span class="sxs-lookup"><span data-stu-id="22bf5-111">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="22bf5-112">Le app possono anche definire e registrare servizi personalizzati e renderli disponibili nell'app tramite DI.</span><span class="sxs-lookup"><span data-stu-id="22bf5-112">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="22bf5-113">DI è una tecnica per accedere ai servizi configurati in una posizione centrale.</span><span class="sxs-lookup"><span data-stu-id="22bf5-113">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="22bf5-114">Questa operazione può essere utile nelle Blazor app per:</span><span class="sxs-lookup"><span data-stu-id="22bf5-114">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="22bf5-115">Condividere una singola istanza di una classe di servizio in molti componenti, noti come un servizio *singleton* .</span><span class="sxs-lookup"><span data-stu-id="22bf5-115">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="22bf5-116">Separare i componenti da classi di servizi concrete usando astrazioni di riferimento.</span><span class="sxs-lookup"><span data-stu-id="22bf5-116">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="22bf5-117">Si consideri ad esempio un'interfaccia `IDataAccess` per l'accesso ai dati nell'app.</span><span class="sxs-lookup"><span data-stu-id="22bf5-117">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="22bf5-118">L'interfaccia viene implementata da una `DataAccess` classe concreta e registrata come servizio nel contenitore del servizio dell'app.</span><span class="sxs-lookup"><span data-stu-id="22bf5-118">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="22bf5-119">Quando un componente usa il per ricevere un' `IDataAccess` implementazione di, il componente non è associato al tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="22bf5-119">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="22bf5-120">L'implementazione può essere scambiata, ad esempio per un'implementazione fittizia negli unit test.</span><span class="sxs-lookup"><span data-stu-id="22bf5-120">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="22bf5-121">Servizi predefiniti</span><span class="sxs-lookup"><span data-stu-id="22bf5-121">Default services</span></span>

<span data-ttu-id="22bf5-122">I servizi predefiniti vengono aggiunti automaticamente alla raccolta di servizi dell'app.</span><span class="sxs-lookup"><span data-stu-id="22bf5-122">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="22bf5-123">Service</span><span class="sxs-lookup"><span data-stu-id="22bf5-123">Service</span></span> | <span data-ttu-id="22bf5-124">Durata</span><span class="sxs-lookup"><span data-stu-id="22bf5-124">Lifetime</span></span> | <span data-ttu-id="22bf5-125">Descrizione</span><span class="sxs-lookup"><span data-stu-id="22bf5-125">Description</span></span> |
| ---
<span data-ttu-id="22bf5-126">title: "ASP.NET Core Blazor Injection Dependency Injection" Author: Description: "vedere come le Blazor app possono inserire i servizi in componenti".</span><span class="sxs-lookup"><span data-stu-id="22bf5-126">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="22bf5-127">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="22bf5-127">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="22bf5-128">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-128">'Blazor'</span></span>
- <span data-ttu-id="22bf5-129">'Identity'</span><span class="sxs-lookup"><span data-stu-id="22bf5-129">'Identity'</span></span>
- <span data-ttu-id="22bf5-130">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="22bf5-130">'Let's Encrypt'</span></span>
- <span data-ttu-id="22bf5-131">'Razor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-131">'Razor'</span></span>
- <span data-ttu-id="22bf5-132">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="22bf5-132">'SignalR' uid:</span></span> 

<span data-ttu-id="22bf5-133">---- | Titolo---:' ASP.NET Core Blazor injection dipendenza ' Author: Description:' vedere come le Blazor app possono inserire i servizi nei componenti .'</span><span class="sxs-lookup"><span data-stu-id="22bf5-133">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="22bf5-134">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="22bf5-134">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="22bf5-135">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-135">'Blazor'</span></span>
- <span data-ttu-id="22bf5-136">'Identity'</span><span class="sxs-lookup"><span data-stu-id="22bf5-136">'Identity'</span></span>
- <span data-ttu-id="22bf5-137">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="22bf5-137">'Let's Encrypt'</span></span>
- <span data-ttu-id="22bf5-138">'Razor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-138">'Razor'</span></span>
- <span data-ttu-id="22bf5-139">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="22bf5-139">'SignalR' uid:</span></span> 

-
<span data-ttu-id="22bf5-140">title: "ASP.NET Core Blazor Injection Dependency Injection" Author: Description: "vedere come le Blazor app possono inserire i servizi in componenti".</span><span class="sxs-lookup"><span data-stu-id="22bf5-140">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="22bf5-141">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="22bf5-141">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="22bf5-142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-142">'Blazor'</span></span>
- <span data-ttu-id="22bf5-143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="22bf5-143">'Identity'</span></span>
- <span data-ttu-id="22bf5-144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="22bf5-144">'Let's Encrypt'</span></span>
- <span data-ttu-id="22bf5-145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-145">'Razor'</span></span>
- <span data-ttu-id="22bf5-146">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="22bf5-146">'SignalR' uid:</span></span> 

<span data-ttu-id="22bf5-147">---- | Titolo---:' ASP.NET Core Blazor injection dipendenza ' Author: Description:' vedere come le Blazor app possono inserire i servizi nei componenti .'</span><span class="sxs-lookup"><span data-stu-id="22bf5-147">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="22bf5-148">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="22bf5-148">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="22bf5-149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-149">'Blazor'</span></span>
- <span data-ttu-id="22bf5-150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="22bf5-150">'Identity'</span></span>
- <span data-ttu-id="22bf5-151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="22bf5-151">'Let's Encrypt'</span></span>
- <span data-ttu-id="22bf5-152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-152">'Razor'</span></span>
- <span data-ttu-id="22bf5-153">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="22bf5-153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="22bf5-154">title: "ASP.NET Core Blazor Injection Dependency Injection" Author: Description: "vedere come le Blazor app possono inserire i servizi in componenti".</span><span class="sxs-lookup"><span data-stu-id="22bf5-154">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="22bf5-155">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="22bf5-155">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="22bf5-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-156">'Blazor'</span></span>
- <span data-ttu-id="22bf5-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="22bf5-157">'Identity'</span></span>
- <span data-ttu-id="22bf5-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="22bf5-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="22bf5-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-159">'Razor'</span></span>
- <span data-ttu-id="22bf5-160">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="22bf5-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="22bf5-161">title: "ASP.NET Core Blazor Injection Dependency Injection" Author: Description: "vedere come le Blazor app possono inserire i servizi in componenti".</span><span class="sxs-lookup"><span data-stu-id="22bf5-161">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="22bf5-162">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="22bf5-162">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="22bf5-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-163">'Blazor'</span></span>
- <span data-ttu-id="22bf5-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="22bf5-164">'Identity'</span></span>
- <span data-ttu-id="22bf5-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="22bf5-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="22bf5-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-166">'Razor'</span></span>
- <span data-ttu-id="22bf5-167">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="22bf5-167">'SignalR' uid:</span></span> 

<span data-ttu-id="22bf5-168">------ | | <xref:System.Net.Http.HttpClient> | Temporaneo | Fornisce metodi per l'invio di richieste HTTP e la ricezione di risposte HTTP da una risorsa identificata da un URI.</span><span class="sxs-lookup"><span data-stu-id="22bf5-168">------ | | <xref:System.Net.Http.HttpClient> | Transient | Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="22bf5-169">L'istanza di <xref:System.Net.Http.HttpClient> in un' Blazor app webassembly usa il browser per gestire il traffico HTTP in background.</span><span class="sxs-lookup"><span data-stu-id="22bf5-169">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br>Blazor<span data-ttu-id="22bf5-170">Per impostazione predefinita, le app Server non includono un <xref:System.Net.Http.HttpClient> configurato come servizio.</span><span class="sxs-lookup"><span data-stu-id="22bf5-170"> Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="22bf5-171">Fornire un <xref:System.Net.Http.HttpClient> a un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="22bf5-171">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="22bf5-172">Per altre informazioni, vedere <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="22bf5-172">For more information, see <xref:blazor/call-web-api>.</span></span> <span data-ttu-id="22bf5-173">| | <xref:Microsoft.JSInterop.IJSRuntime> | Singleton ( Blazor webassembly)</span><span class="sxs-lookup"><span data-stu-id="22bf5-173">| | <xref:Microsoft.JSInterop.IJSRuntime> | Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="22bf5-174">Con ambito ( Blazor Server) | Rappresenta un'istanza di un runtime JavaScript in cui vengono inviate le chiamate a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="22bf5-174">Scoped (Blazor Server) | Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="22bf5-175">Per altre informazioni, vedere <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="22bf5-175">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> <span data-ttu-id="22bf5-176">| | <xref:Microsoft.AspNetCore.Components.NavigationManager> | Singleton ( Blazor webassembly)</span><span class="sxs-lookup"><span data-stu-id="22bf5-176">| | <xref:Microsoft.AspNetCore.Components.NavigationManager> | Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="22bf5-177">Con ambito ( Blazor Server) | Contiene gli helper per lavorare con gli URI e lo stato di navigazione.</span><span class="sxs-lookup"><span data-stu-id="22bf5-177">Scoped (Blazor Server) | Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="22bf5-178">Per ulteriori informazioni, vedere [URI e Helper dello stato di navigazione](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="22bf5-178">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="22bf5-179">Un provider di servizi personalizzato non fornisce automaticamente i servizi predefiniti elencati nella tabella.</span><span class="sxs-lookup"><span data-stu-id="22bf5-179">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="22bf5-180">Se si usa un provider di servizi personalizzato e si richiede uno dei servizi indicati nella tabella, aggiungere i servizi necessari al nuovo provider di servizi.</span><span class="sxs-lookup"><span data-stu-id="22bf5-180">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="22bf5-181">Aggiungere servizi a un'app</span><span class="sxs-lookup"><span data-stu-id="22bf5-181">Add services to an app</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="22bf5-182">Webassembly</span><span class="sxs-lookup"><span data-stu-id="22bf5-182"> WebAssembly</span></span>

<span data-ttu-id="22bf5-183">Configurare i servizi per la raccolta di servizi dell'app nel `Main` metodo di *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="22bf5-183">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="22bf5-184">Nell'esempio seguente l' `MyDependency` implementazione è registrata per `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="22bf5-184">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="22bf5-185">Una volta compilato l'host, è possibile accedere ai servizi dall'ambito radice prima DI eseguire il rendering di tutti i componenti.</span><span class="sxs-lookup"><span data-stu-id="22bf5-185">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="22bf5-186">Questa operazione può essere utile per eseguire la logica di inizializzazione prima del rendering del contenuto:</span><span class="sxs-lookup"><span data-stu-id="22bf5-186">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="22bf5-187">L'host fornisce anche un'istanza di configurazione centrale per l'app.</span><span class="sxs-lookup"><span data-stu-id="22bf5-187">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="22bf5-188">Basandosi sull'esempio precedente, l'URL del servizio meteo viene passato da un'origine di configurazione predefinita, ad esempio *appSettings. JSON*, a `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="22bf5-188">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a>Blazor<span data-ttu-id="22bf5-189">Server</span><span class="sxs-lookup"><span data-stu-id="22bf5-189"> Server</span></span>

<span data-ttu-id="22bf5-190">Dopo aver creato una nuova app, esaminare il `Startup.ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="22bf5-190">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="22bf5-191">Al <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> metodo viene passato un oggetto <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , che è un elenco di oggetti del descrittore del servizio ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ).</span><span class="sxs-lookup"><span data-stu-id="22bf5-191">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="22bf5-192">I servizi vengono aggiunti fornendo descrittori del servizio alla raccolta di servizi.</span><span class="sxs-lookup"><span data-stu-id="22bf5-192">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="22bf5-193">Nell'esempio seguente viene illustrato il concetto con l' `IDataAccess` interfaccia e la relativa implementazione concreta `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="22bf5-193">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="22bf5-194">Durata del servizio</span><span class="sxs-lookup"><span data-stu-id="22bf5-194">Service lifetime</span></span>

<span data-ttu-id="22bf5-195">I servizi possono essere configurati con le durate mostrate nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="22bf5-195">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="22bf5-196">Durata</span><span class="sxs-lookup"><span data-stu-id="22bf5-196">Lifetime</span></span> | <span data-ttu-id="22bf5-197">Descrizione</span><span class="sxs-lookup"><span data-stu-id="22bf5-197">Description</span></span> |
| ---
<span data-ttu-id="22bf5-198">title: "ASP.NET Core Blazor Injection Dependency Injection" Author: Description: "vedere come le Blazor app possono inserire i servizi in componenti".</span><span class="sxs-lookup"><span data-stu-id="22bf5-198">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="22bf5-199">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="22bf5-199">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="22bf5-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-200">'Blazor'</span></span>
- <span data-ttu-id="22bf5-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="22bf5-201">'Identity'</span></span>
- <span data-ttu-id="22bf5-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="22bf5-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="22bf5-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-203">'Razor'</span></span>
- <span data-ttu-id="22bf5-204">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="22bf5-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="22bf5-205">title: "ASP.NET Core Blazor Injection Dependency Injection" Author: Description: "vedere come le Blazor app possono inserire i servizi in componenti".</span><span class="sxs-lookup"><span data-stu-id="22bf5-205">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="22bf5-206">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="22bf5-206">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="22bf5-207">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-207">'Blazor'</span></span>
- <span data-ttu-id="22bf5-208">'Identity'</span><span class="sxs-lookup"><span data-stu-id="22bf5-208">'Identity'</span></span>
- <span data-ttu-id="22bf5-209">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="22bf5-209">'Let's Encrypt'</span></span>
- <span data-ttu-id="22bf5-210">'Razor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-210">'Razor'</span></span>
- <span data-ttu-id="22bf5-211">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="22bf5-211">'SignalR' uid:</span></span> 

<span data-ttu-id="22bf5-212">---- | Titolo---:' ASP.NET Core Blazor injection dipendenza ' Author: Description:' vedere come le Blazor app possono inserire i servizi nei componenti .'</span><span class="sxs-lookup"><span data-stu-id="22bf5-212">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="22bf5-213">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="22bf5-213">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="22bf5-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-214">'Blazor'</span></span>
- <span data-ttu-id="22bf5-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="22bf5-215">'Identity'</span></span>
- <span data-ttu-id="22bf5-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="22bf5-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="22bf5-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-217">'Razor'</span></span>
- <span data-ttu-id="22bf5-218">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="22bf5-218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="22bf5-219">title: "ASP.NET Core Blazor Injection Dependency Injection" Author: Description: "vedere come le Blazor app possono inserire i servizi in componenti".</span><span class="sxs-lookup"><span data-stu-id="22bf5-219">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="22bf5-220">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="22bf5-220">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="22bf5-221">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-221">'Blazor'</span></span>
- <span data-ttu-id="22bf5-222">'Identity'</span><span class="sxs-lookup"><span data-stu-id="22bf5-222">'Identity'</span></span>
- <span data-ttu-id="22bf5-223">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="22bf5-223">'Let's Encrypt'</span></span>
- <span data-ttu-id="22bf5-224">'Razor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-224">'Razor'</span></span>
- <span data-ttu-id="22bf5-225">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="22bf5-225">'SignalR' uid:</span></span> 

-
<span data-ttu-id="22bf5-226">title: "ASP.NET Core Blazor Injection Dependency Injection" Author: Description: "vedere come le Blazor app possono inserire i servizi in componenti".</span><span class="sxs-lookup"><span data-stu-id="22bf5-226">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="22bf5-227">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="22bf5-227">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="22bf5-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-228">'Blazor'</span></span>
- <span data-ttu-id="22bf5-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="22bf5-229">'Identity'</span></span>
- <span data-ttu-id="22bf5-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="22bf5-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="22bf5-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="22bf5-231">'Razor'</span></span>
- <span data-ttu-id="22bf5-232">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="22bf5-232">'SignalR' uid:</span></span> 

<span data-ttu-id="22bf5-233">------ | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor Le app webassembly attualmente non dispongono di un concetto di ambiti di.</span><span class="sxs-lookup"><span data-stu-id="22bf5-233">------ | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="22bf5-234">`Scoped`-i servizi registrati si comportano come `Singleton` servizi.</span><span class="sxs-lookup"><span data-stu-id="22bf5-234">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="22bf5-235">Tuttavia, il Blazor modello di hosting del server supporta il `Scoped` ciclo di vita.</span><span class="sxs-lookup"><span data-stu-id="22bf5-235">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="22bf5-236">Nelle Blazor app Server, una registrazione del servizio con ambito ha come ambito la *connessione*.</span><span class="sxs-lookup"><span data-stu-id="22bf5-236">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="22bf5-237">Per questo motivo, è preferibile usare i servizi con ambito per i servizi che devono avere come ambito l'utente corrente, anche se l'obiettivo corrente è eseguire sul lato client nel browser.</span><span class="sxs-lookup"><span data-stu-id="22bf5-237">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> <span data-ttu-id="22bf5-238">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | La creazione di una *singola istanza* del servizio.</span><span class="sxs-lookup"><span data-stu-id="22bf5-238">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI creates a *single instance* of the service.</span></span> <span data-ttu-id="22bf5-239">Tutti i componenti che richiedono un `Singleton` servizio ricevono un'istanza dello stesso servizio.</span><span class="sxs-lookup"><span data-stu-id="22bf5-239">All components requiring a `Singleton` service receive an instance of the same service.</span></span> <span data-ttu-id="22bf5-240">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Ogni volta che un componente ottiene un'istanza di un `Transient` servizio dal contenitore del servizio, riceve una *nuova istanza* del servizio.</span><span class="sxs-lookup"><span data-stu-id="22bf5-240">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="22bf5-241">Il sistema DI è basato sul sistema DI ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="22bf5-241">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="22bf5-242">Per altre informazioni, vedere <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="22bf5-242">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="22bf5-243">Richiedere un servizio in un componente</span><span class="sxs-lookup"><span data-stu-id="22bf5-243">Request a service in a component</span></span>

<span data-ttu-id="22bf5-244">Una volta aggiunti i servizi alla raccolta di servizi, inserire i servizi nei componenti utilizzando la direttiva [ \@ Inject](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="22bf5-244">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="22bf5-245">[`@inject`](xref:mvc/views/razor#inject)dispone di due parametri:</span><span class="sxs-lookup"><span data-stu-id="22bf5-245">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="22bf5-246">Tipo: tipo di servizio da inserire.</span><span class="sxs-lookup"><span data-stu-id="22bf5-246">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="22bf5-247">Property: nome della proprietà che riceve il servizio app inserito.</span><span class="sxs-lookup"><span data-stu-id="22bf5-247">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="22bf5-248">La proprietà non richiede la creazione manuale.</span><span class="sxs-lookup"><span data-stu-id="22bf5-248">The property doesn't require manual creation.</span></span> <span data-ttu-id="22bf5-249">Il compilatore crea la proprietà.</span><span class="sxs-lookup"><span data-stu-id="22bf5-249">The compiler creates the property.</span></span>

<span data-ttu-id="22bf5-250">Per altre informazioni, vedere <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="22bf5-250">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="22bf5-251">Usare più [`@inject`](xref:mvc/views/razor#inject) istruzioni per inserire servizi diversi.</span><span class="sxs-lookup"><span data-stu-id="22bf5-251">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="22bf5-252">Nell'esempio seguente viene illustrato come utilizzare [`@inject`](xref:mvc/views/razor#inject) .</span><span class="sxs-lookup"><span data-stu-id="22bf5-252">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="22bf5-253">Il servizio che implementa `Services.IDataAccess` viene inserito nella proprietà del componente `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="22bf5-253">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="22bf5-254">Si noti come il codice usa solo l' `IDataAccess` astrazione:</span><span class="sxs-lookup"><span data-stu-id="22bf5-254">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="22bf5-255">Internamente, la proprietà generata ( `DataRepository` ) utilizza l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="22bf5-255">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="22bf5-256">In genere, questo attributo non viene utilizzato direttamente.</span><span class="sxs-lookup"><span data-stu-id="22bf5-256">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="22bf5-257">Se è necessaria una classe base per i componenti e le proprietà inserite sono necessarie anche per la classe base, aggiungere manualmente l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attributo:</span><span class="sxs-lookup"><span data-stu-id="22bf5-257">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="22bf5-258">Nei componenti derivati dalla classe di base, la [`@inject`](xref:mvc/views/razor#inject) direttiva non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="22bf5-258">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="22bf5-259">La <xref:Microsoft.AspNetCore.Components.InjectAttribute> classe della classe base è sufficiente:</span><span class="sxs-lookup"><span data-stu-id="22bf5-259">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="22bf5-260">Usare l'inserimento DI dipendenze nei servizi</span><span class="sxs-lookup"><span data-stu-id="22bf5-260">Use DI in services</span></span>

<span data-ttu-id="22bf5-261">Servizi complessi potrebbe richiedere servizi aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="22bf5-261">Complex services might require additional services.</span></span> <span data-ttu-id="22bf5-262">Nell'esempio precedente, `DataAccess` potrebbe richiedere il <xref:System.Net.Http.HttpClient> servizio predefinito.</span><span class="sxs-lookup"><span data-stu-id="22bf5-262">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="22bf5-263">[`@inject`](xref:mvc/views/razor#inject)(o l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attributo) non è disponibile per l'uso nei servizi.</span><span class="sxs-lookup"><span data-stu-id="22bf5-263">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="22bf5-264">È necessario usare invece l' *inserimento del costruttore* .</span><span class="sxs-lookup"><span data-stu-id="22bf5-264">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="22bf5-265">I servizi necessari vengono aggiunti aggiungendo parametri al costruttore del servizio.</span><span class="sxs-lookup"><span data-stu-id="22bf5-265">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="22bf5-266">Quando si crea il servizio, vengono riconosciuti i servizi richiesti nel costruttore e forniti DI conseguenza.</span><span class="sxs-lookup"><span data-stu-id="22bf5-266">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="22bf5-267">Prerequisiti per l'inserimento del costruttore:</span><span class="sxs-lookup"><span data-stu-id="22bf5-267">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="22bf5-268">È necessario che esista un costruttore i cui argomenti possono essere tutti soddisfatti da DI.</span><span class="sxs-lookup"><span data-stu-id="22bf5-268">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="22bf5-269">Sono consentiti parametri aggiuntivi non analizzati da DI se specificano i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="22bf5-269">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="22bf5-270">Il costruttore applicabile deve essere *pubblico*.</span><span class="sxs-lookup"><span data-stu-id="22bf5-270">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="22bf5-271">È necessario che esista un costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="22bf5-271">One applicable constructor must exist.</span></span> <span data-ttu-id="22bf5-272">In caso di ambiguità, viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="22bf5-272">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="22bf5-273">Classi di componenti di base dell'utilità per gestire un ambito DI</span><span class="sxs-lookup"><span data-stu-id="22bf5-273">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="22bf5-274">Nelle app ASP.NET Core, i servizi con ambito hanno in genere come ambito la richiesta corrente.</span><span class="sxs-lookup"><span data-stu-id="22bf5-274">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="22bf5-275">Al termine della richiesta, tutti i servizi con ambito o temporaneo vengono eliminati dal sistema DI.</span><span class="sxs-lookup"><span data-stu-id="22bf5-275">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="22bf5-276">Nelle Blazor app Server, l'ambito della richiesta dura per la durata della connessione client, che può comportare un tempo di permanenza dei servizi temporanei e con ambito più lungo del previsto.</span><span class="sxs-lookup"><span data-stu-id="22bf5-276">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="22bf5-277">Nelle Blazor app webassembly i servizi registrati con una durata con ambito vengono considerati come singleton, quindi vivono più a lungo rispetto ai servizi con ambito nelle app ASP.NET Core tipiche.</span><span class="sxs-lookup"><span data-stu-id="22bf5-277">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="22bf5-278">Un approccio che limita la durata di un servizio nelle Blazor app è l'uso del <xref:Microsoft.AspNetCore.Components.OwningComponentBase> tipo.</span><span class="sxs-lookup"><span data-stu-id="22bf5-278">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="22bf5-279"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>è un tipo astratto derivato da <xref:Microsoft.AspNetCore.Components.ComponentBase> che consente di creare un ambito di che corrisponde alla durata del componente.</span><span class="sxs-lookup"><span data-stu-id="22bf5-279"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="22bf5-280">Con questo ambito, è possibile usare i servizi DI i con una durata con ambito e fare in modo che siano attivi fino a quando il componente.</span><span class="sxs-lookup"><span data-stu-id="22bf5-280">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="22bf5-281">Quando il componente viene eliminato definitivamente, vengono eliminati anche i servizi del provider di servizi con ambito.</span><span class="sxs-lookup"><span data-stu-id="22bf5-281">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="22bf5-282">Questa operazione può essere utile per i servizi che:</span><span class="sxs-lookup"><span data-stu-id="22bf5-282">This can be useful for services that:</span></span>

* <span data-ttu-id="22bf5-283">È necessario riutilizzarlo all'interno di un componente, perché la durata temporanea non è appropriata.</span><span class="sxs-lookup"><span data-stu-id="22bf5-283">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="22bf5-284">Non devono essere condivise tra i componenti, perché la durata singleton non è appropriata.</span><span class="sxs-lookup"><span data-stu-id="22bf5-284">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="22bf5-285">Sono disponibili due versioni del <xref:Microsoft.AspNetCore.Components.OwningComponentBase> tipo:</span><span class="sxs-lookup"><span data-stu-id="22bf5-285">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="22bf5-286"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>è un elemento figlio astratto e disposable del <xref:Microsoft.AspNetCore.Components.ComponentBase> tipo con una <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> proprietà protetta di tipo <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="22bf5-286"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="22bf5-287">Questo provider può essere utilizzato per risolvere i servizi che hanno come ambito la durata del componente.</span><span class="sxs-lookup"><span data-stu-id="22bf5-287">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="22bf5-288">I servizi DI inserimento nel componente usando [`@inject`](xref:mvc/views/razor#inject) o l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attributo non vengono creati nell'ambito del componente.</span><span class="sxs-lookup"><span data-stu-id="22bf5-288">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="22bf5-289">Per utilizzare l'ambito del componente, i servizi devono essere risolti utilizzando <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> o <xref:System.IServiceProvider.GetService%2A> .</span><span class="sxs-lookup"><span data-stu-id="22bf5-289">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="22bf5-290">Tutti i servizi risolti utilizzando il <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider hanno le dipendenze fornite dallo stesso ambito.</span><span class="sxs-lookup"><span data-stu-id="22bf5-290">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="22bf5-291"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601>deriva da <xref:Microsoft.AspNetCore.Components.OwningComponentBase> e aggiunge una <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> proprietà che restituisce un'istanza di `T` dal provider dell'ambito di.</span><span class="sxs-lookup"><span data-stu-id="22bf5-291"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="22bf5-292">Questo tipo è un modo pratico per accedere ai servizi con ambito senza usare un'istanza di <xref:System.IServiceProvider> quando è presente un servizio primario richiesto dall'app dal contenitore di inserimento delle dipendenze usando l'ambito del componente.</span><span class="sxs-lookup"><span data-stu-id="22bf5-292">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="22bf5-293">La <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> proprietà è disponibile, in modo che l'app possa ottenere i servizi di altri tipi, se necessario.</span><span class="sxs-lookup"><span data-stu-id="22bf5-293">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="22bf5-294">Uso di Entity Framework DbContext da DI</span><span class="sxs-lookup"><span data-stu-id="22bf5-294">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="22bf5-295">Un tipo di servizio comune da recuperare da un in app Web è Entity Framework oggetti (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="22bf5-295">One common service type to retrieve from DI in web apps is Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> objects.</span></span> <span data-ttu-id="22bf5-296"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>Per impostazione predefinita, la registrazione dei servizi EF tramite aggiunge <xref:Microsoft.EntityFrameworkCore.DbContext> come servizio con ambito.</span><span class="sxs-lookup"><span data-stu-id="22bf5-296">Registering EF services using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> adds the <xref:Microsoft.EntityFrameworkCore.DbContext> as a scoped service by default.</span></span> <span data-ttu-id="22bf5-297">La registrazione come servizio con ambito può causare problemi nelle Blazor app perché causa <xref:Microsoft.EntityFrameworkCore.DbContext> la lunga durata delle istanze e la condivisione nell'app.</span><span class="sxs-lookup"><span data-stu-id="22bf5-297">Registering as a scoped service can lead to problems in Blazor apps because it causes <xref:Microsoft.EntityFrameworkCore.DbContext> instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="22bf5-298"><xref:Microsoft.EntityFrameworkCore.DbContext>non è thread-safe e non deve essere usato simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="22bf5-298"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="22bf5-299">A seconda dell'app, l'uso <xref:Microsoft.AspNetCore.Components.OwningComponentBase> di per limitare l'ambito di un <xref:Microsoft.EntityFrameworkCore.DbContext> a un singolo componente *può* risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="22bf5-299">Depending on the app, using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> to limit the scope of a <xref:Microsoft.EntityFrameworkCore.DbContext> to a single component *may* solve the issue.</span></span> <span data-ttu-id="22bf5-300">Se un componente non utilizza un oggetto <xref:Microsoft.EntityFrameworkCore.DbContext> in parallelo, la derivazione del componente da <xref:Microsoft.AspNetCore.Components.OwningComponentBase> e il recupero <xref:Microsoft.EntityFrameworkCore.DbContext> di da <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> è sufficiente perché garantisce quanto segue:</span><span class="sxs-lookup"><span data-stu-id="22bf5-300">If a component doesn't use a <xref:Microsoft.EntityFrameworkCore.DbContext> in parallel, deriving the component from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and retrieving the <xref:Microsoft.EntityFrameworkCore.DbContext> from <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> is sufficient because it ensures that:</span></span>

* <span data-ttu-id="22bf5-301">I componenti separati non condividono un <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="22bf5-301">Separate components don't share a <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>
* <span data-ttu-id="22bf5-302">Il <xref:Microsoft.EntityFrameworkCore.DbContext> viene vissuto solo fino a quando il componente dipende da esso.</span><span class="sxs-lookup"><span data-stu-id="22bf5-302">The <xref:Microsoft.EntityFrameworkCore.DbContext> lives only as long as the component depending on it.</span></span>

<span data-ttu-id="22bf5-303">Se un singolo componente può usare <xref:Microsoft.EntityFrameworkCore.DbContext> simultaneamente, ad esempio ogni volta che un utente seleziona un pulsante, anche l'uso di <xref:Microsoft.AspNetCore.Components.OwningComponentBase> non evita problemi con le operazioni EF simultanee.</span><span class="sxs-lookup"><span data-stu-id="22bf5-303">If a single component might use a <xref:Microsoft.EntityFrameworkCore.DbContext> concurrently (for example, every time a user selects a button), even using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="22bf5-304">In tal caso, usare un oggetto diverso <xref:Microsoft.EntityFrameworkCore.DbContext> per ogni operazione EF logica.</span><span class="sxs-lookup"><span data-stu-id="22bf5-304">In that case, use a different <xref:Microsoft.EntityFrameworkCore.DbContext> for each logical EF operation.</span></span> <span data-ttu-id="22bf5-305">Usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="22bf5-305">Use either of the following approaches:</span></span>

* <span data-ttu-id="22bf5-306">Creare <xref:Microsoft.EntityFrameworkCore.DbContext> direttamente usando <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> come argomento, che può essere recuperato da di ed è thread-safe.</span><span class="sxs-lookup"><span data-stu-id="22bf5-306">Create the <xref:Microsoft.EntityFrameworkCore.DbContext> directly using <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* <span data-ttu-id="22bf5-307">Registrare <xref:Microsoft.EntityFrameworkCore.DbContext> nel contenitore del servizio con una durata temporanea:</span><span class="sxs-lookup"><span data-stu-id="22bf5-307">Register the <xref:Microsoft.EntityFrameworkCore.DbContext> in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="22bf5-308">Quando si registra il contesto, usare <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="22bf5-308">When registering the context, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="22bf5-309">Il <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> metodo di estensione accetta due parametri facoltativi di tipo <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> .</span><span class="sxs-lookup"><span data-stu-id="22bf5-309">The <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method takes two optional parameters of type <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.</span></span> <span data-ttu-id="22bf5-310">Per usare questo approccio, solo il `contextLifetime` parametro deve essere <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="22bf5-310">To use this approach, only the `contextLifetime` parameter needs to be <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="22bf5-311">`optionsLifetime`può contenere il valore predefinito di <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="22bf5-311">`optionsLifetime` can keep its default value of <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType>.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="22bf5-312">Il temporaneo <xref:Microsoft.EntityFrameworkCore.DbContext> può essere inserito normalmente (usando [`@inject`](xref:mvc/views/razor#inject) ) in componenti che non eseguono più operazioni EF in parallelo.</span><span class="sxs-lookup"><span data-stu-id="22bf5-312">The transient <xref:Microsoft.EntityFrameworkCore.DbContext> can be injected as normal (using [`@inject`](xref:mvc/views/razor#inject)) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="22bf5-313">Quelli che possono eseguire contemporaneamente più operazioni EF possono richiedere <xref:Microsoft.EntityFrameworkCore.DbContext> oggetti distinti per ogni operazione parallela usando <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> .</span><span class="sxs-lookup"><span data-stu-id="22bf5-313">Those that may perform multiple EF operations simultaneously can request separate <xref:Microsoft.EntityFrameworkCore.DbContext> objects for each parallel operation using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A>.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="22bf5-314">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="22bf5-314">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
