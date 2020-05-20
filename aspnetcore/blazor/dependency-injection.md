---
<span data-ttu-id="a3daa-101">title: "ASP.NET Core Blazor Injection Dependency Injection" Author: Description: "vedere come le Blazor app possono inserire i servizi in componenti".</span><span class="sxs-lookup"><span data-stu-id="a3daa-101">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="a3daa-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="a3daa-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a3daa-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-103">'Blazor'</span></span>
- <span data-ttu-id="a3daa-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a3daa-104">'Identity'</span></span>
- <span data-ttu-id="a3daa-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a3daa-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="a3daa-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-106">'Razor'</span></span>
- <span data-ttu-id="a3daa-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="a3daa-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="a3daa-108">BlazorInserimento di dipendenze ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a3daa-108">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="a3daa-109">Di [Rainer Stropek](https://www.timecockpit.com) e [Mike entusiasmanti](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="a3daa-109">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

Blazor<span data-ttu-id="a3daa-110">supporta l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a3daa-110"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a3daa-111">Le app possono usare i servizi predefiniti inserendoli in componenti.</span><span class="sxs-lookup"><span data-stu-id="a3daa-111">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="a3daa-112">Le app possono anche definire e registrare servizi personalizzati e renderli disponibili nell'app tramite DI.</span><span class="sxs-lookup"><span data-stu-id="a3daa-112">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="a3daa-113">DI è una tecnica per accedere ai servizi configurati in una posizione centrale.</span><span class="sxs-lookup"><span data-stu-id="a3daa-113">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="a3daa-114">Questa operazione può essere utile nelle Blazor app per:</span><span class="sxs-lookup"><span data-stu-id="a3daa-114">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="a3daa-115">Condividere una singola istanza di una classe di servizio in molti componenti, noti come un servizio *singleton* .</span><span class="sxs-lookup"><span data-stu-id="a3daa-115">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="a3daa-116">Separare i componenti da classi di servizi concrete usando astrazioni di riferimento.</span><span class="sxs-lookup"><span data-stu-id="a3daa-116">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="a3daa-117">Si consideri ad esempio un'interfaccia `IDataAccess` per l'accesso ai dati nell'app.</span><span class="sxs-lookup"><span data-stu-id="a3daa-117">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="a3daa-118">L'interfaccia viene implementata da una `DataAccess` classe concreta e registrata come servizio nel contenitore del servizio dell'app.</span><span class="sxs-lookup"><span data-stu-id="a3daa-118">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="a3daa-119">Quando un componente usa il per ricevere un' `IDataAccess` implementazione di, il componente non è associato al tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="a3daa-119">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="a3daa-120">L'implementazione può essere scambiata, ad esempio per un'implementazione fittizia negli unit test.</span><span class="sxs-lookup"><span data-stu-id="a3daa-120">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="a3daa-121">Servizi predefiniti</span><span class="sxs-lookup"><span data-stu-id="a3daa-121">Default services</span></span>

<span data-ttu-id="a3daa-122">I servizi predefiniti vengono aggiunti automaticamente alla raccolta di servizi dell'app.</span><span class="sxs-lookup"><span data-stu-id="a3daa-122">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="a3daa-123">Service</span><span class="sxs-lookup"><span data-stu-id="a3daa-123">Service</span></span> | <span data-ttu-id="a3daa-124">Durata</span><span class="sxs-lookup"><span data-stu-id="a3daa-124">Lifetime</span></span> | <span data-ttu-id="a3daa-125">Description</span><span class="sxs-lookup"><span data-stu-id="a3daa-125">Description</span></span> |
| ---
<span data-ttu-id="a3daa-126">title: "ASP.NET Core Blazor Injection Dependency Injection" Author: Description: "vedere come le Blazor app possono inserire i servizi in componenti".</span><span class="sxs-lookup"><span data-stu-id="a3daa-126">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="a3daa-127">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="a3daa-127">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a3daa-128">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-128">'Blazor'</span></span>
- <span data-ttu-id="a3daa-129">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a3daa-129">'Identity'</span></span>
- <span data-ttu-id="a3daa-130">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a3daa-130">'Let's Encrypt'</span></span>
- <span data-ttu-id="a3daa-131">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-131">'Razor'</span></span>
- <span data-ttu-id="a3daa-132">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="a3daa-132">'SignalR' uid:</span></span> 

