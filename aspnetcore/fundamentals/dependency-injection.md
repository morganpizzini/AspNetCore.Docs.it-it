---
<span data-ttu-id="8db0d-101">Titolo: inserimento delle dipendenze in ASP.NET Core autore: Rick-Anderson Description: informazioni su come ASP.NET Core implementa l'inserimento delle dipendenze e su come usarlo.</span><span class="sxs-lookup"><span data-stu-id="8db0d-101">title: Dependency injection in ASP.NET Core author: rick-anderson description: Learn how ASP.NET Core implements dependency injection and how to use it.</span></span>
<span data-ttu-id="8db0d-102">monikerRange:' >= aspnetcore-2,1' ms. Author: Riande ms. Custom: MVC ms. Date: 05/14/2020 no-loc:</span><span class="sxs-lookup"><span data-stu-id="8db0d-102">monikerRange: '>= aspnetcore-2.1' ms.author: riande ms.custom: mvc ms.date: 05/14/2020 no-loc:</span></span>
- <span data-ttu-id="8db0d-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8db0d-103">'Blazor'</span></span>
- <span data-ttu-id="8db0d-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8db0d-104">'Identity'</span></span>
- <span data-ttu-id="8db0d-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8db0d-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="8db0d-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8db0d-106">'Razor'</span></span>
- <span data-ttu-id="8db0d-107">' SignalR ' UID: Nozioni fondamentali/dipendenza-inserimento</span><span class="sxs-lookup"><span data-stu-id="8db0d-107">'SignalR' uid: fundamentals/dependency-injection</span></span>