<span data-ttu-id="a3daa-133">---- | Titolo---:' ASP.NET Core Blazor injection dipendenza ' Author: Description:' vedere come le Blazor app possono inserire i servizi nei componenti .'</span><span class="sxs-lookup"><span data-stu-id="a3daa-133">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="a3daa-134">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="a3daa-134">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a3daa-135">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-135">'Blazor'</span></span>
- <span data-ttu-id="a3daa-136">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a3daa-136">'Identity'</span></span>
- <span data-ttu-id="a3daa-137">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a3daa-137">'Let's Encrypt'</span></span>
- <span data-ttu-id="a3daa-138">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-138">'Razor'</span></span>
- <span data-ttu-id="a3daa-139">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="a3daa-139">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a3daa-140">title: "ASP.NET Core Blazor Injection Dependency Injection" Author: Description: "vedere come le Blazor app possono inserire i servizi in componenti".</span><span class="sxs-lookup"><span data-stu-id="a3daa-140">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="a3daa-141">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="a3daa-141">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a3daa-142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-142">'Blazor'</span></span>
- <span data-ttu-id="a3daa-143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a3daa-143">'Identity'</span></span>
- <span data-ttu-id="a3daa-144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a3daa-144">'Let's Encrypt'</span></span>
- <span data-ttu-id="a3daa-145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-145">'Razor'</span></span>
- <span data-ttu-id="a3daa-146">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="a3daa-146">'SignalR' uid:</span></span> 

<span data-ttu-id="a3daa-147">---- | Titolo---:' ASP.NET Core Blazor injection dipendenza ' Author: Description:' vedere come le Blazor app possono inserire i servizi nei componenti .'</span><span class="sxs-lookup"><span data-stu-id="a3daa-147">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="a3daa-148">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="a3daa-148">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a3daa-149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-149">'Blazor'</span></span>
- <span data-ttu-id="a3daa-150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a3daa-150">'Identity'</span></span>
- <span data-ttu-id="a3daa-151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a3daa-151">'Let's Encrypt'</span></span>
- <span data-ttu-id="a3daa-152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-152">'Razor'</span></span>
- <span data-ttu-id="a3daa-153">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="a3daa-153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a3daa-154">title: "ASP.NET Core Blazor Injection Dependency Injection" Author: Description: "vedere come le Blazor app possono inserire i servizi in componenti".</span><span class="sxs-lookup"><span data-stu-id="a3daa-154">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="a3daa-155">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="a3daa-155">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a3daa-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-156">'Blazor'</span></span>
- <span data-ttu-id="a3daa-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a3daa-157">'Identity'</span></span>
- <span data-ttu-id="a3daa-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a3daa-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="a3daa-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-159">'Razor'</span></span>
- <span data-ttu-id="a3daa-160">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="a3daa-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a3daa-161">title: "ASP.NET Core Blazor Injection Dependency Injection" Author: Description: "vedere come le Blazor app possono inserire i servizi in componenti".</span><span class="sxs-lookup"><span data-stu-id="a3daa-161">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="a3daa-162">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="a3daa-162">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a3daa-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-163">'Blazor'</span></span>
- <span data-ttu-id="a3daa-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a3daa-164">'Identity'</span></span>
- <span data-ttu-id="a3daa-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a3daa-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="a3daa-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-166">'Razor'</span></span>
- <span data-ttu-id="a3daa-167">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="a3daa-167">'SignalR' uid:</span></span> 

<span data-ttu-id="a3daa-168">------ | | <xref:System.Net.Http.HttpClient> | Temporaneo | Fornisce metodi per l'invio di richieste HTTP e la ricezione di risposte HTTP da una risorsa identificata da un URI.</span><span class="sxs-lookup"><span data-stu-id="a3daa-168">------ | | <xref:System.Net.Http.HttpClient> | Transient | Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="a3daa-169">L'istanza di `HttpClient` in un' Blazor app webassembly usa il browser per gestire il traffico HTTP in background.</span><span class="sxs-lookup"><span data-stu-id="a3daa-169">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br>Blazor<span data-ttu-id="a3daa-170">Per impostazione predefinita, le app Server non includono un `HttpClient` configurato come servizio.</span><span class="sxs-lookup"><span data-stu-id="a3daa-170"> Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="a3daa-171">Fornire un `HttpClient` a un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="a3daa-171">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="a3daa-172">Per altre informazioni, vedere <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="a3daa-172">For more information, see <xref:blazor/call-web-api>.</span></span> <span data-ttu-id="a3daa-173">| | `IJSRuntime` | Singleton ( Blazor webassembly)</span><span class="sxs-lookup"><span data-stu-id="a3daa-173">| | `IJSRuntime` | Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="a3daa-174">Con ambito ( Blazor Server) | Rappresenta un'istanza di un runtime JavaScript in cui vengono inviate le chiamate a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a3daa-174">Scoped (Blazor Server) | Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="a3daa-175">Per altre informazioni, vedere <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="a3daa-175">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> <span data-ttu-id="a3daa-176">| | `NavigationManager` | Singleton ( Blazor webassembly)</span><span class="sxs-lookup"><span data-stu-id="a3daa-176">| | `NavigationManager` | Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="a3daa-177">Con ambito ( Blazor Server) | Contiene gli helper per lavorare con gli URI e lo stato di navigazione.</span><span class="sxs-lookup"><span data-stu-id="a3daa-177">Scoped (Blazor Server) | Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="a3daa-178">Per ulteriori informazioni, vedere [URI e Helper dello stato di navigazione](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="a3daa-178">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="a3daa-179">Un provider di servizi personalizzato non fornisce automaticamente i servizi predefiniti elencati nella tabella.</span><span class="sxs-lookup"><span data-stu-id="a3daa-179">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="a3daa-180">Se si usa un provider di servizi personalizzato e si richiede uno dei servizi indicati nella tabella, aggiungere i servizi necessari al nuovo provider di servizi.</span><span class="sxs-lookup"><span data-stu-id="a3daa-180">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="a3daa-181">Aggiungere servizi a un'app</span><span class="sxs-lookup"><span data-stu-id="a3daa-181">Add services to an app</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="a3daa-182">Webassembly</span><span class="sxs-lookup"><span data-stu-id="a3daa-182"> WebAssembly</span></span>

<span data-ttu-id="a3daa-183">Configurare i servizi per la raccolta di servizi dell'app nel `Main` metodo di *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="a3daa-183">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="a3daa-184">Nell'esempio seguente l' `MyDependency` implementazione è registrata per `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="a3daa-184">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="a3daa-185">Una volta compilato l'host, è possibile accedere ai servizi dall'ambito radice prima DI eseguire il rendering di tutti i componenti.</span><span class="sxs-lookup"><span data-stu-id="a3daa-185">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="a3daa-186">Questa operazione può essere utile per eseguire la logica di inizializzazione prima del rendering del contenuto:</span><span class="sxs-lookup"><span data-stu-id="a3daa-186">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="a3daa-187">L'host fornisce anche un'istanza di configurazione centrale per l'app.</span><span class="sxs-lookup"><span data-stu-id="a3daa-187">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="a3daa-188">Basandosi sull'esempio precedente, l'URL del servizio meteo viene passato da un'origine di configurazione predefinita, ad esempio *appSettings. JSON*, a `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="a3daa-188">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

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

### <a name="blazor-server"></a>Blazor<span data-ttu-id="a3daa-189">Server</span><span class="sxs-lookup"><span data-stu-id="a3daa-189"> Server</span></span>