---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="8db0d-108">Inserimento delle dipendenze in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8db0d-108">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="8db0d-109">Di [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="8db0d-109">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8db0d-110">ASP.NET Core supporta il modello progettuale software per l'inserimento delle dipendenze, ovvero una tecnica per ottenere l'[inversione del controllo (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra classi e relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8db0d-110">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="8db0d-111">Per altre informazioni specifiche per l'inserimento delle dipendenze all'interno di controller MVC, vedere <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="8db0d-111">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="8db0d-112">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8db0d-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="8db0d-113">Panoramica dell'inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="8db0d-113">Overview of dependency injection</span></span>

<span data-ttu-id="8db0d-114">Una *dipendenza* è qualsiasi oggetto richiesto da un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="8db0d-114">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="8db0d-115">Esaminare la classe `MyDependency` seguente con un metodo `WriteMessage` da cui dipendono altre classi in un'app:</span><span class="sxs-lookup"><span data-stu-id="8db0d-115">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="8db0d-116">È possibile creare un'istanza della classe `MyDependency` per rendere il metodo `WriteMessage`disponibile per una classe.</span><span class="sxs-lookup"><span data-stu-id="8db0d-116">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="8db0d-117">La classe `MyDependency` è una dipendenza della classe `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="8db0d-117">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="8db0d-118">La classe crea e dipende direttamente dall'istanza `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-118">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="8db0d-119">Le dipendenze del codice (come nell'esempio precedente) sono problematiche e devono essere evitate per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8db0d-119">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="8db0d-120">Per sostituire `MyDependency` con un'implementazione diversa, la classe deve essere modificata.</span><span class="sxs-lookup"><span data-stu-id="8db0d-120">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="8db0d-121">Se `MyDependency` ha dipendenze, devono essere configurate dalla classe.</span><span class="sxs-lookup"><span data-stu-id="8db0d-121">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="8db0d-122">In un progetto di grandi dimensioni con più classi che dipendono da `MyDependency`, il codice di configurazione diventa sparso in tutta l'app.</span><span class="sxs-lookup"><span data-stu-id="8db0d-122">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="8db0d-123">È difficile eseguire unit test di questa implementazione.</span><span class="sxs-lookup"><span data-stu-id="8db0d-123">This implementation is difficult to unit test.</span></span> <span data-ttu-id="8db0d-124">L'app dovrebbe usare una classe `MyDependency` fittizia o stub, ma ciò non è possibile con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="8db0d-124">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="8db0d-125">L'inserimento delle dipendenze consente di risolvere questi problemi tramite:</span><span class="sxs-lookup"><span data-stu-id="8db0d-125">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="8db0d-126">L'uso di un'interfaccia o di una classe di base per astrarre l'implementazione delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8db0d-126">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="8db0d-127">La registrazione della dipendenza in un contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="8db0d-127">Registration of the dependency in a service container.</span></span> <span data-ttu-id="8db0d-128">ASP.NET Core offre il contenitore di servizi predefinito <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="8db0d-128">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="8db0d-129">I servizi vengono registrati nel metodo `Startup.ConfigureServices` dell'app.</span><span class="sxs-lookup"><span data-stu-id="8db0d-129">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="8db0d-130">L'*inserimento* del servizio nel costruttore della classe in cui viene usato.</span><span class="sxs-lookup"><span data-stu-id="8db0d-130">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="8db0d-131">Il framework si assume la responsabilità della creazione di un'istanza della dipendenza e della sua eliminazione quando non è più necessaria.</span><span class="sxs-lookup"><span data-stu-id="8db0d-131">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="8db0d-132">Nell'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l'interfaccia `IMyDependency` definisce un metodo che il servizio fornisce all'app:</span><span class="sxs-lookup"><span data-stu-id="8db0d-132">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="8db0d-133">Questa interfaccia viene implementata da un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="8db0d-133">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="8db0d-134">`MyDependency` richiede <xref:Microsoft.Extensions.Logging.ILogger`1> nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="8db0d-134">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="8db0d-135">Non è insolito usare l'inserimento delle dipendenze in modo concatenato.</span><span class="sxs-lookup"><span data-stu-id="8db0d-135">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="8db0d-136">Ogni dipendenza richiesta richiede a sua volta le proprie dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8db0d-136">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="8db0d-137">Il contenitore risolve le dipendenze nel grafico e restituisce il servizio completamente risolto.</span><span class="sxs-lookup"><span data-stu-id="8db0d-137">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="8db0d-138">Il set di dipendenze che devono essere risolte viene generalmente chiamato *albero delle dipendenze* o *grafico dipendenze* o *grafico degli oggetti*.</span><span class="sxs-lookup"><span data-stu-id="8db0d-138">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="8db0d-139">`IMyDependency` e `ILogger<TCategoryName>` devono essere registrati nel contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="8db0d-139">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="8db0d-140">`IMyDependency` viene registrato in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-140">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8db0d-141">`ILogger<TCategoryName>`viene registrato dall'infrastruttura di registrazione delle astrazioni, pertanto si tratta di un [servizio fornito dal Framework](#framework-provided-services) registrato per impostazione predefinita dal Framework.</span><span class="sxs-lookup"><span data-stu-id="8db0d-141">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="8db0d-142">Il contenitore risolve `ILogger<TCategoryName>` avvalendosi di [tipi aperti (generici)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando l'esigenza di registrare ogni singolo [tipo costruito (generico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="8db0d-142">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="8db0d-143">Nell'app di esempio, il servizio `IMyDependency` viene registrato con il tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-143">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="8db0d-144">La registrazione definisce come ambito della durata di servizio la durata di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="8db0d-144">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="8db0d-145">Le [durate dei servizi](#service-lifetimes) sono descritte più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="8db0d-145">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="8db0d-146">Ogni metodo di estensione `services.Add{SERVICE_NAME}` aggiunge (e potenzialmente configura) i servizi.</span><span class="sxs-lookup"><span data-stu-id="8db0d-146">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="8db0d-147">Ad esempio, `services.AddMvc()` aggiunge le pagine dei servizi Razor e MVC require.</span><span class="sxs-lookup"><span data-stu-id="8db0d-147">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="8db0d-148">È consigliabile che le app seguano questa convenzione.</span><span class="sxs-lookup"><span data-stu-id="8db0d-148">We recommended that apps follow this convention.</span></span> <span data-ttu-id="8db0d-149">Inserire i metodi di estensione nello spazio dei nomi [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) per incapsulare i gruppi di registrazioni di servizio.</span><span class="sxs-lookup"><span data-stu-id="8db0d-149">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="8db0d-150">Se il costruttore del servizio richiede un [tipo incorporato](/dotnet/csharp/language-reference/keywords/built-in-types-table), ad esempio `string`, è possibile inserirlo usando la [configurazione](xref:fundamentals/configuration/index) o il [modello di opzioni](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="8db0d-150">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="8db0d-151">È richiesta un'istanza del servizio tramite il costruttore di una classe in cui il servizio viene usato e assegnato a un campo privato.</span><span class="sxs-lookup"><span data-stu-id="8db0d-151">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="8db0d-152">Il campo viene usato per accedere al servizio in base alle esigenze per l'intera classe.</span><span class="sxs-lookup"><span data-stu-id="8db0d-152">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="8db0d-153">Nell'app di esempio, viene richiesta l'istanza `IMyDependency` usata per chiamare il metodo `WriteMessage` del servizio:</span><span class="sxs-lookup"><span data-stu-id="8db0d-153">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="8db0d-154">Servizi inseriti all'avvio</span><span class="sxs-lookup"><span data-stu-id="8db0d-154">Services injected into Startup</span></span>

<span data-ttu-id="8db0d-155">`Startup`Quando si utilizza l'host generico () è possibile inserire nel costruttore solo i tipi di servizio seguenti <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="8db0d-155">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="8db0d-156">I servizi possono essere inseriti in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="8db0d-156">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="8db0d-157">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="8db0d-157">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="8db0d-158">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="8db0d-158">Framework-provided services</span></span>

<span data-ttu-id="8db0d-159">Il `Startup.ConfigureServices` metodo è responsabile della definizione dei servizi utilizzati dall'app, incluse le funzionalità della piattaforma, ad esempio Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="8db0d-159">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="8db0d-160">Inizialmente, l'oggetto `IServiceCollection` fornito a `ConfigureServices` dispone di servizi definiti dal Framework a seconda della [configurazione dell'host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="8db0d-160">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="8db0d-161">Non è insolito che un'app basata su un modello di ASP.NET Core disponga di centinaia di servizi registrati dal Framework.</span><span class="sxs-lookup"><span data-stu-id="8db0d-161">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="8db0d-162">Nella tabella seguente è elencato un piccolo esempio di servizi registrati dal Framework.</span><span class="sxs-lookup"><span data-stu-id="8db0d-162">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="8db0d-163">Tipo di servizio</span><span class="sxs-lookup"><span data-stu-id="8db0d-163">Service Type</span></span> | <span data-ttu-id="8db0d-164">Durata</span><span class="sxs-lookup"><span data-stu-id="8db0d-164">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="8db0d-165">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="8db0d-165">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="8db0d-166">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-166">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="8db0d-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-167">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="8db0d-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-168">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="8db0d-169">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="8db0d-169">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="8db0d-170">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-170">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="8db0d-171">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="8db0d-171">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="8db0d-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-172">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="8db0d-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-173">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="8db0d-174">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-174">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="8db0d-175">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="8db0d-175">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="8db0d-176">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-176">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="8db0d-177">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-177">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="8db0d-178">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-178">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="8db0d-179">Registrare servizi aggiuntivi con i metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="8db0d-179">Register additional services with extension methods</span></span>

<span data-ttu-id="8db0d-180">Quando è disponibile un metodo di estensione della raccolta di servizi per la registrazione di un servizio (e dei servizi dipendenti, se necessario), la convenzione consiste nell'usare un singolo metodo di estensione `Add{SERVICE_NAME}` per registrare tutti i servizi richiesti da tale servizio.</span><span class="sxs-lookup"><span data-stu-id="8db0d-180">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="8db0d-181">Il codice seguente è un esempio di come aggiungere servizi aggiuntivi al contenitore usando i metodi di estensione [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="8db0d-181">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="8db0d-182">Per altre informazioni, vedere la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> nella documentazione delle API.</span><span class="sxs-lookup"><span data-stu-id="8db0d-182">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="8db0d-183">Durate dei servizi</span><span class="sxs-lookup"><span data-stu-id="8db0d-183">Service lifetimes</span></span>

<span data-ttu-id="8db0d-184">Scegliere una durata appropriata per ogni servizio registrato.</span><span class="sxs-lookup"><span data-stu-id="8db0d-184">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="8db0d-185">I servizi ASP.NET Core possono essere configurati con le impostazioni di durata seguenti:</span><span class="sxs-lookup"><span data-stu-id="8db0d-185">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="8db0d-186">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="8db0d-186">Transient</span></span>

<span data-ttu-id="8db0d-187">I servizi con durata temporanea (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) vengono creati ogni volta che vengono richiesti dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="8db0d-187">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="8db0d-188">Questa impostazione di durata è consigliata per i servizi semplici senza stati.</span><span class="sxs-lookup"><span data-stu-id="8db0d-188">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="8db0d-189">Con ambito</span><span class="sxs-lookup"><span data-stu-id="8db0d-189">Scoped</span></span>

<span data-ttu-id="8db0d-190">I servizi con durata con ambito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) vengono creati una volta per ogni richiesta client (connessione).</span><span class="sxs-lookup"><span data-stu-id="8db0d-190">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="8db0d-191">Quando si usa un servizio con ambito in un middleware, inserire il servizio nel metodo `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-191">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="8db0d-192">Non inserire tramite il [Costruttore Injection](xref:mvc/controllers/dependency-injection#constructor-injection) perché impone al servizio di comportarsi come un singleton.</span><span class="sxs-lookup"><span data-stu-id="8db0d-192">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="8db0d-193">Per altre informazioni, vedere <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="8db0d-193">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="8db0d-194">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-194">Singleton</span></span>

<span data-ttu-id="8db0d-195">I servizi con durata singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) vengono creati la prima volta che vengono richiesti (o quando si esegue `Startup.ConfigureServices` e viene specificata un'istanza con la registrazione del servizio).</span><span class="sxs-lookup"><span data-stu-id="8db0d-195">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="8db0d-196">Tutte le richieste successive usano la stessa istanza.</span><span class="sxs-lookup"><span data-stu-id="8db0d-196">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="8db0d-197">Se l'app richiede un comportamento singleton, è consigliabile consentire al contenitore del servizio di gestire la durata del servizio.</span><span class="sxs-lookup"><span data-stu-id="8db0d-197">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="8db0d-198">Non implementare lo schema progettuale singleton e fornire codice utente per gestire la durata dell'oggetto nella classe.</span><span class="sxs-lookup"><span data-stu-id="8db0d-198">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="8db0d-199">Può essere pericoloso risolvere un servizio con ambito da un singleton.</span><span class="sxs-lookup"><span data-stu-id="8db0d-199">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="8db0d-200">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="8db0d-200">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="8db0d-201">Metodi di registrazione del servizio</span><span class="sxs-lookup"><span data-stu-id="8db0d-201">Service registration methods</span></span>

<span data-ttu-id="8db0d-202">I metodi di estensione per la registrazione del servizio offrono overload che risultano utili in scenari specifici.</span><span class="sxs-lookup"><span data-stu-id="8db0d-202">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="8db0d-203">Metodo</span><span class="sxs-lookup"><span data-stu-id="8db0d-203">Method</span></span> | <span data-ttu-id="8db0d-204">Automatico</span><span class="sxs-lookup"><span data-stu-id="8db0d-204">Automatic</span></span><br><span data-ttu-id="8db0d-205">object</span><span class="sxs-lookup"><span data-stu-id="8db0d-205">object</span></span><br><span data-ttu-id="8db0d-206">eliminazione</span><span class="sxs-lookup"><span data-stu-id="8db0d-206">disposal</span></span> | <span data-ttu-id="8db0d-207">Multipli</span><span class="sxs-lookup"><span data-stu-id="8db0d-207">Multiple</span></span><br><span data-ttu-id="8db0d-208">implementazioni</span><span class="sxs-lookup"><span data-stu-id="8db0d-208">implementations</span></span> | <span data-ttu-id="8db0d-209">Pass args</span><span class="sxs-lookup"><span data-stu-id="8db0d-209">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="8db0d-210">Esempio:</span><span class="sxs-lookup"><span data-stu-id="8db0d-210">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="8db0d-211">Sì</span><span class="sxs-lookup"><span data-stu-id="8db0d-211">Yes</span></span> | <span data-ttu-id="8db0d-212">Sì</span><span class="sxs-lookup"><span data-stu-id="8db0d-212">Yes</span></span> | <span data-ttu-id="8db0d-213">No</span><span class="sxs-lookup"><span data-stu-id="8db0d-213">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="8db0d-214">Esempi:</span><span class="sxs-lookup"><span data-stu-id="8db0d-214">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="8db0d-215">Sì</span><span class="sxs-lookup"><span data-stu-id="8db0d-215">Yes</span></span> | <span data-ttu-id="8db0d-216">Sì</span><span class="sxs-lookup"><span data-stu-id="8db0d-216">Yes</span></span> | <span data-ttu-id="8db0d-217">Sì</span><span class="sxs-lookup"><span data-stu-id="8db0d-217">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="8db0d-218">Esempio:</span><span class="sxs-lookup"><span data-stu-id="8db0d-218">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="8db0d-219">Sì</span><span class="sxs-lookup"><span data-stu-id="8db0d-219">Yes</span></span> | <span data-ttu-id="8db0d-220">No</span><span class="sxs-lookup"><span data-stu-id="8db0d-220">No</span></span> | <span data-ttu-id="8db0d-221">No</span><span class="sxs-lookup"><span data-stu-id="8db0d-221">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="8db0d-222">Esempi:</span><span class="sxs-lookup"><span data-stu-id="8db0d-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="8db0d-223">No</span><span class="sxs-lookup"><span data-stu-id="8db0d-223">No</span></span> | <span data-ttu-id="8db0d-224">Sì</span><span class="sxs-lookup"><span data-stu-id="8db0d-224">Yes</span></span> | <span data-ttu-id="8db0d-225">Sì</span><span class="sxs-lookup"><span data-stu-id="8db0d-225">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="8db0d-226">Esempi:</span><span class="sxs-lookup"><span data-stu-id="8db0d-226">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="8db0d-227">No</span><span class="sxs-lookup"><span data-stu-id="8db0d-227">No</span></span> | <span data-ttu-id="8db0d-228">No</span><span class="sxs-lookup"><span data-stu-id="8db0d-228">No</span></span> | <span data-ttu-id="8db0d-229">Sì</span><span class="sxs-lookup"><span data-stu-id="8db0d-229">Yes</span></span> |

<span data-ttu-id="8db0d-230">Per ulteriori informazioni sull'eliminazione dei tipi, vedere la sezione [Eliminazione dei servizi](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="8db0d-230">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="8db0d-231">Uno scenario comune per più implementazioni è costituito dai [tipi di simulazioni per i test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="8db0d-231">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="8db0d-232">I metodi `TryAdd{LIFETIME}` registrano il servizio solo se esiste già un'implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="8db0d-232">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="8db0d-233">Nell'esempio seguente, la prima riga registra `MyDependency` per `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-233">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="8db0d-234">La seconda riga non ha alcun effetto perché `IMyDependency` dispone già di un'implementazione registrata:</span><span class="sxs-lookup"><span data-stu-id="8db0d-234">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="8db0d-235">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="8db0d-235">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="8db0d-236">I metodi [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrano il servizio solo se non esiste già un'implementazione *dello stesso tipo*.</span><span class="sxs-lookup"><span data-stu-id="8db0d-236">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="8db0d-237">Più servizi vengono risolti tramite `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-237">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="8db0d-238">Durante la registrazione di servizi, lo sviluppatore desidera aggiungere un'istanza solo se non ne è già stata aggiunta una dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="8db0d-238">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="8db0d-239">In genere, questo metodo viene utilizzato dagli autori di librerie per evitare di registrare due copie di un'istanza nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="8db0d-239">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="8db0d-240">Nell'esempio seguente, la prima riga registra `MyDep` per `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-240">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="8db0d-241">La seconda riga registra `MyDep` per `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-241">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="8db0d-242">La seconda riga non ha alcun effetto perché `IMyDep1` dispone già di un'implementazione registrata di `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="8db0d-242">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="8db0d-243">Comportamento dell'inserimento del costruttore</span><span class="sxs-lookup"><span data-stu-id="8db0d-243">Constructor injection behavior</span></span>

<span data-ttu-id="8db0d-244">I servizi possono essere risolti con due meccanismi:</span><span class="sxs-lookup"><span data-stu-id="8db0d-244">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="8db0d-245"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Consente la creazione di oggetti senza registrazione del servizio nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8db0d-245"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="8db0d-246">`ActivatorUtilities` viene usato con astrazioni esposte all'utente, ad esempio helper tag, controller MVC e strumenti di associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="8db0d-246">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="8db0d-247">I costruttori possono accettare argomenti non forniti tramite l'inserimento di dipendenze, ma gli argomenti devono assegnare valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="8db0d-247">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="8db0d-248">Quando i servizi vengono risolti da `IServiceProvider` o `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede un costruttore *pubblico* .</span><span class="sxs-lookup"><span data-stu-id="8db0d-248">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="8db0d-249">Quando i servizi vengono risolti da `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede l'esistenza di un solo costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="8db0d-249">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="8db0d-250">Sebbene siano supportati gli overload dei costruttori, può essere presente un solo overload i cui argomenti possono essere tutti specificati tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8db0d-250">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="8db0d-251">Contesti di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="8db0d-251">Entity Framework contexts</span></span>

<span data-ttu-id="8db0d-252">I contesti di Entity Framework vengono in genere aggiunti al contenitore di servizi usando la [durata con ambito](#service-lifetimes) perché le operazioni di database delle app Web hanno di solito come ambito la richiesta client.</span><span class="sxs-lookup"><span data-stu-id="8db0d-252">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="8db0d-253">L'ambito della durata predefinita è se una durata non è specificata da un overload [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) durante la registrazione del contesto del database.</span><span class="sxs-lookup"><span data-stu-id="8db0d-253">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="8db0d-254">I servizi con una durata specificata non devono usare un contesto di database con una durata più breve rispetto al servizio.</span><span class="sxs-lookup"><span data-stu-id="8db0d-254">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="8db0d-255">Opzioni di durata e di registrazione</span><span class="sxs-lookup"><span data-stu-id="8db0d-255">Lifetime and registration options</span></span>

<span data-ttu-id="8db0d-256">Per illustrare la differenza tra le opzioni di durata e registrazione, si considerino le interfacce seguenti che rappresentano le attività come un'operazione con un identificatore univoco, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-256">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="8db0d-257">A seconda del modo in cui la durata di un servizio operativo viene configurata per le interfacce seguenti, il contenitore fornisce la stessa istanza o un'istanza diversa del servizio quando richiesto da una classe:</span><span class="sxs-lookup"><span data-stu-id="8db0d-257">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="8db0d-258">Le interfacce vengono implementate nella classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-258">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="8db0d-259">Il costruttore `Operation` genera un GUID se non ne viene fornito uno:</span><span class="sxs-lookup"><span data-stu-id="8db0d-259">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="8db0d-260">Viene registrato un `OperationService` che dipende da ognuno degli altri tipi `Operation`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-260">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="8db0d-261">Quando viene richiesto `OperationService` tramite l'inserimento delle dipendenze, riceve una nuova istanza di ogni servizio o un'istanza esistente in base alla durata del servizio dipendente.</span><span class="sxs-lookup"><span data-stu-id="8db0d-261">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="8db0d-262">Se i servizi temporanei vengono creati quando vengono richiesti dal contenitore, `OperationId` del servizio `IOperationTransient` è diverso da `OperationId` di `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-262">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="8db0d-263">`OperationService` riceve una nuova istanza della classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-263">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="8db0d-264">La nuova istanza restituisce un `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="8db0d-264">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="8db0d-265">Se i servizi con ambito vengono creati per ogni richiesta client, `OperationId` del servizio `IOperationScoped` corrisponde a `OperationService` all'interno di una richiesta client.</span><span class="sxs-lookup"><span data-stu-id="8db0d-265">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="8db0d-266">In tutte le richieste client entrambi i servizi condividono un valore `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="8db0d-266">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="8db0d-267">Se i servizi singleton e con istanza singleton vengono creati una sola volta e usati per tutte le richieste client e tutti i servizi, `OperationId` rimane costante in tutte le richieste di servizio.</span><span class="sxs-lookup"><span data-stu-id="8db0d-267">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="8db0d-268">In `Startup.ConfigureServices`, ogni tipo viene aggiunto al contenitore in base alla durata denominata:</span><span class="sxs-lookup"><span data-stu-id="8db0d-268">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="8db0d-269">Il servizio `IOperationSingletonInstance` usa un'istanza specifica con un ID noto di `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-269">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="8db0d-270">È chiaro quando questo tipo è in uso (il relativo GUID è composto da tutti zero).</span><span class="sxs-lookup"><span data-stu-id="8db0d-270">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="8db0d-271">L'app di esempio dimostra le durate degli oggetti all'interno e tra le singole richieste.</span><span class="sxs-lookup"><span data-stu-id="8db0d-271">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="8db0d-272">L'`IndexModel` dell'app di esempio richiede ogni genere di tipo `IOperation` e `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-272">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="8db0d-273">La pagina visualizza quindi tutti i valori `OperationId` della classe del modello di pagina e del servizio tramite assegnazioni di proprietà:</span><span class="sxs-lookup"><span data-stu-id="8db0d-273">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="8db0d-274">L'output seguente mostra i risultati delle due richieste:</span><span class="sxs-lookup"><span data-stu-id="8db0d-274">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="8db0d-275">**Prima richiesta:**</span><span class="sxs-lookup"><span data-stu-id="8db0d-275">**First request:**</span></span>

<span data-ttu-id="8db0d-276">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="8db0d-276">Controller operations:</span></span>

<span data-ttu-id="8db0d-277">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="8db0d-277">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="8db0d-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="8db0d-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="8db0d-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8db0d-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8db0d-280">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8db0d-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8db0d-281">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="8db0d-281">`OperationService` operations:</span></span>

<span data-ttu-id="8db0d-282">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="8db0d-282">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="8db0d-283">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="8db0d-283">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="8db0d-284">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8db0d-284">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8db0d-285">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8db0d-285">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8db0d-286">**Seconda richiesta:**</span><span class="sxs-lookup"><span data-stu-id="8db0d-286">**Second request:**</span></span>

<span data-ttu-id="8db0d-287">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="8db0d-287">Controller operations:</span></span>

<span data-ttu-id="8db0d-288">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="8db0d-288">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="8db0d-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="8db0d-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="8db0d-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8db0d-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8db0d-291">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8db0d-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8db0d-292">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="8db0d-292">`OperationService` operations:</span></span>

<span data-ttu-id="8db0d-293">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="8db0d-293">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="8db0d-294">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="8db0d-294">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="8db0d-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8db0d-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8db0d-296">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8db0d-296">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8db0d-297">Osservare i valori `OperationId` che variano all'interno della richiesta e da una richiesta e l'altra:</span><span class="sxs-lookup"><span data-stu-id="8db0d-297">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="8db0d-298">Gli oggetti *temporanei* sono sempre diversi.</span><span class="sxs-lookup"><span data-stu-id="8db0d-298">*Transient* objects are always different.</span></span> <span data-ttu-id="8db0d-299">Il valore `OperationId` temporaneo per la prima e la seconda richiesta client è diverso per entrambe le operazioni `OperationService` e in tutte le richieste client.</span><span class="sxs-lookup"><span data-stu-id="8db0d-299">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="8db0d-300">Viene fornita una nuova istanza per ogni richiesta di servizio e richiesta client.</span><span class="sxs-lookup"><span data-stu-id="8db0d-300">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="8db0d-301">Gli oggetti *con ambito* sono gli stessi all'interno di una richiesta client, ma variano nelle diverse richieste client.</span><span class="sxs-lookup"><span data-stu-id="8db0d-301">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="8db0d-302">Gli oggetti *singleton* sono gli stessi per ogni oggetto e per ogni richiesta, indipendentemente dal fatto che un' `Operation` istanza venga fornita in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8db0d-302">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="8db0d-303">Chiamare i servizi da main</span><span class="sxs-lookup"><span data-stu-id="8db0d-303">Call services from main</span></span>

<span data-ttu-id="8db0d-304">Creare un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>IServiceScope con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) per risolvere un servizio con ambito nell'ambito di applicazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="8db0d-304">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="8db0d-305">Questo approccio è utile per l'accesso a un servizio con ambito all'avvio e per l'esecuzione di attività di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="8db0d-305">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="8db0d-306">L'esempio seguente indica come ottenere un contesto per `MyScopedService` in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="8db0d-306">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

## <a name="scope-validation"></a><span data-ttu-id="8db0d-307">Convalida dell'ambito</span><span class="sxs-lookup"><span data-stu-id="8db0d-307">Scope validation</span></span>

<span data-ttu-id="8db0d-308">Quando l'app è in esecuzione nell'ambiente di sviluppo e chiama [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) per compilare l'host, il provider di servizi predefinito esegue i controlli per verificare che:</span><span class="sxs-lookup"><span data-stu-id="8db0d-308">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="8db0d-309">I servizi con ambito non vengano risolti direttamente o indirettamente dal provider di servizi radice.</span><span class="sxs-lookup"><span data-stu-id="8db0d-309">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="8db0d-310">I servizi con ambito non vengano inseriti direttamente o indirettamente in singleton.</span><span class="sxs-lookup"><span data-stu-id="8db0d-310">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="8db0d-311">Il provider di servizi radice venga creato con la chiamata di <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="8db0d-311">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="8db0d-312">La durata del provider di servizi radice corrisponde alla durata dell'app/server quando il provider viene avviato con l'app e viene eliminato alla chiusura dell'app.</span><span class="sxs-lookup"><span data-stu-id="8db0d-312">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="8db0d-313">I servizi con ambito vengono eliminati dal contenitore che li ha creati.</span><span class="sxs-lookup"><span data-stu-id="8db0d-313">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="8db0d-314">Se un servizio con ambito viene creato nel contenitore radice, la durata del servizio viene di fatto convertita in singleton, perché il servizio viene eliminato solo dal contenitore radice alla chiusura dell'app/server.</span><span class="sxs-lookup"><span data-stu-id="8db0d-314">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="8db0d-315">La convalida degli ambiti servizio rileva queste situazioni nel contesto della chiamata di `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-315">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="8db0d-316">Per altre informazioni, vedere <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="8db0d-316">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="8db0d-317">Servizi di richiesta</span><span class="sxs-lookup"><span data-stu-id="8db0d-317">Request Services</span></span>

<span data-ttu-id="8db0d-318">I servizi disponibili all'interno di una richiesta ASP.NET Core da `HttpContext` vengono esposti tramite la raccolta [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="8db0d-318">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="8db0d-319">I servizi di richiesta rappresentano i servizi configurati e richiesti come parte dell'app.</span><span class="sxs-lookup"><span data-stu-id="8db0d-319">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="8db0d-320">Quando gli oggetti specificano dipendenze, le dipendenze sono soddisfatte dai tipi individuati in `RequestServices`, non in `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-320">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="8db0d-321">In generale, l'app non deve usare queste proprietà direttamente.</span><span class="sxs-lookup"><span data-stu-id="8db0d-321">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="8db0d-322">Richiedere invece i tipi richiesti dalle classi tramite costruttori di classe e consentire al Framework di inserire le dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8db0d-322">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="8db0d-323">Si ottengono classi più facili da testare.</span><span class="sxs-lookup"><span data-stu-id="8db0d-323">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="8db0d-324">È consigliabile richiedere le dipendenze come parametri del costruttore per l'accesso alla raccolta `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-324">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="8db0d-325">Progettare i servizi per l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="8db0d-325">Design services for dependency injection</span></span>

<span data-ttu-id="8db0d-326">Le procedure consigliate prevedono di:</span><span class="sxs-lookup"><span data-stu-id="8db0d-326">Best practices are to:</span></span>

* <span data-ttu-id="8db0d-327">Progettare i servizi per usare l'inserimento delle dipendenze per ottenere le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8db0d-327">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="8db0d-328">Evitare membri e classi statiche con stato.</span><span class="sxs-lookup"><span data-stu-id="8db0d-328">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="8db0d-329">Progettare le app in modo da usare i servizi singleton, evitando così di creare uno stato globale.</span><span class="sxs-lookup"><span data-stu-id="8db0d-329">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="8db0d-330">Evitare la creazione diretta di istanze delle classi dipendenti all'interno di servizi.</span><span class="sxs-lookup"><span data-stu-id="8db0d-330">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="8db0d-331">La creazione diretta di istanze associa il codice a una determinata implementazione.</span><span class="sxs-lookup"><span data-stu-id="8db0d-331">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="8db0d-332">Fare in modo che le classi siano piccole, con factoring corretto e facili da testare.</span><span class="sxs-lookup"><span data-stu-id="8db0d-332">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="8db0d-333">Se una classe sembra avere troppe dipendenze inserite, ciò significa in genere che la classe ha un numero eccessivo di responsabilità e sta violando il [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (principio di responsabilità singola).</span><span class="sxs-lookup"><span data-stu-id="8db0d-333">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="8db0d-334">Tentare di effettuare il refactoring della classe spostando alcune delle responsabilità in una nuova classe.</span><span class="sxs-lookup"><span data-stu-id="8db0d-334">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="8db0d-335">Tenere presente che Razor le classi del modello della pagina delle pagine e le classi controller MVC devono concentrarsi sulle problematiche dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="8db0d-335">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="8db0d-336">Di conseguenza, le regole business e i dettagli di implementazione di accesso ai dati devono essere inseriti in classi appropriate per queste [problematiche separate](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="8db0d-336">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="8db0d-337">Eliminazione dei servizi</span><span class="sxs-lookup"><span data-stu-id="8db0d-337">Disposal of services</span></span>

<span data-ttu-id="8db0d-338">Il contenitore chiama <xref:System.IDisposable.Dispose*> per i tipi <xref:System.IDisposable> creati.</span><span class="sxs-lookup"><span data-stu-id="8db0d-338">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="8db0d-339">Se un'istanza viene aggiunta al contenitore dal codice utente, non viene eliminata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="8db0d-339">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="8db0d-340">Nell'esempio seguente i servizi vengono creati dal contenitore del servizio ed eliminati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="8db0d-340">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="8db0d-341">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="8db0d-341">In the following example:</span></span>

* <span data-ttu-id="8db0d-342">Le istanze del servizio non vengono create dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="8db0d-342">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="8db0d-343">Le durate di servizio previste non sono note dal Framework.</span><span class="sxs-lookup"><span data-stu-id="8db0d-343">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="8db0d-344">Il Framework non elimina automaticamente i servizi.</span><span class="sxs-lookup"><span data-stu-id="8db0d-344">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="8db0d-345">Se i servizi non vengono eliminati in modo esplicito nel codice dello sviluppatore, vengono mantenuti fino a quando l'app non viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="8db0d-345">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="8db0d-346">Linee guida IDisposable per istanze temporanee e condivise</span><span class="sxs-lookup"><span data-stu-id="8db0d-346">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="8db0d-347">Durata temporanea e limitata</span><span class="sxs-lookup"><span data-stu-id="8db0d-347">Transient, limited lifetime</span></span>

<span data-ttu-id="8db0d-348">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="8db0d-348">**Scenario**</span></span>

<span data-ttu-id="8db0d-349">L'app richiede un' <xref:System.IDisposable> istanza con una durata temporanea per uno degli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="8db0d-349">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="8db0d-350">L'istanza viene risolta nell'ambito radice.</span><span class="sxs-lookup"><span data-stu-id="8db0d-350">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="8db0d-351">L'istanza deve essere eliminata prima della fine dell'ambito.</span><span class="sxs-lookup"><span data-stu-id="8db0d-351">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="8db0d-352">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="8db0d-352">**Solution**</span></span>

<span data-ttu-id="8db0d-353">Usare il modello factory per creare un'istanza all'esterno dell'ambito padre.</span><span class="sxs-lookup"><span data-stu-id="8db0d-353">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="8db0d-354">In questa situazione, in genere l'app dispone di un `Create` metodo che chiama direttamente il costruttore del tipo finale.</span><span class="sxs-lookup"><span data-stu-id="8db0d-354">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="8db0d-355">Se il tipo finale ha altre dipendenze, la factory può:</span><span class="sxs-lookup"><span data-stu-id="8db0d-355">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="8db0d-356">Ricevere un oggetto <xref:System.IServiceProvider> nel relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="8db0d-356">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="8db0d-357">Usare <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> per creare un'istanza dell'istanza all'esterno del contenitore, usando il contenitore per le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8db0d-357">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="8db0d-358">Istanza condivisa, durata limitata</span><span class="sxs-lookup"><span data-stu-id="8db0d-358">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="8db0d-359">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="8db0d-359">**Scenario**</span></span>

<span data-ttu-id="8db0d-360">L'app richiede un' <xref:System.IDisposable> istanza condivisa tra più servizi, ma <xref:System.IDisposable> deve avere una durata limitata.</span><span class="sxs-lookup"><span data-stu-id="8db0d-360">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="8db0d-361">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="8db0d-361">**Solution**</span></span>

<span data-ttu-id="8db0d-362">Registrare l'istanza con una durata con ambito.</span><span class="sxs-lookup"><span data-stu-id="8db0d-362">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="8db0d-363">Usare <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> per avviare e creare un nuovo oggetto <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="8db0d-363">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="8db0d-364">Usare l'ambito <xref:System.IServiceProvider> per ottenere i servizi richiesti.</span><span class="sxs-lookup"><span data-stu-id="8db0d-364">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="8db0d-365">Eliminare l'ambito quando la durata deve terminare.</span><span class="sxs-lookup"><span data-stu-id="8db0d-365">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="8db0d-366">Linee guida generali</span><span class="sxs-lookup"><span data-stu-id="8db0d-366">General Guidelines</span></span>

* <span data-ttu-id="8db0d-367">Non registrare <xref:System.IDisposable> istanze con un ambito temporaneo.</span><span class="sxs-lookup"><span data-stu-id="8db0d-367">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="8db0d-368">Usare invece il modello Factory.</span><span class="sxs-lookup"><span data-stu-id="8db0d-368">Use the factory pattern instead.</span></span>
* <span data-ttu-id="8db0d-369">Non risolvere istanze temporanee o <xref:System.IDisposable> con ambito nell'ambito radice.</span><span class="sxs-lookup"><span data-stu-id="8db0d-369">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="8db0d-370">L'unica eccezione generale si verifica quando l'app crea/ricrea ed Elimina <xref:System.IServiceProvider> , che non è un modello ideale.</span><span class="sxs-lookup"><span data-stu-id="8db0d-370">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="8db0d-371">Per <xref:System.IDisposable> la ricezione di una dipendenza tramite di non è necessario che il ricevitore implementi <xref:System.IDisposable> se stesso.</span><span class="sxs-lookup"><span data-stu-id="8db0d-371">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="8db0d-372">Il ricevitore della <xref:System.IDisposable> dipendenza non deve chiamare <xref:System.IDisposable.Dispose%2A> su tale dipendenza.</span><span class="sxs-lookup"><span data-stu-id="8db0d-372">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="8db0d-373">Gli ambiti devono essere usati per controllare la durata dei servizi.</span><span class="sxs-lookup"><span data-stu-id="8db0d-373">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="8db0d-374">Gli ambiti non sono gerarchici e non esiste alcuna connessione speciale tra gli ambiti.</span><span class="sxs-lookup"><span data-stu-id="8db0d-374">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="8db0d-375">Sostituzione del contenitore di servizi predefinito</span><span class="sxs-lookup"><span data-stu-id="8db0d-375">Default service container replacement</span></span>

<span data-ttu-id="8db0d-376">Il contenitore dei servizi incorporato è progettato per soddisfare le esigenze del Framework e della maggior parte delle app consumer.</span><span class="sxs-lookup"><span data-stu-id="8db0d-376">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="8db0d-377">Si consiglia di usare il contenitore predefinito a meno che non sia necessaria una funzionalità specifica che il contenitore incorporato non supporta, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8db0d-377">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="8db0d-378">Inserimento di proprietà</span><span class="sxs-lookup"><span data-stu-id="8db0d-378">Property injection</span></span>
* <span data-ttu-id="8db0d-379">Inserimento in base al nome</span><span class="sxs-lookup"><span data-stu-id="8db0d-379">Injection based on name</span></span>
* <span data-ttu-id="8db0d-380">Contenitori figlio</span><span class="sxs-lookup"><span data-stu-id="8db0d-380">Child containers</span></span>
* <span data-ttu-id="8db0d-381">Gestione della durata personalizzata</span><span class="sxs-lookup"><span data-stu-id="8db0d-381">Custom lifetime management</span></span>
* <span data-ttu-id="8db0d-382">Supporto di `Func<T>` per l'inizializzazione differita</span><span class="sxs-lookup"><span data-stu-id="8db0d-382">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="8db0d-383">Registrazione basata sulle convenzioni</span><span class="sxs-lookup"><span data-stu-id="8db0d-383">Convention-based registration</span></span>

<span data-ttu-id="8db0d-384">Con ASP.NET Core app è possibile usare i contenitori di terze parti seguenti:</span><span class="sxs-lookup"><span data-stu-id="8db0d-384">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="8db0d-385">Autofac</span><span class="sxs-lookup"><span data-stu-id="8db0d-385">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="8db0d-386">DryIoc</span><span class="sxs-lookup"><span data-stu-id="8db0d-386">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="8db0d-387">Laura</span><span class="sxs-lookup"><span data-stu-id="8db0d-387">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="8db0d-388">LightInject</span><span class="sxs-lookup"><span data-stu-id="8db0d-388">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="8db0d-389">Lamar</span><span class="sxs-lookup"><span data-stu-id="8db0d-389">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="8db0d-390">Stashbox</span><span class="sxs-lookup"><span data-stu-id="8db0d-390">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="8db0d-391">Unity</span><span class="sxs-lookup"><span data-stu-id="8db0d-391">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="8db0d-392">Thread safety</span><span class="sxs-lookup"><span data-stu-id="8db0d-392">Thread safety</span></span>

<span data-ttu-id="8db0d-393">Creare servizi singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="8db0d-393">Create thread-safe singleton services.</span></span> <span data-ttu-id="8db0d-394">Se un servizio singleton ha una dipendenza in un servizio temporaneo, è necessario che anche il servizio temporaneo sia thread-safe, a seconda di come viene usato dal singleton.</span><span class="sxs-lookup"><span data-stu-id="8db0d-394">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="8db0d-395">Il metodo factory di un singolo servizio, ad esempio il secondo argomento di [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), non deve essere thread-safe.</span><span class="sxs-lookup"><span data-stu-id="8db0d-395">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="8db0d-396">Come nel caso di un costruttore di tipo (`static`), è garantito che venga chiamato una sola volta da un singolo thread.</span><span class="sxs-lookup"><span data-stu-id="8db0d-396">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="8db0d-397">Consigli</span><span class="sxs-lookup"><span data-stu-id="8db0d-397">Recommendations</span></span>

* <span data-ttu-id="8db0d-398">La risoluzione basata sui servizi `async/await` e `Task` non è supportata.</span><span class="sxs-lookup"><span data-stu-id="8db0d-398">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="8db0d-399">Dato che C# non supporta i costruttori asincroni, il modello consigliato consiste nell'usare i metodi asincroni dopo avere risolto in modo sincrono il servizio.</span><span class="sxs-lookup"><span data-stu-id="8db0d-399">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="8db0d-400">Evitare di archiviare i dati e la configurazione direttamente nel contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="8db0d-400">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="8db0d-401">Ad esempio, il carrello acquisti di un utente non dovrebbe in genere essere aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="8db0d-401">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="8db0d-402">La configurazione deve usare il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="8db0d-402">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="8db0d-403">Analogamente, evitare gli oggetti "contenitori di dati" che hanno la sola funzione di consentire l'accesso ad altri oggetti.</span><span class="sxs-lookup"><span data-stu-id="8db0d-403">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="8db0d-404">È preferibile richiedere l'elemento effettivo tramite inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8db0d-404">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="8db0d-405">Evitare l'accesso statico ai servizi (ad esempio, la tipizzazione statica [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) da usare in altre posizioni).</span><span class="sxs-lookup"><span data-stu-id="8db0d-405">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="8db0d-406">Evitare di usare il *modello di localizzatore del servizio*.</span><span class="sxs-lookup"><span data-stu-id="8db0d-406">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="8db0d-407">Ad esempio, non richiamare <xref:System.IServiceProvider.GetService*> per ottenere un'istanza del servizio quando è invece possibile usare l'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="8db0d-407">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="8db0d-408">**Errata**</span><span class="sxs-lookup"><span data-stu-id="8db0d-408">**Incorrect:**</span></span>

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  <span data-ttu-id="8db0d-409">**Corretto**:</span><span class="sxs-lookup"><span data-stu-id="8db0d-409">**Correct**:</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

* <span data-ttu-id="8db0d-410">Un'altra variazione del localizzatore del servizio da evitare è inserire una factory che risolve le dipendenze in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="8db0d-410">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="8db0d-411">Queste procedure combinano le strategie di [inversione del controllo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="8db0d-411">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="8db0d-412">Evitare l'accesso statico a `HttpContext` (ad esempio [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="8db0d-412">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="8db0d-413">È tuttavia possibile che in alcuni casi queste raccomandazioni debbano essere ignorate.</span><span class="sxs-lookup"><span data-stu-id="8db0d-413">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="8db0d-414">Le eccezioni sono rari, soprattutto casi speciali all'interno del Framework stesso.</span><span class="sxs-lookup"><span data-stu-id="8db0d-414">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="8db0d-415">L'inserimento di dipendenze è un'*alternativa* ai modelli di accesso agli oggetti statici/globali.</span><span class="sxs-lookup"><span data-stu-id="8db0d-415">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="8db0d-416">Se l'inserimento di dipendenze viene usato con l'accesso agli oggetti statico i vantaggi non saranno evidenti.</span><span class="sxs-lookup"><span data-stu-id="8db0d-416">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="8db0d-417">Modelli consigliati per il multi-tenant in DI</span><span class="sxs-lookup"><span data-stu-id="8db0d-417">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="8db0d-418">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) fornisce il multi-tenant.</span><span class="sxs-lookup"><span data-stu-id="8db0d-418">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="8db0d-419">Per ulteriori informazioni, vedere la [documentazione di Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="8db0d-419">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="8db0d-420">Vedere le app di esempio in https://github.com/OrchardCMS/OrchardCore.Samples per esempi di come creare app modulari e multi-tenant usando solo il Framework Orchard core senza le funzionalità specifiche del CMS.</span><span class="sxs-lookup"><span data-stu-id="8db0d-420">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8db0d-421">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8db0d-421">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="8db0d-422">Quattro modi per eliminare IDisposable in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8db0d-422">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="8db0d-423">Scrittura di codice pulito in ASP.NET Core con inserimento delle dipendenze (MSDN)</span><span class="sxs-lookup"><span data-stu-id="8db0d-423">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="8db0d-424">Explicit Dependencies Principle (Principio delle dipendenze esplicite)</span><span class="sxs-lookup"><span data-stu-id="8db0d-424">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="8db0d-425">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler) (Contenitori di inversione del controllo e modello di inserimento delle dipendenze)</span><span class="sxs-lookup"><span data-stu-id="8db0d-425">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="8db0d-426">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Come registrare un servizio con più interfacce in ASP.NET Core DI)</span><span class="sxs-lookup"><span data-stu-id="8db0d-426">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8db0d-427">ASP.NET Core supporta il modello progettuale software per l'inserimento delle dipendenze, ovvero una tecnica per ottenere l'[inversione del controllo (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra classi e relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8db0d-427">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="8db0d-428">Per altre informazioni specifiche per l'inserimento delle dipendenze all'interno di controller MVC, vedere <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="8db0d-428">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="8db0d-429">[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8db0d-429">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="8db0d-430">Panoramica dell'inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="8db0d-430">Overview of dependency injection</span></span>

<span data-ttu-id="8db0d-431">Una *dipendenza* è qualsiasi oggetto richiesto da un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="8db0d-431">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="8db0d-432">Esaminare la classe `MyDependency` seguente con un metodo `WriteMessage` da cui dipendono altre classi in un'app:</span><span class="sxs-lookup"><span data-stu-id="8db0d-432">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="8db0d-433">È possibile creare un'istanza della classe `MyDependency` per rendere il metodo `WriteMessage`disponibile per una classe.</span><span class="sxs-lookup"><span data-stu-id="8db0d-433">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="8db0d-434">La classe `MyDependency` è una dipendenza della classe `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="8db0d-434">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="8db0d-435">La classe crea e dipende direttamente dall'istanza `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-435">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="8db0d-436">Le dipendenze del codice (come nell'esempio precedente) sono problematiche e devono essere evitate per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8db0d-436">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="8db0d-437">Per sostituire `MyDependency` con un'implementazione diversa, la classe deve essere modificata.</span><span class="sxs-lookup"><span data-stu-id="8db0d-437">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="8db0d-438">Se `MyDependency` ha dipendenze, devono essere configurate dalla classe.</span><span class="sxs-lookup"><span data-stu-id="8db0d-438">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="8db0d-439">In un progetto di grandi dimensioni con più classi che dipendono da `MyDependency`, il codice di configurazione diventa sparso in tutta l'app.</span><span class="sxs-lookup"><span data-stu-id="8db0d-439">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="8db0d-440">È difficile eseguire unit test di questa implementazione.</span><span class="sxs-lookup"><span data-stu-id="8db0d-440">This implementation is difficult to unit test.</span></span> <span data-ttu-id="8db0d-441">L'app dovrebbe usare una classe `MyDependency` fittizia o stub, ma ciò non è possibile con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="8db0d-441">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="8db0d-442">L'inserimento delle dipendenze consente di risolvere questi problemi tramite:</span><span class="sxs-lookup"><span data-stu-id="8db0d-442">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="8db0d-443">L'uso di un'interfaccia o di una classe di base per astrarre l'implementazione delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8db0d-443">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="8db0d-444">La registrazione della dipendenza in un contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="8db0d-444">Registration of the dependency in a service container.</span></span> <span data-ttu-id="8db0d-445">ASP.NET Core offre il contenitore di servizi predefinito <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="8db0d-445">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="8db0d-446">I servizi vengono registrati nel metodo `Startup.ConfigureServices` dell'app.</span><span class="sxs-lookup"><span data-stu-id="8db0d-446">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="8db0d-447">L'*inserimento* del servizio nel costruttore della classe in cui viene usato.</span><span class="sxs-lookup"><span data-stu-id="8db0d-447">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="8db0d-448">Il framework si assume la responsabilità della creazione di un'istanza della dipendenza e della sua eliminazione quando non è più necessaria.</span><span class="sxs-lookup"><span data-stu-id="8db0d-448">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="8db0d-449">Nell'[app di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l'interfaccia `IMyDependency` definisce un metodo che il servizio fornisce all'app:</span><span class="sxs-lookup"><span data-stu-id="8db0d-449">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="8db0d-450">Questa interfaccia viene implementata da un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="8db0d-450">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="8db0d-451">`MyDependency` richiede <xref:Microsoft.Extensions.Logging.ILogger`1> nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="8db0d-451">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="8db0d-452">Non è insolito usare l'inserimento delle dipendenze in modo concatenato.</span><span class="sxs-lookup"><span data-stu-id="8db0d-452">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="8db0d-453">Ogni dipendenza richiesta richiede a sua volta le proprie dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8db0d-453">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="8db0d-454">Il contenitore risolve le dipendenze nel grafico e restituisce il servizio completamente risolto.</span><span class="sxs-lookup"><span data-stu-id="8db0d-454">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="8db0d-455">Il set di dipendenze che devono essere risolte viene generalmente chiamato *albero delle dipendenze* o *grafico dipendenze* o *grafico degli oggetti*.</span><span class="sxs-lookup"><span data-stu-id="8db0d-455">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="8db0d-456">`IMyDependency` e `ILogger<TCategoryName>` devono essere registrati nel contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="8db0d-456">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="8db0d-457">`IMyDependency` viene registrato in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-457">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8db0d-458">`ILogger<TCategoryName>`viene registrato dall'infrastruttura di registrazione delle astrazioni, pertanto si tratta di un [servizio fornito dal Framework](#framework-provided-services) registrato per impostazione predefinita dal Framework.</span><span class="sxs-lookup"><span data-stu-id="8db0d-458">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="8db0d-459">Il contenitore risolve `ILogger<TCategoryName>` avvalendosi di [tipi aperti (generici)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando l'esigenza di registrare ogni singolo [tipo costruito (generico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="8db0d-459">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="8db0d-460">Nell'app di esempio, il servizio `IMyDependency` viene registrato con il tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-460">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="8db0d-461">La registrazione definisce come ambito della durata di servizio la durata di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="8db0d-461">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="8db0d-462">Le [durate dei servizi](#service-lifetimes) sono descritte più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="8db0d-462">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="8db0d-463">Ogni metodo di estensione `services.Add{SERVICE_NAME}` aggiunge (e potenzialmente configura) i servizi.</span><span class="sxs-lookup"><span data-stu-id="8db0d-463">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="8db0d-464">Ad esempio, `services.AddMvc()` aggiunge le pagine dei servizi Razor e MVC require.</span><span class="sxs-lookup"><span data-stu-id="8db0d-464">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="8db0d-465">È consigliabile che le app seguano questa convenzione.</span><span class="sxs-lookup"><span data-stu-id="8db0d-465">We recommended that apps follow this convention.</span></span> <span data-ttu-id="8db0d-466">Inserire i metodi di estensione nello spazio dei nomi [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) per incapsulare i gruppi di registrazioni di servizio.</span><span class="sxs-lookup"><span data-stu-id="8db0d-466">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="8db0d-467">Se il costruttore del servizio richiede un [tipo incorporato](/dotnet/csharp/language-reference/keywords/built-in-types-table), ad esempio `string`, è possibile inserirlo usando la [configurazione](xref:fundamentals/configuration/index) o il [modello di opzioni](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="8db0d-467">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="8db0d-468">È richiesta un'istanza del servizio tramite il costruttore di una classe in cui il servizio viene usato e assegnato a un campo privato.</span><span class="sxs-lookup"><span data-stu-id="8db0d-468">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="8db0d-469">Il campo viene usato per accedere al servizio in base alle esigenze per l'intera classe.</span><span class="sxs-lookup"><span data-stu-id="8db0d-469">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="8db0d-470">Nell'app di esempio, viene richiesta l'istanza `IMyDependency` usata per chiamare il metodo `WriteMessage` del servizio:</span><span class="sxs-lookup"><span data-stu-id="8db0d-470">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="8db0d-471">Servizi inseriti all'avvio</span><span class="sxs-lookup"><span data-stu-id="8db0d-471">Services injected into Startup</span></span>

<span data-ttu-id="8db0d-472">`Startup`Quando si utilizza l'host generico () è possibile inserire nel costruttore solo i tipi di servizio seguenti <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="8db0d-472">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="8db0d-473">I servizi possono essere inseriti in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="8db0d-473">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="8db0d-474">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="8db0d-474">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="8db0d-475">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="8db0d-475">Framework-provided services</span></span>

<span data-ttu-id="8db0d-476">Il `Startup.ConfigureServices` metodo è responsabile della definizione dei servizi utilizzati dall'app, incluse le funzionalità della piattaforma, ad esempio Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="8db0d-476">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="8db0d-477">Inizialmente, l'oggetto `IServiceCollection` fornito a `ConfigureServices` dispone di servizi definiti dal Framework a seconda della [configurazione dell'host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="8db0d-477">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="8db0d-478">Non è insolito che un'app basata su un modello di ASP.NET Core disponga di centinaia di servizi registrati dal Framework.</span><span class="sxs-lookup"><span data-stu-id="8db0d-478">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="8db0d-479">Nella tabella seguente è elencato un piccolo esempio di servizi registrati dal Framework.</span><span class="sxs-lookup"><span data-stu-id="8db0d-479">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="8db0d-480">Tipo di servizio</span><span class="sxs-lookup"><span data-stu-id="8db0d-480">Service Type</span></span> | <span data-ttu-id="8db0d-481">Durata</span><span class="sxs-lookup"><span data-stu-id="8db0d-481">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="8db0d-482">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="8db0d-482">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="8db0d-483">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-483">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="8db0d-484">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-484">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="8db0d-485">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-485">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="8db0d-486">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="8db0d-486">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="8db0d-487">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-487">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="8db0d-488">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="8db0d-488">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="8db0d-489">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-489">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="8db0d-490">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-490">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="8db0d-491">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-491">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="8db0d-492">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="8db0d-492">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="8db0d-493">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-493">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="8db0d-494">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-494">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="8db0d-495">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-495">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="8db0d-496">Registrare servizi aggiuntivi con i metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="8db0d-496">Register additional services with extension methods</span></span>

<span data-ttu-id="8db0d-497">Quando è disponibile un metodo di estensione della raccolta di servizi per la registrazione di un servizio (e dei servizi dipendenti, se necessario), la convenzione consiste nell'usare un singolo metodo di estensione `Add{SERVICE_NAME}` per registrare tutti i servizi richiesti da tale servizio.</span><span class="sxs-lookup"><span data-stu-id="8db0d-497">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="8db0d-498">Il codice seguente è un esempio di come aggiungere servizi aggiuntivi al contenitore usando i metodi di estensione [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="8db0d-498">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="8db0d-499">Per altre informazioni, vedere la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> nella documentazione delle API.</span><span class="sxs-lookup"><span data-stu-id="8db0d-499">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="8db0d-500">Durate dei servizi</span><span class="sxs-lookup"><span data-stu-id="8db0d-500">Service lifetimes</span></span>

<span data-ttu-id="8db0d-501">Scegliere una durata appropriata per ogni servizio registrato.</span><span class="sxs-lookup"><span data-stu-id="8db0d-501">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="8db0d-502">I servizi ASP.NET Core possono essere configurati con le impostazioni di durata seguenti:</span><span class="sxs-lookup"><span data-stu-id="8db0d-502">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="8db0d-503">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="8db0d-503">Transient</span></span>

<span data-ttu-id="8db0d-504">I servizi con durata temporanea (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) vengono creati ogni volta che vengono richiesti dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="8db0d-504">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="8db0d-505">Questa impostazione di durata è consigliata per i servizi semplici senza stati.</span><span class="sxs-lookup"><span data-stu-id="8db0d-505">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="8db0d-506">Con ambito</span><span class="sxs-lookup"><span data-stu-id="8db0d-506">Scoped</span></span>

<span data-ttu-id="8db0d-507">I servizi con durata con ambito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) vengono creati una volta per ogni richiesta client (connessione).</span><span class="sxs-lookup"><span data-stu-id="8db0d-507">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="8db0d-508">Quando si usa un servizio con ambito in un middleware, inserire il servizio nel metodo `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-508">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="8db0d-509">Non inserire tramite il [Costruttore Injection](xref:mvc/controllers/dependency-injection#constructor-injection) perché impone al servizio di comportarsi come un singleton.</span><span class="sxs-lookup"><span data-stu-id="8db0d-509">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="8db0d-510">Per altre informazioni, vedere <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="8db0d-510">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="8db0d-511">Singleton</span><span class="sxs-lookup"><span data-stu-id="8db0d-511">Singleton</span></span>

<span data-ttu-id="8db0d-512">I servizi con durata singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) vengono creati la prima volta che vengono richiesti (o quando si esegue `Startup.ConfigureServices` e viene specificata un'istanza con la registrazione del servizio).</span><span class="sxs-lookup"><span data-stu-id="8db0d-512">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="8db0d-513">Tutte le richieste successive usano la stessa istanza.</span><span class="sxs-lookup"><span data-stu-id="8db0d-513">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="8db0d-514">Se l'app richiede un comportamento singleton, è consigliabile consentire al contenitore del servizio di gestire la durata del servizio.</span><span class="sxs-lookup"><span data-stu-id="8db0d-514">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="8db0d-515">Non implementare lo schema progettuale singleton e fornire codice utente per gestire la durata dell'oggetto nella classe.</span><span class="sxs-lookup"><span data-stu-id="8db0d-515">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="8db0d-516">Può essere pericoloso risolvere un servizio con ambito da un singleton.</span><span class="sxs-lookup"><span data-stu-id="8db0d-516">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="8db0d-517">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="8db0d-517">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="8db0d-518">Metodi di registrazione del servizio</span><span class="sxs-lookup"><span data-stu-id="8db0d-518">Service registration methods</span></span>

<span data-ttu-id="8db0d-519">I metodi di estensione per la registrazione del servizio offrono overload che risultano utili in scenari specifici.</span><span class="sxs-lookup"><span data-stu-id="8db0d-519">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="8db0d-520">Metodo</span><span class="sxs-lookup"><span data-stu-id="8db0d-520">Method</span></span> | <span data-ttu-id="8db0d-521">Automatico</span><span class="sxs-lookup"><span data-stu-id="8db0d-521">Automatic</span></span><br><span data-ttu-id="8db0d-522">object</span><span class="sxs-lookup"><span data-stu-id="8db0d-522">object</span></span><br><span data-ttu-id="8db0d-523">eliminazione</span><span class="sxs-lookup"><span data-stu-id="8db0d-523">disposal</span></span> | <span data-ttu-id="8db0d-524">Multipli</span><span class="sxs-lookup"><span data-stu-id="8db0d-524">Multiple</span></span><br><span data-ttu-id="8db0d-525">implementazioni</span><span class="sxs-lookup"><span data-stu-id="8db0d-525">implementations</span></span> | <span data-ttu-id="8db0d-526">Pass args</span><span class="sxs-lookup"><span data-stu-id="8db0d-526">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="8db0d-527">Esempio:</span><span class="sxs-lookup"><span data-stu-id="8db0d-527">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="8db0d-528">Sì</span><span class="sxs-lookup"><span data-stu-id="8db0d-528">Yes</span></span> | <span data-ttu-id="8db0d-529">Sì</span><span class="sxs-lookup"><span data-stu-id="8db0d-529">Yes</span></span> | <span data-ttu-id="8db0d-530">No</span><span class="sxs-lookup"><span data-stu-id="8db0d-530">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="8db0d-531">Esempi:</span><span class="sxs-lookup"><span data-stu-id="8db0d-531">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="8db0d-532">Sì</span><span class="sxs-lookup"><span data-stu-id="8db0d-532">Yes</span></span> | <span data-ttu-id="8db0d-533">Sì</span><span class="sxs-lookup"><span data-stu-id="8db0d-533">Yes</span></span> | <span data-ttu-id="8db0d-534">Sì</span><span class="sxs-lookup"><span data-stu-id="8db0d-534">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="8db0d-535">Esempio:</span><span class="sxs-lookup"><span data-stu-id="8db0d-535">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="8db0d-536">Sì</span><span class="sxs-lookup"><span data-stu-id="8db0d-536">Yes</span></span> | <span data-ttu-id="8db0d-537">No</span><span class="sxs-lookup"><span data-stu-id="8db0d-537">No</span></span> | <span data-ttu-id="8db0d-538">No</span><span class="sxs-lookup"><span data-stu-id="8db0d-538">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="8db0d-539">Esempi:</span><span class="sxs-lookup"><span data-stu-id="8db0d-539">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="8db0d-540">No</span><span class="sxs-lookup"><span data-stu-id="8db0d-540">No</span></span> | <span data-ttu-id="8db0d-541">Sì</span><span class="sxs-lookup"><span data-stu-id="8db0d-541">Yes</span></span> | <span data-ttu-id="8db0d-542">Sì</span><span class="sxs-lookup"><span data-stu-id="8db0d-542">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="8db0d-543">Esempi:</span><span class="sxs-lookup"><span data-stu-id="8db0d-543">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="8db0d-544">No</span><span class="sxs-lookup"><span data-stu-id="8db0d-544">No</span></span> | <span data-ttu-id="8db0d-545">No</span><span class="sxs-lookup"><span data-stu-id="8db0d-545">No</span></span> | <span data-ttu-id="8db0d-546">Sì</span><span class="sxs-lookup"><span data-stu-id="8db0d-546">Yes</span></span> |

<span data-ttu-id="8db0d-547">Per ulteriori informazioni sull'eliminazione dei tipi, vedere la sezione [Eliminazione dei servizi](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="8db0d-547">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="8db0d-548">Uno scenario comune per più implementazioni è costituito dai [tipi di simulazioni per i test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="8db0d-548">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="8db0d-549">I metodi `TryAdd{LIFETIME}` registrano il servizio solo se esiste già un'implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="8db0d-549">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="8db0d-550">Nell'esempio seguente, la prima riga registra `MyDependency` per `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-550">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="8db0d-551">La seconda riga non ha alcun effetto perché `IMyDependency` dispone già di un'implementazione registrata:</span><span class="sxs-lookup"><span data-stu-id="8db0d-551">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="8db0d-552">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="8db0d-552">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="8db0d-553">I metodi [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrano il servizio solo se non esiste già un'implementazione *dello stesso tipo*.</span><span class="sxs-lookup"><span data-stu-id="8db0d-553">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="8db0d-554">Più servizi vengono risolti tramite `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-554">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="8db0d-555">Durante la registrazione di servizi, lo sviluppatore desidera aggiungere un'istanza solo se non ne è già stata aggiunta una dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="8db0d-555">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="8db0d-556">In genere, questo metodo viene utilizzato dagli autori di librerie per evitare di registrare due copie di un'istanza nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="8db0d-556">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="8db0d-557">Nell'esempio seguente, la prima riga registra `MyDep` per `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-557">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="8db0d-558">La seconda riga registra `MyDep` per `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-558">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="8db0d-559">La seconda riga non ha alcun effetto perché `IMyDep1` dispone già di un'implementazione registrata di `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="8db0d-559">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="8db0d-560">Comportamento dell'inserimento del costruttore</span><span class="sxs-lookup"><span data-stu-id="8db0d-560">Constructor injection behavior</span></span>

<span data-ttu-id="8db0d-561">I servizi possono essere risolti con due meccanismi:</span><span class="sxs-lookup"><span data-stu-id="8db0d-561">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="8db0d-562"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Consente la creazione di oggetti senza registrazione del servizio nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8db0d-562"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="8db0d-563">`ActivatorUtilities` viene usato con astrazioni esposte all'utente, ad esempio helper tag, controller MVC e strumenti di associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="8db0d-563">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="8db0d-564">I costruttori possono accettare argomenti non forniti tramite l'inserimento di dipendenze, ma gli argomenti devono assegnare valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="8db0d-564">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="8db0d-565">Quando i servizi vengono risolti da `IServiceProvider` o `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede un costruttore *pubblico* .</span><span class="sxs-lookup"><span data-stu-id="8db0d-565">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="8db0d-566">Quando i servizi vengono risolti da `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede l'esistenza di un solo costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="8db0d-566">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="8db0d-567">Sebbene siano supportati gli overload dei costruttori, può essere presente un solo overload i cui argomenti possono essere tutti specificati tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8db0d-567">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="8db0d-568">Contesti di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="8db0d-568">Entity Framework contexts</span></span>

<span data-ttu-id="8db0d-569">I contesti di Entity Framework vengono in genere aggiunti al contenitore di servizi usando la [durata con ambito](#service-lifetimes) perché le operazioni di database delle app Web hanno di solito come ambito la richiesta client.</span><span class="sxs-lookup"><span data-stu-id="8db0d-569">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="8db0d-570">L'ambito della durata predefinita è se una durata non è specificata da un overload [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) durante la registrazione del contesto del database.</span><span class="sxs-lookup"><span data-stu-id="8db0d-570">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="8db0d-571">I servizi con una durata specificata non devono usare un contesto di database con una durata più breve rispetto al servizio.</span><span class="sxs-lookup"><span data-stu-id="8db0d-571">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="8db0d-572">Opzioni di durata e di registrazione</span><span class="sxs-lookup"><span data-stu-id="8db0d-572">Lifetime and registration options</span></span>

<span data-ttu-id="8db0d-573">Per illustrare la differenza tra le opzioni di durata e registrazione, si considerino le interfacce seguenti che rappresentano le attività come un'operazione con un identificatore univoco, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-573">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="8db0d-574">A seconda del modo in cui la durata di un servizio operativo viene configurata per le interfacce seguenti, il contenitore fornisce la stessa istanza o un'istanza diversa del servizio quando richiesto da una classe:</span><span class="sxs-lookup"><span data-stu-id="8db0d-574">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="8db0d-575">Le interfacce vengono implementate nella classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-575">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="8db0d-576">Il costruttore `Operation` genera un GUID se non ne viene fornito uno:</span><span class="sxs-lookup"><span data-stu-id="8db0d-576">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="8db0d-577">Viene registrato un `OperationService` che dipende da ognuno degli altri tipi `Operation`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-577">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="8db0d-578">Quando viene richiesto `OperationService` tramite l'inserimento delle dipendenze, riceve una nuova istanza di ogni servizio o un'istanza esistente in base alla durata del servizio dipendente.</span><span class="sxs-lookup"><span data-stu-id="8db0d-578">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="8db0d-579">Se i servizi temporanei vengono creati quando vengono richiesti dal contenitore, `OperationId` del servizio `IOperationTransient` è diverso da `OperationId` di `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-579">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="8db0d-580">`OperationService` riceve una nuova istanza della classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-580">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="8db0d-581">La nuova istanza restituisce un `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="8db0d-581">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="8db0d-582">Se i servizi con ambito vengono creati per ogni richiesta client, `OperationId` del servizio `IOperationScoped` corrisponde a `OperationService` all'interno di una richiesta client.</span><span class="sxs-lookup"><span data-stu-id="8db0d-582">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="8db0d-583">In tutte le richieste client entrambi i servizi condividono un valore `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="8db0d-583">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="8db0d-584">Se i servizi singleton e con istanza singleton vengono creati una sola volta e usati per tutte le richieste client e tutti i servizi, `OperationId` rimane costante in tutte le richieste di servizio.</span><span class="sxs-lookup"><span data-stu-id="8db0d-584">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="8db0d-585">In `Startup.ConfigureServices`, ogni tipo viene aggiunto al contenitore in base alla durata denominata:</span><span class="sxs-lookup"><span data-stu-id="8db0d-585">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="8db0d-586">Il servizio `IOperationSingletonInstance` usa un'istanza specifica con un ID noto di `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-586">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="8db0d-587">È chiaro quando questo tipo è in uso (il relativo GUID è composto da tutti zero).</span><span class="sxs-lookup"><span data-stu-id="8db0d-587">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="8db0d-588">L'app di esempio dimostra le durate degli oggetti all'interno e tra le singole richieste.</span><span class="sxs-lookup"><span data-stu-id="8db0d-588">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="8db0d-589">L'`IndexModel` dell'app di esempio richiede ogni genere di tipo `IOperation` e `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-589">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="8db0d-590">La pagina visualizza quindi tutti i valori `OperationId` della classe del modello di pagina e del servizio tramite assegnazioni di proprietà:</span><span class="sxs-lookup"><span data-stu-id="8db0d-590">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="8db0d-591">L'output seguente mostra i risultati delle due richieste:</span><span class="sxs-lookup"><span data-stu-id="8db0d-591">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="8db0d-592">**Prima richiesta:**</span><span class="sxs-lookup"><span data-stu-id="8db0d-592">**First request:**</span></span>

<span data-ttu-id="8db0d-593">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="8db0d-593">Controller operations:</span></span>

<span data-ttu-id="8db0d-594">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="8db0d-594">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="8db0d-595">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="8db0d-595">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="8db0d-596">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8db0d-596">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8db0d-597">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8db0d-597">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8db0d-598">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="8db0d-598">`OperationService` operations:</span></span>

<span data-ttu-id="8db0d-599">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="8db0d-599">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="8db0d-600">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="8db0d-600">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="8db0d-601">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8db0d-601">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8db0d-602">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8db0d-602">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8db0d-603">**Seconda richiesta:**</span><span class="sxs-lookup"><span data-stu-id="8db0d-603">**Second request:**</span></span>

<span data-ttu-id="8db0d-604">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="8db0d-604">Controller operations:</span></span>

<span data-ttu-id="8db0d-605">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="8db0d-605">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="8db0d-606">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="8db0d-606">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="8db0d-607">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8db0d-607">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8db0d-608">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8db0d-608">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8db0d-609">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="8db0d-609">`OperationService` operations:</span></span>

<span data-ttu-id="8db0d-610">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="8db0d-610">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="8db0d-611">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="8db0d-611">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="8db0d-612">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8db0d-612">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8db0d-613">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8db0d-613">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8db0d-614">Osservare i valori `OperationId` che variano all'interno della richiesta e da una richiesta e l'altra:</span><span class="sxs-lookup"><span data-stu-id="8db0d-614">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="8db0d-615">Gli oggetti *temporanei* sono sempre diversi.</span><span class="sxs-lookup"><span data-stu-id="8db0d-615">*Transient* objects are always different.</span></span> <span data-ttu-id="8db0d-616">Il valore `OperationId` temporaneo per la prima e la seconda richiesta client è diverso per entrambe le operazioni `OperationService` e in tutte le richieste client.</span><span class="sxs-lookup"><span data-stu-id="8db0d-616">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="8db0d-617">Viene fornita una nuova istanza per ogni richiesta di servizio e richiesta client.</span><span class="sxs-lookup"><span data-stu-id="8db0d-617">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="8db0d-618">Gli oggetti *con ambito* sono gli stessi all'interno di una richiesta client, ma variano nelle diverse richieste client.</span><span class="sxs-lookup"><span data-stu-id="8db0d-618">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="8db0d-619">Gli oggetti *singleton* sono gli stessi per ogni oggetto e per ogni richiesta, indipendentemente dal fatto che un' `Operation` istanza venga fornita in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8db0d-619">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="8db0d-620">Chiamare i servizi da main</span><span class="sxs-lookup"><span data-stu-id="8db0d-620">Call services from main</span></span>

<span data-ttu-id="8db0d-621">Creare un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>IServiceScope con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) per risolvere un servizio con ambito nell'ambito di applicazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="8db0d-621">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="8db0d-622">Questo approccio è utile per l'accesso a un servizio con ambito all'avvio e per l'esecuzione di attività di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="8db0d-622">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="8db0d-623">L'esempio seguente indica come ottenere un contesto per `MyScopedService` in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="8db0d-623">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="8db0d-624">Convalida dell'ambito</span><span class="sxs-lookup"><span data-stu-id="8db0d-624">Scope validation</span></span>

<span data-ttu-id="8db0d-625">Quando l'app viene eseguita nell'ambiente di sviluppo, il provider di servizi predefinito esegue controlli per verificare che:</span><span class="sxs-lookup"><span data-stu-id="8db0d-625">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="8db0d-626">I servizi con ambito non vengano risolti direttamente o indirettamente dal provider di servizi radice.</span><span class="sxs-lookup"><span data-stu-id="8db0d-626">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="8db0d-627">I servizi con ambito non vengano inseriti direttamente o indirettamente in singleton.</span><span class="sxs-lookup"><span data-stu-id="8db0d-627">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="8db0d-628">Il provider di servizi radice venga creato con la chiamata di <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="8db0d-628">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="8db0d-629">La durata del provider di servizi radice corrisponde alla durata dell'app/server quando il provider viene avviato con l'app e viene eliminato alla chiusura dell'app.</span><span class="sxs-lookup"><span data-stu-id="8db0d-629">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="8db0d-630">I servizi con ambito vengono eliminati dal contenitore che li ha creati.</span><span class="sxs-lookup"><span data-stu-id="8db0d-630">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="8db0d-631">Se un servizio con ambito viene creato nel contenitore radice, la durata del servizio viene di fatto convertita in singleton, perché il servizio viene eliminato solo dal contenitore radice alla chiusura dell'app/server.</span><span class="sxs-lookup"><span data-stu-id="8db0d-631">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="8db0d-632">La convalida degli ambiti servizio rileva queste situazioni nel contesto della chiamata di `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-632">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="8db0d-633">Per altre informazioni, vedere <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="8db0d-633">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="8db0d-634">Servizi di richiesta</span><span class="sxs-lookup"><span data-stu-id="8db0d-634">Request Services</span></span>

<span data-ttu-id="8db0d-635">I servizi disponibili all'interno di una richiesta ASP.NET Core da `HttpContext` vengono esposti tramite la raccolta [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="8db0d-635">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="8db0d-636">I servizi di richiesta rappresentano i servizi configurati e richiesti come parte dell'app.</span><span class="sxs-lookup"><span data-stu-id="8db0d-636">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="8db0d-637">Quando gli oggetti specificano dipendenze, le dipendenze sono soddisfatte dai tipi individuati in `RequestServices`, non in `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-637">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="8db0d-638">In generale, l'app non deve usare queste proprietà direttamente.</span><span class="sxs-lookup"><span data-stu-id="8db0d-638">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="8db0d-639">Richiedere invece i tipi richiesti dalle classi tramite i costruttori di classi e consentire al framework di inserire le dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8db0d-639">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="8db0d-640">Si ottengono classi più facili da testare.</span><span class="sxs-lookup"><span data-stu-id="8db0d-640">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="8db0d-641">È consigliabile richiedere le dipendenze come parametri del costruttore per l'accesso alla raccolta `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="8db0d-641">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="8db0d-642">Progettare i servizi per l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="8db0d-642">Design services for dependency injection</span></span>

<span data-ttu-id="8db0d-643">Le procedure consigliate prevedono di:</span><span class="sxs-lookup"><span data-stu-id="8db0d-643">Best practices are to:</span></span>

* <span data-ttu-id="8db0d-644">Progettare i servizi per usare l'inserimento delle dipendenze per ottenere le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8db0d-644">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="8db0d-645">Evitare membri e classi statiche con stato.</span><span class="sxs-lookup"><span data-stu-id="8db0d-645">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="8db0d-646">Progettare le app in modo da usare i servizi singleton, evitando così di creare uno stato globale.</span><span class="sxs-lookup"><span data-stu-id="8db0d-646">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="8db0d-647">Evitare la creazione diretta di istanze delle classi dipendenti all'interno di servizi.</span><span class="sxs-lookup"><span data-stu-id="8db0d-647">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="8db0d-648">La creazione diretta di istanze associa il codice a una determinata implementazione.</span><span class="sxs-lookup"><span data-stu-id="8db0d-648">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="8db0d-649">Fare in modo che le classi siano piccole, con factoring corretto e facili da testare.</span><span class="sxs-lookup"><span data-stu-id="8db0d-649">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="8db0d-650">Se una classe sembra avere troppe dipendenze inserite, ciò significa in genere che la classe ha un numero eccessivo di responsabilità e sta violando il [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (principio di responsabilità singola).</span><span class="sxs-lookup"><span data-stu-id="8db0d-650">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="8db0d-651">Tentare di effettuare il refactoring della classe spostando alcune delle responsabilità in una nuova classe.</span><span class="sxs-lookup"><span data-stu-id="8db0d-651">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="8db0d-652">Tenere presente che Razor le classi del modello della pagina delle pagine e le classi controller MVC devono concentrarsi sulle problematiche dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="8db0d-652">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="8db0d-653">Di conseguenza, le regole business e i dettagli di implementazione di accesso ai dati devono essere inseriti in classi appropriate per queste [problematiche separate](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="8db0d-653">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="8db0d-654">Eliminazione dei servizi</span><span class="sxs-lookup"><span data-stu-id="8db0d-654">Disposal of services</span></span>

<span data-ttu-id="8db0d-655">Il contenitore chiama <xref:System.IDisposable.Dispose*> per i tipi <xref:System.IDisposable> creati.</span><span class="sxs-lookup"><span data-stu-id="8db0d-655">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="8db0d-656">Se un'istanza viene aggiunta al contenitore dal codice utente, non viene eliminata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="8db0d-656">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="8db0d-657">Nell'esempio seguente i servizi vengono creati dal contenitore del servizio ed eliminati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="8db0d-657">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="8db0d-658">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="8db0d-658">In the following example:</span></span>

* <span data-ttu-id="8db0d-659">Le istanze del servizio non vengono create dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="8db0d-659">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="8db0d-660">Le durate di servizio previste non sono note dal Framework.</span><span class="sxs-lookup"><span data-stu-id="8db0d-660">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="8db0d-661">Il Framework non elimina automaticamente i servizi.</span><span class="sxs-lookup"><span data-stu-id="8db0d-661">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="8db0d-662">Se i servizi non vengono eliminati in modo esplicito nel codice dello sviluppatore, vengono mantenuti fino a quando l'app non viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="8db0d-662">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="8db0d-663">Linee guida IDisposable per istanze temporanee e condivise</span><span class="sxs-lookup"><span data-stu-id="8db0d-663">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="8db0d-664">Durata temporanea e limitata</span><span class="sxs-lookup"><span data-stu-id="8db0d-664">Transient, limited lifetime</span></span>

<span data-ttu-id="8db0d-665">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="8db0d-665">**Scenario**</span></span>

<span data-ttu-id="8db0d-666">L'app richiede un' <xref:System.IDisposable> istanza con una durata temporanea per uno degli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="8db0d-666">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="8db0d-667">L'istanza viene risolta nell'ambito radice.</span><span class="sxs-lookup"><span data-stu-id="8db0d-667">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="8db0d-668">L'istanza deve essere eliminata prima della fine dell'ambito.</span><span class="sxs-lookup"><span data-stu-id="8db0d-668">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="8db0d-669">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="8db0d-669">**Solution**</span></span>

<span data-ttu-id="8db0d-670">Usare il modello factory per creare un'istanza all'esterno dell'ambito padre.</span><span class="sxs-lookup"><span data-stu-id="8db0d-670">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="8db0d-671">In questa situazione, in genere l'app dispone di un `Create` metodo che chiama direttamente il costruttore del tipo finale.</span><span class="sxs-lookup"><span data-stu-id="8db0d-671">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="8db0d-672">Se il tipo finale ha altre dipendenze, la factory può:</span><span class="sxs-lookup"><span data-stu-id="8db0d-672">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="8db0d-673">Ricevere un oggetto <xref:System.IServiceProvider> nel relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="8db0d-673">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="8db0d-674">Usare <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> per creare un'istanza dell'istanza all'esterno del contenitore, usando il contenitore per le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8db0d-674">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="8db0d-675">Istanza condivisa, durata limitata</span><span class="sxs-lookup"><span data-stu-id="8db0d-675">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="8db0d-676">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="8db0d-676">**Scenario**</span></span>

<span data-ttu-id="8db0d-677">L'app richiede un' <xref:System.IDisposable> istanza condivisa tra più servizi, ma <xref:System.IDisposable> deve avere una durata limitata.</span><span class="sxs-lookup"><span data-stu-id="8db0d-677">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="8db0d-678">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="8db0d-678">**Solution**</span></span>

<span data-ttu-id="8db0d-679">Registrare l'istanza con una durata con ambito.</span><span class="sxs-lookup"><span data-stu-id="8db0d-679">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="8db0d-680">Usare <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> per avviare e creare un nuovo oggetto <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="8db0d-680">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="8db0d-681">Usare l'ambito <xref:System.IServiceProvider> per ottenere i servizi richiesti.</span><span class="sxs-lookup"><span data-stu-id="8db0d-681">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="8db0d-682">Eliminare l'ambito quando la durata deve terminare.</span><span class="sxs-lookup"><span data-stu-id="8db0d-682">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="8db0d-683">Linee guida generali</span><span class="sxs-lookup"><span data-stu-id="8db0d-683">General Guidelines</span></span>

* <span data-ttu-id="8db0d-684">Non registrare <xref:System.IDisposable> istanze con un ambito temporaneo.</span><span class="sxs-lookup"><span data-stu-id="8db0d-684">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="8db0d-685">Usare invece il modello Factory.</span><span class="sxs-lookup"><span data-stu-id="8db0d-685">Use the factory pattern instead.</span></span>
* <span data-ttu-id="8db0d-686">Non risolvere istanze temporanee o <xref:System.IDisposable> con ambito nell'ambito radice.</span><span class="sxs-lookup"><span data-stu-id="8db0d-686">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="8db0d-687">L'unica eccezione generale si verifica quando l'app crea/ricrea ed Elimina <xref:System.IServiceProvider> , che non è un modello ideale.</span><span class="sxs-lookup"><span data-stu-id="8db0d-687">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="8db0d-688">Per <xref:System.IDisposable> la ricezione di una dipendenza tramite di non è necessario che il ricevitore implementi <xref:System.IDisposable> se stesso.</span><span class="sxs-lookup"><span data-stu-id="8db0d-688">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="8db0d-689">Il ricevitore della <xref:System.IDisposable> dipendenza non deve chiamare <xref:System.IDisposable.Dispose%2A> su tale dipendenza.</span><span class="sxs-lookup"><span data-stu-id="8db0d-689">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="8db0d-690">Gli ambiti devono essere usati per controllare la durata dei servizi.</span><span class="sxs-lookup"><span data-stu-id="8db0d-690">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="8db0d-691">Gli ambiti non sono gerarchici e non esiste alcuna connessione speciale tra gli ambiti.</span><span class="sxs-lookup"><span data-stu-id="8db0d-691">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="8db0d-692">Sostituzione del contenitore di servizi predefinito</span><span class="sxs-lookup"><span data-stu-id="8db0d-692">Default service container replacement</span></span>

<span data-ttu-id="8db0d-693">Il contenitore dei servizi incorporato è progettato per soddisfare le esigenze del Framework e della maggior parte delle app consumer.</span><span class="sxs-lookup"><span data-stu-id="8db0d-693">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="8db0d-694">Si consiglia di usare il contenitore predefinito a meno che non sia necessaria una funzionalità specifica che il contenitore incorporato non supporta, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8db0d-694">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="8db0d-695">Inserimento di proprietà</span><span class="sxs-lookup"><span data-stu-id="8db0d-695">Property injection</span></span>
* <span data-ttu-id="8db0d-696">Inserimento in base al nome</span><span class="sxs-lookup"><span data-stu-id="8db0d-696">Injection based on name</span></span>
* <span data-ttu-id="8db0d-697">Contenitori figlio</span><span class="sxs-lookup"><span data-stu-id="8db0d-697">Child containers</span></span>
* <span data-ttu-id="8db0d-698">Gestione della durata personalizzata</span><span class="sxs-lookup"><span data-stu-id="8db0d-698">Custom lifetime management</span></span>
* <span data-ttu-id="8db0d-699">Supporto di `Func<T>` per l'inizializzazione differita</span><span class="sxs-lookup"><span data-stu-id="8db0d-699">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="8db0d-700">Registrazione basata sulle convenzioni</span><span class="sxs-lookup"><span data-stu-id="8db0d-700">Convention-based registration</span></span>

<span data-ttu-id="8db0d-701">Con ASP.NET Core app è possibile usare i contenitori di terze parti seguenti:</span><span class="sxs-lookup"><span data-stu-id="8db0d-701">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="8db0d-702">Autofac</span><span class="sxs-lookup"><span data-stu-id="8db0d-702">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="8db0d-703">DryIoc</span><span class="sxs-lookup"><span data-stu-id="8db0d-703">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="8db0d-704">Laura</span><span class="sxs-lookup"><span data-stu-id="8db0d-704">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="8db0d-705">LightInject</span><span class="sxs-lookup"><span data-stu-id="8db0d-705">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="8db0d-706">Lamar</span><span class="sxs-lookup"><span data-stu-id="8db0d-706">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="8db0d-707">Stashbox</span><span class="sxs-lookup"><span data-stu-id="8db0d-707">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="8db0d-708">Unity</span><span class="sxs-lookup"><span data-stu-id="8db0d-708">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="8db0d-709">Thread safety</span><span class="sxs-lookup"><span data-stu-id="8db0d-709">Thread safety</span></span>

<span data-ttu-id="8db0d-710">Creare servizi singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="8db0d-710">Create thread-safe singleton services.</span></span> <span data-ttu-id="8db0d-711">Se un servizio singleton ha una dipendenza in un servizio temporaneo, è necessario che anche il servizio temporaneo sia thread-safe, a seconda di come viene usato dal singleton.</span><span class="sxs-lookup"><span data-stu-id="8db0d-711">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="8db0d-712">Il metodo factory di un singolo servizio, ad esempio il secondo argomento di [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), non deve essere thread-safe.</span><span class="sxs-lookup"><span data-stu-id="8db0d-712">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="8db0d-713">Come nel caso di un costruttore di tipo (`static`), è garantito che venga chiamato una sola volta da un singolo thread.</span><span class="sxs-lookup"><span data-stu-id="8db0d-713">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="8db0d-714">Consigli</span><span class="sxs-lookup"><span data-stu-id="8db0d-714">Recommendations</span></span>

* <span data-ttu-id="8db0d-715">La risoluzione basata sui servizi `async/await` e `Task` non è supportata.</span><span class="sxs-lookup"><span data-stu-id="8db0d-715">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="8db0d-716">Dato che C# non supporta i costruttori asincroni, il modello consigliato consiste nell'usare i metodi asincroni dopo avere risolto in modo sincrono il servizio.</span><span class="sxs-lookup"><span data-stu-id="8db0d-716">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="8db0d-717">Evitare di archiviare i dati e la configurazione direttamente nel contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="8db0d-717">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="8db0d-718">Ad esempio, il carrello acquisti di un utente non dovrebbe in genere essere aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="8db0d-718">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="8db0d-719">La configurazione deve usare il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="8db0d-719">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="8db0d-720">Analogamente, evitare gli oggetti "contenitori di dati" che hanno la sola funzione di consentire l'accesso ad altri oggetti.</span><span class="sxs-lookup"><span data-stu-id="8db0d-720">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="8db0d-721">È preferibile richiedere l'elemento effettivo tramite inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8db0d-721">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="8db0d-722">Evitare l'accesso statico ai servizi (ad esempio, la tipizzazione statica [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) da usare in altre posizioni).</span><span class="sxs-lookup"><span data-stu-id="8db0d-722">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="8db0d-723">Evitare di usare il *modello del localizzatore di servizi*, che combina [inversione delle strategie di controllo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="8db0d-723">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="8db0d-724">Non richiamare <xref:System.IServiceProvider.GetService*> per ottenere un'istanza del servizio quando si può usare invece di:</span><span class="sxs-lookup"><span data-stu-id="8db0d-724">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="8db0d-725">**Errata**</span><span class="sxs-lookup"><span data-stu-id="8db0d-725">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
    {
        public void MyMethod()
        {
            var optionsMonitor = 
                _services.GetService<IOptionsMonitor<MyOptions>>();
            var option = optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

    <span data-ttu-id="8db0d-726">**Corretto**:</span><span class="sxs-lookup"><span data-stu-id="8db0d-726">**Correct**:</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

  * <span data-ttu-id="8db0d-727">Evitare di inserire una factory che risolve le dipendenze in fase di esecuzione usando <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="8db0d-727">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="8db0d-728">Evitare l'accesso statico a `HttpContext` (ad esempio [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="8db0d-728">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="8db0d-729">È tuttavia possibile che in alcuni casi queste raccomandazioni debbano essere ignorate.</span><span class="sxs-lookup"><span data-stu-id="8db0d-729">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="8db0d-730">Le eccezioni sono rari, soprattutto casi speciali all'interno del Framework stesso.</span><span class="sxs-lookup"><span data-stu-id="8db0d-730">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="8db0d-731">L'inserimento di dipendenze è un'*alternativa* ai modelli di accesso agli oggetti statici/globali.</span><span class="sxs-lookup"><span data-stu-id="8db0d-731">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="8db0d-732">Se l'inserimento di dipendenze viene usato con l'accesso agli oggetti statico i vantaggi non saranno evidenti.</span><span class="sxs-lookup"><span data-stu-id="8db0d-732">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8db0d-733">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8db0d-733">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="8db0d-734">Quattro modi per eliminare IDisposable in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8db0d-734">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="8db0d-735">Scrittura di codice pulito in ASP.NET Core con inserimento delle dipendenze (MSDN)</span><span class="sxs-lookup"><span data-stu-id="8db0d-735">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="8db0d-736">Explicit Dependencies Principle (Principio delle dipendenze esplicite)</span><span class="sxs-lookup"><span data-stu-id="8db0d-736">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="8db0d-737">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler) (Contenitori di inversione del controllo e modello di inserimento delle dipendenze)</span><span class="sxs-lookup"><span data-stu-id="8db0d-737">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="8db0d-738">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Come registrare un servizio con più interfacce in ASP.NET Core DI)</span><span class="sxs-lookup"><span data-stu-id="8db0d-738">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