<span data-ttu-id="a3daa-190">Dopo aver creato una nuova app, esaminare il `Startup.ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="a3daa-190">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="a3daa-191">Al `ConfigureServices` metodo viene passato un oggetto <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , che è un elenco di oggetti del descrittore del servizio ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ).</span><span class="sxs-lookup"><span data-stu-id="a3daa-191">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="a3daa-192">I servizi vengono aggiunti fornendo descrittori del servizio alla raccolta di servizi.</span><span class="sxs-lookup"><span data-stu-id="a3daa-192">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="a3daa-193">Nell'esempio seguente viene illustrato il concetto con l' `IDataAccess` interfaccia e la relativa implementazione concreta `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="a3daa-193">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="a3daa-194">Durata del servizio</span><span class="sxs-lookup"><span data-stu-id="a3daa-194">Service lifetime</span></span>

<span data-ttu-id="a3daa-195">I servizi possono essere configurati con le durate mostrate nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="a3daa-195">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="a3daa-196">Durata</span><span class="sxs-lookup"><span data-stu-id="a3daa-196">Lifetime</span></span> | <span data-ttu-id="a3daa-197">Description</span><span class="sxs-lookup"><span data-stu-id="a3daa-197">Description</span></span> |
| ---
<span data-ttu-id="a3daa-198">title: "ASP.NET Core Blazor Injection Dependency Injection" Author: Description: "vedere come le Blazor app possono inserire i servizi in componenti".</span><span class="sxs-lookup"><span data-stu-id="a3daa-198">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="a3daa-199">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="a3daa-199">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a3daa-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-200">'Blazor'</span></span>
- <span data-ttu-id="a3daa-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a3daa-201">'Identity'</span></span>
- <span data-ttu-id="a3daa-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a3daa-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="a3daa-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-203">'Razor'</span></span>
- <span data-ttu-id="a3daa-204">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="a3daa-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a3daa-205">title: "ASP.NET Core Blazor Injection Dependency Injection" Author: Description: "vedere come le Blazor app possono inserire i servizi in componenti".</span><span class="sxs-lookup"><span data-stu-id="a3daa-205">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="a3daa-206">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="a3daa-206">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a3daa-207">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-207">'Blazor'</span></span>
- <span data-ttu-id="a3daa-208">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a3daa-208">'Identity'</span></span>
- <span data-ttu-id="a3daa-209">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a3daa-209">'Let's Encrypt'</span></span>
- <span data-ttu-id="a3daa-210">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-210">'Razor'</span></span>
- <span data-ttu-id="a3daa-211">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="a3daa-211">'SignalR' uid:</span></span> 

<span data-ttu-id="a3daa-212">---- | Titolo---:' ASP.NET Core Blazor injection dipendenza ' Author: Description:' vedere come le Blazor app possono inserire i servizi nei componenti .'</span><span class="sxs-lookup"><span data-stu-id="a3daa-212">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="a3daa-213">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="a3daa-213">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a3daa-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-214">'Blazor'</span></span>
- <span data-ttu-id="a3daa-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a3daa-215">'Identity'</span></span>
- <span data-ttu-id="a3daa-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a3daa-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="a3daa-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-217">'Razor'</span></span>
- <span data-ttu-id="a3daa-218">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="a3daa-218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a3daa-219">title: "ASP.NET Core Blazor Injection Dependency Injection" Author: Description: "vedere come le Blazor app possono inserire i servizi in componenti".</span><span class="sxs-lookup"><span data-stu-id="a3daa-219">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="a3daa-220">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="a3daa-220">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a3daa-221">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-221">'Blazor'</span></span>
- <span data-ttu-id="a3daa-222">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a3daa-222">'Identity'</span></span>
- <span data-ttu-id="a3daa-223">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a3daa-223">'Let's Encrypt'</span></span>
- <span data-ttu-id="a3daa-224">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-224">'Razor'</span></span>
- <span data-ttu-id="a3daa-225">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="a3daa-225">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a3daa-226">title: "ASP.NET Core Blazor Injection Dependency Injection" Author: Description: "vedere come le Blazor app possono inserire i servizi in componenti".</span><span class="sxs-lookup"><span data-stu-id="a3daa-226">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="a3daa-227">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="a3daa-227">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a3daa-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-228">'Blazor'</span></span>
- <span data-ttu-id="a3daa-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a3daa-229">'Identity'</span></span>
- <span data-ttu-id="a3daa-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a3daa-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="a3daa-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a3daa-231">'Razor'</span></span>
- <span data-ttu-id="a3daa-232">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="a3daa-232">'SignalR' uid:</span></span> 

<span data-ttu-id="a3daa-233">------ | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor Le app webassembly attualmente non dispongono di un concetto di ambiti di.</span><span class="sxs-lookup"><span data-stu-id="a3daa-233">------ | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="a3daa-234">`Scoped`-i servizi registrati si comportano come `Singleton` servizi.</span><span class="sxs-lookup"><span data-stu-id="a3daa-234">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="a3daa-235">Tuttavia, il Blazor modello di hosting del server supporta il `Scoped` ciclo di vita.</span><span class="sxs-lookup"><span data-stu-id="a3daa-235">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="a3daa-236">Nelle Blazor app Server, una registrazione del servizio con ambito ha come ambito la *connessione*.</span><span class="sxs-lookup"><span data-stu-id="a3daa-236">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="a3daa-237">Per questo motivo, è preferibile usare i servizi con ambito per i servizi che devono avere come ambito l'utente corrente, anche se l'obiettivo corrente è eseguire sul lato client nel browser.</span><span class="sxs-lookup"><span data-stu-id="a3daa-237">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> <span data-ttu-id="a3daa-238">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | La creazione di una *singola istanza* del servizio.</span><span class="sxs-lookup"><span data-stu-id="a3daa-238">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI creates a *single instance* of the service.</span></span> <span data-ttu-id="a3daa-239">Tutti i componenti che richiedono un `Singleton` servizio ricevono un'istanza dello stesso servizio.</span><span class="sxs-lookup"><span data-stu-id="a3daa-239">All components requiring a `Singleton` service receive an instance of the same service.</span></span> <span data-ttu-id="a3daa-240">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Ogni volta che un componente ottiene un'istanza di un `Transient` servizio dal contenitore del servizio, riceve una *nuova istanza* del servizio.</span><span class="sxs-lookup"><span data-stu-id="a3daa-240">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="a3daa-241">Il sistema DI è basato sul sistema DI ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a3daa-241">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="a3daa-242">Per altre informazioni, vedere <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="a3daa-242">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="a3daa-243">Richiedere un servizio in un componente</span><span class="sxs-lookup"><span data-stu-id="a3daa-243">Request a service in a component</span></span>

<span data-ttu-id="a3daa-244">Una volta aggiunti i servizi alla raccolta di servizi, inserire i servizi nei componenti utilizzando la direttiva [ \@ Inject](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="a3daa-244">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="a3daa-245">`@inject`dispone di due parametri:</span><span class="sxs-lookup"><span data-stu-id="a3daa-245">`@inject` has two parameters:</span></span>

* <span data-ttu-id="a3daa-246">Digitare &ndash; il tipo di servizio da inserire.</span><span class="sxs-lookup"><span data-stu-id="a3daa-246">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="a3daa-247">Proprietà &ndash; nome della proprietà che riceve il servizio app inserito.</span><span class="sxs-lookup"><span data-stu-id="a3daa-247">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="a3daa-248">La proprietà non richiede la creazione manuale.</span><span class="sxs-lookup"><span data-stu-id="a3daa-248">The property doesn't require manual creation.</span></span> <span data-ttu-id="a3daa-249">Il compilatore crea la proprietà.</span><span class="sxs-lookup"><span data-stu-id="a3daa-249">The compiler creates the property.</span></span>

<span data-ttu-id="a3daa-250">Per altre informazioni, vedere <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="a3daa-250">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="a3daa-251">Usare più `@inject` istruzioni per inserire servizi diversi.</span><span class="sxs-lookup"><span data-stu-id="a3daa-251">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="a3daa-252">Nell'esempio riportato di seguito viene illustrato come usare `@inject`.</span><span class="sxs-lookup"><span data-stu-id="a3daa-252">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="a3daa-253">Il servizio che implementa `Services.IDataAccess` viene inserito nella proprietà del componente `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="a3daa-253">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="a3daa-254">Si noti come il codice usa solo l' `IDataAccess` astrazione:</span><span class="sxs-lookup"><span data-stu-id="a3daa-254">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="a3daa-255">Internamente, la proprietà generata ( `DataRepository` ) utilizza l' `InjectAttribute` attributo.</span><span class="sxs-lookup"><span data-stu-id="a3daa-255">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="a3daa-256">In genere, questo attributo non viene utilizzato direttamente.</span><span class="sxs-lookup"><span data-stu-id="a3daa-256">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="a3daa-257">Se è necessaria una classe base per i componenti e le proprietà inserite sono necessarie anche per la classe base, aggiungere manualmente `InjectAttribute` :</span><span class="sxs-lookup"><span data-stu-id="a3daa-257">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="a3daa-258">Nei componenti derivati dalla classe di base, la `@inject` direttiva non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="a3daa-258">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="a3daa-259">La `InjectAttribute` classe della classe base è sufficiente:</span><span class="sxs-lookup"><span data-stu-id="a3daa-259">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="a3daa-260">Usare l'inserimento DI dipendenze nei servizi</span><span class="sxs-lookup"><span data-stu-id="a3daa-260">Use DI in services</span></span>

<span data-ttu-id="a3daa-261">Servizi complessi potrebbe richiedere servizi aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="a3daa-261">Complex services might require additional services.</span></span> <span data-ttu-id="a3daa-262">Nell'esempio precedente, `DataAccess` potrebbe richiedere il `HttpClient` servizio predefinito.</span><span class="sxs-lookup"><span data-stu-id="a3daa-262">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="a3daa-263">`@inject`(o `InjectAttribute` ) non è disponibile per l'uso nei servizi.</span><span class="sxs-lookup"><span data-stu-id="a3daa-263">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="a3daa-264">È necessario usare invece l' *inserimento del costruttore* .</span><span class="sxs-lookup"><span data-stu-id="a3daa-264">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="a3daa-265">I servizi necessari vengono aggiunti aggiungendo parametri al costruttore del servizio.</span><span class="sxs-lookup"><span data-stu-id="a3daa-265">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="a3daa-266">Quando si crea il servizio, vengono riconosciuti i servizi richiesti nel costruttore e forniti DI conseguenza.</span><span class="sxs-lookup"><span data-stu-id="a3daa-266">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="a3daa-267">Prerequisiti per l'inserimento del costruttore:</span><span class="sxs-lookup"><span data-stu-id="a3daa-267">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="a3daa-268">È necessario che esista un costruttore i cui argomenti possono essere tutti soddisfatti da DI.</span><span class="sxs-lookup"><span data-stu-id="a3daa-268">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="a3daa-269">Sono consentiti parametri aggiuntivi non analizzati da DI se specificano i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="a3daa-269">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="a3daa-270">Il costruttore applicabile deve essere *pubblico*.</span><span class="sxs-lookup"><span data-stu-id="a3daa-270">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="a3daa-271">È necessario che esista un costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="a3daa-271">One applicable constructor must exist.</span></span> <span data-ttu-id="a3daa-272">In caso di ambiguità, viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="a3daa-272">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="a3daa-273">Classi di componenti di base dell'utilità per gestire un ambito DI</span><span class="sxs-lookup"><span data-stu-id="a3daa-273">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="a3daa-274">Nelle app ASP.NET Core, i servizi con ambito hanno in genere come ambito la richiesta corrente.</span><span class="sxs-lookup"><span data-stu-id="a3daa-274">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="a3daa-275">Al termine della richiesta, tutti i servizi con ambito o temporaneo vengono eliminati dal sistema DI.</span><span class="sxs-lookup"><span data-stu-id="a3daa-275">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="a3daa-276">Nelle Blazor app Server, l'ambito della richiesta dura per la durata della connessione client, che può comportare un tempo di permanenza dei servizi temporanei e con ambito più lungo del previsto.</span><span class="sxs-lookup"><span data-stu-id="a3daa-276">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="a3daa-277">Nelle Blazor app webassembly i servizi registrati con una durata con ambito vengono considerati come singleton, quindi vivono più a lungo rispetto ai servizi con ambito nelle app ASP.NET Core tipiche.</span><span class="sxs-lookup"><span data-stu-id="a3daa-277">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="a3daa-278">Un approccio che limita la durata di un servizio nelle Blazor app è l'uso del `OwningComponentBase` tipo.</span><span class="sxs-lookup"><span data-stu-id="a3daa-278">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="a3daa-279">`OwningComponentBase`è un tipo astratto derivato da `ComponentBase` che consente di creare un ambito di che corrisponde alla durata del componente.</span><span class="sxs-lookup"><span data-stu-id="a3daa-279">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="a3daa-280">Con questo ambito, è possibile usare i servizi DI i con una durata con ambito e fare in modo che siano attivi fino a quando il componente.</span><span class="sxs-lookup"><span data-stu-id="a3daa-280">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="a3daa-281">Quando il componente viene eliminato definitivamente, vengono eliminati anche i servizi del provider di servizi con ambito.</span><span class="sxs-lookup"><span data-stu-id="a3daa-281">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="a3daa-282">Questa operazione può essere utile per i servizi che:</span><span class="sxs-lookup"><span data-stu-id="a3daa-282">This can be useful for services that:</span></span>

* <span data-ttu-id="a3daa-283">È necessario riutilizzarlo all'interno di un componente, perché la durata temporanea non è appropriata.</span><span class="sxs-lookup"><span data-stu-id="a3daa-283">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="a3daa-284">Non devono essere condivise tra i componenti, perché la durata singleton non è appropriata.</span><span class="sxs-lookup"><span data-stu-id="a3daa-284">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="a3daa-285">Sono disponibili due versioni del `OwningComponentBase` tipo:</span><span class="sxs-lookup"><span data-stu-id="a3daa-285">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="a3daa-286">`OwningComponentBase`è un elemento figlio astratto e disposable del `ComponentBase` tipo con una `ScopedServices` proprietà protetta di tipo `IServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="a3daa-286">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="a3daa-287">Questo provider può essere utilizzato per risolvere i servizi che hanno come ambito la durata del componente.</span><span class="sxs-lookup"><span data-stu-id="a3daa-287">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="a3daa-288">I servizi DI inserimento nel componente tramite `@inject` o `InjectAttribute` ( `[Inject]` ) non vengono creati nell'ambito del componente.</span><span class="sxs-lookup"><span data-stu-id="a3daa-288">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="a3daa-289">Per utilizzare l'ambito del componente, i servizi devono essere risolti utilizzando `ScopedServices.GetRequiredService` o `ScopedServices.GetService` .</span><span class="sxs-lookup"><span data-stu-id="a3daa-289">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="a3daa-290">Tutti i servizi risolti utilizzando il `ScopedServices` provider hanno le dipendenze fornite dallo stesso ambito.</span><span class="sxs-lookup"><span data-stu-id="a3daa-290">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="a3daa-291">`OwningComponentBase<T>`deriva da `OwningComponentBase` e aggiunge una proprietà `Service` che restituisce un'istanza di `T` dal provider dell'ambito di.</span><span class="sxs-lookup"><span data-stu-id="a3daa-291">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="a3daa-292">Questo tipo è un modo pratico per accedere ai servizi con ambito senza usare un'istanza di `IServiceProvider` quando è presente un servizio primario richiesto dall'app dal contenitore di inserimento delle dipendenze usando l'ambito del componente.</span><span class="sxs-lookup"><span data-stu-id="a3daa-292">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="a3daa-293">La `ScopedServices` proprietà è disponibile, in modo che l'app possa ottenere i servizi di altri tipi, se necessario.</span><span class="sxs-lookup"><span data-stu-id="a3daa-293">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="a3daa-294">Uso di Entity Framework DbContext da DI</span><span class="sxs-lookup"><span data-stu-id="a3daa-294">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="a3daa-295">Un tipo di servizio comune da recuperare da un in app Web è Entity Framework oggetti (EF) `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="a3daa-295">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="a3daa-296">`IServiceCollection.AddDbContext`Per impostazione predefinita, la registrazione dei servizi EF tramite aggiunge `DbContext` come servizio con ambito.</span><span class="sxs-lookup"><span data-stu-id="a3daa-296">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="a3daa-297">La registrazione come servizio con ambito può causare problemi nelle Blazor app perché causa `DbContext` la lunga durata delle istanze e la condivisione nell'app.</span><span class="sxs-lookup"><span data-stu-id="a3daa-297">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="a3daa-298">`DbContext`non è thread-safe e non deve essere usato simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="a3daa-298">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="a3daa-299">A seconda dell'app, l'uso `OwningComponentBase` di per limitare l'ambito di un `DbContext` a un singolo componente *può* risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="a3daa-299">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="a3daa-300">Se un componente non utilizza un oggetto `DbContext` in parallelo, la derivazione del componente da `OwningComponentBase` e il recupero `DbContext` di da `ScopedServices` è sufficiente perché garantisce quanto segue:</span><span class="sxs-lookup"><span data-stu-id="a3daa-300">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="a3daa-301">I componenti separati non condividono un `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="a3daa-301">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="a3daa-302">Il `DbContext` viene vissuto solo fino a quando il componente dipende da esso.</span><span class="sxs-lookup"><span data-stu-id="a3daa-302">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="a3daa-303">Se un singolo componente può usare `DbContext` simultaneamente, ad esempio ogni volta che un utente seleziona un pulsante, anche l'uso di `OwningComponentBase` non evita problemi con le operazioni EF simultanee.</span><span class="sxs-lookup"><span data-stu-id="a3daa-303">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="a3daa-304">In tal caso, usare un oggetto diverso `DbContext` per ogni operazione EF logica.</span><span class="sxs-lookup"><span data-stu-id="a3daa-304">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="a3daa-305">Usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="a3daa-305">Use either of the following approaches:</span></span>

* <span data-ttu-id="a3daa-306">Creare `DbContext` direttamente usando `DbContextOptions<TContext>` come argomento, che può essere recuperato da di ed è thread-safe.</span><span class="sxs-lookup"><span data-stu-id="a3daa-306">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

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

* <span data-ttu-id="a3daa-307">Registrare `DbContext` nel contenitore del servizio con una durata temporanea:</span><span class="sxs-lookup"><span data-stu-id="a3daa-307">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="a3daa-308">Quando si registra il contesto, usare `ServiceLifetime.Transient` .</span><span class="sxs-lookup"><span data-stu-id="a3daa-308">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="a3daa-309">Il `AddDbContext` metodo di estensione accetta due parametri facoltativi di tipo `ServiceLifetime` .</span><span class="sxs-lookup"><span data-stu-id="a3daa-309">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="a3daa-310">Per usare questo approccio, solo il `contextLifetime` parametro deve essere `ServiceLifetime.Transient` .</span><span class="sxs-lookup"><span data-stu-id="a3daa-310">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="a3daa-311">`optionsLifetime`può contenere il valore predefinito di `ServiceLifetime.Scoped` .</span><span class="sxs-lookup"><span data-stu-id="a3daa-311">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="a3daa-312">Il temporaneo `DbContext` può essere inserito normalmente (usando `@inject` ) in componenti che non eseguono più operazioni EF in parallelo.</span><span class="sxs-lookup"><span data-stu-id="a3daa-312">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="a3daa-313">Quelli che possono eseguire contemporaneamente più operazioni EF possono richiedere `DbContext` oggetti distinti per ogni operazione parallela usando `IServiceProvider.GetRequiredService` .</span><span class="sxs-lookup"><span data-stu-id="a3daa-313">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="a3daa-314">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a3daa-314">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
