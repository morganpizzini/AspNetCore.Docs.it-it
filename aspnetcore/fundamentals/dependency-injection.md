---
<span data-ttu-id="6cd6f-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-102">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-103">'Identity'</span></span>
- <span data-ttu-id="6cd6f-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-105">'Razor'</span></span>
- <span data-ttu-id="6cd6f-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-106">'SignalR' uid:</span></span> 

---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="6cd6f-107">Inserimento delle dipendenze in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6cd6f-107">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="6cd6f-108">Di [Steve Smith](https://ardalis.com/) e [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="6cd6f-108">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6cd6f-109">ASP.NET Core supporta il modello progettuale software per l'inserimento delle dipendenze, ovvero una tecnica per ottenere l'[inversione del controllo (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra classi e relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-109">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="6cd6f-110">Per altre informazioni specifiche per l'inserimento delle dipendenze all'interno di controller MVC, vedere <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-110">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="6cd6f-111">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6cd6f-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="6cd6f-112">Panoramica dell'inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="6cd6f-112">Overview of dependency injection</span></span>

<span data-ttu-id="6cd6f-113">Una *dipendenza* è qualsiasi oggetto richiesto da un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-113">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="6cd6f-114">Esaminare la classe `MyDependency` seguente con un metodo `WriteMessage` da cui dipendono altre classi in un'app:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="6cd6f-115">È possibile creare un'istanza della classe `MyDependency` per rendere il metodo `WriteMessage`disponibile per una classe.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-115">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="6cd6f-116">La classe `MyDependency` è una dipendenza della classe `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-116">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="6cd6f-117">La classe crea e dipende direttamente dall'istanza `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-117">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="6cd6f-118">Le dipendenze del codice (come nell'esempio precedente) sono problematiche e devono essere evitate per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-118">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="6cd6f-119">Per sostituire `MyDependency` con un'implementazione diversa, la classe deve essere modificata.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-119">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="6cd6f-120">Se `MyDependency` ha dipendenze, devono essere configurate dalla classe.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-120">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="6cd6f-121">In un progetto di grandi dimensioni con più classi che dipendono da `MyDependency`, il codice di configurazione diventa sparso in tutta l'app.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="6cd6f-122">È difficile eseguire unit test di questa implementazione.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="6cd6f-123">L'app dovrebbe usare una classe `MyDependency` fittizia o stub, ma ciò non è possibile con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="6cd6f-124">L'inserimento delle dipendenze consente di risolvere questi problemi tramite:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="6cd6f-125">L'uso di un'interfaccia o di una classe di base per astrarre l'implementazione delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="6cd6f-126">La registrazione della dipendenza in un contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="6cd6f-127">ASP.NET Core offre il contenitore di servizi predefinito <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="6cd6f-128">I servizi vengono registrati nel metodo `Startup.ConfigureServices` dell'app.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-128">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="6cd6f-129">L'*inserimento* del servizio nel costruttore della classe in cui viene usato.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="6cd6f-130">Il framework si assume la responsabilità della creazione di un'istanza della dipendenza e della sua eliminazione quando non è più necessaria.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="6cd6f-131">Nell'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l'interfaccia `IMyDependency` definisce un metodo che il servizio fornisce all'app:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="6cd6f-132">Questa interfaccia viene implementata da un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="6cd6f-133">`MyDependency` richiede <xref:Microsoft.Extensions.Logging.ILogger`1> nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-133">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="6cd6f-134">Non è insolito usare l'inserimento delle dipendenze in modo concatenato.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-134">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="6cd6f-135">Ogni dipendenza richiesta richiede a sua volta le proprie dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-135">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="6cd6f-136">Il contenitore risolve le dipendenze nel grafico e restituisce il servizio completamente risolto.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-136">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="6cd6f-137">Il set di dipendenze che devono essere risolte viene generalmente chiamato *albero delle dipendenze* o *grafico dipendenze* o *grafico degli oggetti*.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-137">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="6cd6f-138">`IMyDependency` e `ILogger<TCategoryName>` devono essere registrati nel contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-138">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="6cd6f-139">`IMyDependency` viene registrato in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-139">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6cd6f-140">`ILogger<TCategoryName>`viene registrato dall'infrastruttura di registrazione delle astrazioni, pertanto si tratta di un [servizio fornito dal Framework](#framework-provided-services) registrato per impostazione predefinita dal Framework.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-140">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="6cd6f-141">Il contenitore risolve `ILogger<TCategoryName>` avvalendosi di [tipi aperti (generici)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando l'esigenza di registrare ogni singolo [tipo costruito (generico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="6cd6f-141">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="6cd6f-142">Nell'app di esempio, il servizio `IMyDependency` viene registrato con il tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-142">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="6cd6f-143">La registrazione definisce come ambito della durata di servizio la durata di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-143">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="6cd6f-144">Le [durate dei servizi](#service-lifetimes) sono descritte più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-144">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="6cd6f-145">Ogni metodo di estensione `services.Add{SERVICE_NAME}` aggiunge (e potenzialmente configura) i servizi.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-145">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="6cd6f-146">Ad esempio, `services.AddMvc()` aggiunge le pagine dei servizi Razor e MVC require.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-146">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="6cd6f-147">È consigliabile che le app seguano questa convenzione.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-147">We recommended that apps follow this convention.</span></span> <span data-ttu-id="6cd6f-148">Inserire i metodi di estensione nello spazio dei nomi [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) per incapsulare i gruppi di registrazioni di servizio.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-148">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="6cd6f-149">Se il costruttore del servizio richiede un [tipo incorporato](/dotnet/csharp/language-reference/keywords/built-in-types-table), ad esempio `string`, è possibile inserirlo usando la [configurazione](xref:fundamentals/configuration/index) o il [modello di opzioni](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="6cd6f-149">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="6cd6f-150">È richiesta un'istanza del servizio tramite il costruttore di una classe in cui il servizio viene usato e assegnato a un campo privato.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-150">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="6cd6f-151">Il campo viene usato per accedere al servizio in base alle esigenze per l'intera classe.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-151">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="6cd6f-152">Nell'app di esempio, viene richiesta l'istanza `IMyDependency` usata per chiamare il metodo `WriteMessage` del servizio:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-152">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="6cd6f-153">Servizi inseriti all'avvio</span><span class="sxs-lookup"><span data-stu-id="6cd6f-153">Services injected into Startup</span></span>

<span data-ttu-id="6cd6f-154">`Startup`Quando si utilizza l'host generico () è possibile inserire nel costruttore solo i tipi di servizio seguenti <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="6cd6f-154">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="6cd6f-155">I servizi possono essere inseriti in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="6cd6f-155">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="6cd6f-156">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-156">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="6cd6f-157">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="6cd6f-157">Framework-provided services</span></span>

<span data-ttu-id="6cd6f-158">Il `Startup.ConfigureServices` metodo è responsabile della definizione dei servizi utilizzati dall'app, incluse le funzionalità della piattaforma, ad esempio Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-158">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="6cd6f-159">Inizialmente, l'oggetto `IServiceCollection` fornito a `ConfigureServices` dispone di servizi definiti dal Framework a seconda della [configurazione dell'host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-159">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="6cd6f-160">Non è insolito che un'app basata su un modello di ASP.NET Core disponga di centinaia di servizi registrati dal Framework.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-160">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="6cd6f-161">Nella tabella seguente è elencato un piccolo esempio di servizi registrati dal Framework.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-161">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="6cd6f-162">Tipo di servizio</span><span class="sxs-lookup"><span data-stu-id="6cd6f-162">Service Type</span></span> | <span data-ttu-id="6cd6f-163">Durata</span><span class="sxs-lookup"><span data-stu-id="6cd6f-163">Lifetime</span></span> |
| ---
<span data-ttu-id="6cd6f-164">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-165">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-165">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-166">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-166">'Identity'</span></span>
- <span data-ttu-id="6cd6f-167">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-167">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-168">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-168">'Razor'</span></span>
- <span data-ttu-id="6cd6f-169">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-169">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-170">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-171">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-172">'Identity'</span></span>
- <span data-ttu-id="6cd6f-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-174">'Razor'</span></span>
- <span data-ttu-id="6cd6f-175">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-176">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-177">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-178">'Identity'</span></span>
- <span data-ttu-id="6cd6f-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-180">'Razor'</span></span>
- <span data-ttu-id="6cd6f-181">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-182">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-183">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-184">'Identity'</span></span>
- <span data-ttu-id="6cd6f-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-186">'Razor'</span></span>
- <span data-ttu-id="6cd6f-187">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-187">'SignalR' uid:</span></span> 

<span data-ttu-id="6cd6f-188">------ | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-188">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-189">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-190">'Identity'</span></span>
- <span data-ttu-id="6cd6f-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-192">'Razor'</span></span>
- <span data-ttu-id="6cd6f-193">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-194">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-195">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-196">'Identity'</span></span>
- <span data-ttu-id="6cd6f-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-198">'Razor'</span></span>
- <span data-ttu-id="6cd6f-199">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-199">'SignalR' uid:</span></span> 

<span data-ttu-id="6cd6f-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Temporaneo | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Temporaneo | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Temporaneo | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Temporaneo | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span><span class="sxs-lookup"><span data-stu-id="6cd6f-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="6cd6f-201">Registrare servizi aggiuntivi con i metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="6cd6f-201">Register additional services with extension methods</span></span>

<span data-ttu-id="6cd6f-202">Quando è disponibile un metodo di estensione della raccolta di servizi per la registrazione di un servizio (e dei servizi dipendenti, se necessario), la convenzione consiste nell'usare un singolo metodo di estensione `Add{SERVICE_NAME}` per registrare tutti i servizi richiesti da tale servizio.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-202">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="6cd6f-203">Il codice seguente è un esempio di come aggiungere servizi aggiuntivi al contenitore usando i metodi di estensione [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="6cd6f-203">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="6cd6f-204">Per altre informazioni, vedere la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> nella documentazione delle API.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-204">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="6cd6f-205">Durate dei servizi</span><span class="sxs-lookup"><span data-stu-id="6cd6f-205">Service lifetimes</span></span>

<span data-ttu-id="6cd6f-206">Scegliere una durata appropriata per ogni servizio registrato.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-206">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="6cd6f-207">I servizi ASP.NET Core possono essere configurati con le impostazioni di durata seguenti:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-207">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="6cd6f-208">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="6cd6f-208">Transient</span></span>

<span data-ttu-id="6cd6f-209">I servizi con durata temporanea (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) vengono creati ogni volta che vengono richiesti dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-209">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="6cd6f-210">Questa impostazione di durata è consigliata per i servizi semplici senza stati.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-210">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="6cd6f-211">Con ambito</span><span class="sxs-lookup"><span data-stu-id="6cd6f-211">Scoped</span></span>

<span data-ttu-id="6cd6f-212">I servizi con durata con ambito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) vengono creati una volta per ogni richiesta client (connessione).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-212">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="6cd6f-213">Quando si usa un servizio con ambito in un middleware, inserire il servizio nel metodo `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-213">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="6cd6f-214">Non inserire tramite il [Costruttore Injection](xref:mvc/controllers/dependency-injection#constructor-injection) perché impone al servizio di comportarsi come un singleton.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-214">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="6cd6f-215">Per altre informazioni, vedere <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-215">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="6cd6f-216">Singleton</span><span class="sxs-lookup"><span data-stu-id="6cd6f-216">Singleton</span></span>

<span data-ttu-id="6cd6f-217">I servizi con durata singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) vengono creati la prima volta che vengono richiesti (o quando si esegue `Startup.ConfigureServices` e viene specificata un'istanza con la registrazione del servizio).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-217">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="6cd6f-218">Tutte le richieste successive usano la stessa istanza.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-218">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="6cd6f-219">Se l'app richiede un comportamento singleton, è consigliabile consentire al contenitore del servizio di gestire la durata del servizio.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-219">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="6cd6f-220">Non implementare lo schema progettuale singleton e fornire codice utente per gestire la durata dell'oggetto nella classe.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-220">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="6cd6f-221">Può essere pericoloso risolvere un servizio con ambito da un singleton.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-221">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="6cd6f-222">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-222">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="6cd6f-223">Metodi di registrazione del servizio</span><span class="sxs-lookup"><span data-stu-id="6cd6f-223">Service registration methods</span></span>

<span data-ttu-id="6cd6f-224">I metodi di estensione per la registrazione del servizio offrono overload che risultano utili in scenari specifici.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-224">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="6cd6f-225">Metodo</span><span class="sxs-lookup"><span data-stu-id="6cd6f-225">Method</span></span> | <span data-ttu-id="6cd6f-226">Automatico</span><span class="sxs-lookup"><span data-stu-id="6cd6f-226">Automatic</span></span><br><span data-ttu-id="6cd6f-227">oggetto</span><span class="sxs-lookup"><span data-stu-id="6cd6f-227">object</span></span><br><span data-ttu-id="6cd6f-228">eliminazione</span><span class="sxs-lookup"><span data-stu-id="6cd6f-228">disposal</span></span> | <span data-ttu-id="6cd6f-229">Multipli</span><span class="sxs-lookup"><span data-stu-id="6cd6f-229">Multiple</span></span><br><span data-ttu-id="6cd6f-230">implementazioni</span><span class="sxs-lookup"><span data-stu-id="6cd6f-230">implementations</span></span> | <span data-ttu-id="6cd6f-231">Pass args</span><span class="sxs-lookup"><span data-stu-id="6cd6f-231">Pass args</span></span> |
| ---
<span data-ttu-id="6cd6f-232">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-232">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-233">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-234">'Identity'</span></span>
- <span data-ttu-id="6cd6f-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-236">'Razor'</span></span>
- <span data-ttu-id="6cd6f-237">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-237">'SignalR' uid:</span></span> 

<span data-ttu-id="6cd6f-238">--- | :---title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-238">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-239">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-240">'Identity'</span></span>
- <span data-ttu-id="6cd6f-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-242">'Razor'</span></span>
- <span data-ttu-id="6cd6f-243">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-244">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-245">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-246">'Identity'</span></span>
- <span data-ttu-id="6cd6f-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-248">'Razor'</span></span>
- <span data-ttu-id="6cd6f-249">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-250">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-251">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-252">'Identity'</span></span>
- <span data-ttu-id="6cd6f-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-254">'Razor'</span></span>
- <span data-ttu-id="6cd6f-255">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-256">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-257">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-258">'Identity'</span></span>
- <span data-ttu-id="6cd6f-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-260">'Razor'</span></span>
- <span data-ttu-id="6cd6f-261">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-262">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-263">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-264">'Identity'</span></span>
- <span data-ttu-id="6cd6f-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-266">'Razor'</span></span>
- <span data-ttu-id="6cd6f-267">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-268">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-269">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-270">'Identity'</span></span>
- <span data-ttu-id="6cd6f-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-272">'Razor'</span></span>
- <span data-ttu-id="6cd6f-273">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-274">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-275">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-276">'Identity'</span></span>
- <span data-ttu-id="6cd6f-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-278">'Razor'</span></span>
- <span data-ttu-id="6cd6f-279">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-280">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-281">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-282">'Identity'</span></span>
- <span data-ttu-id="6cd6f-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-284">'Razor'</span></span>
- <span data-ttu-id="6cd6f-285">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-286">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-287">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-288">'Identity'</span></span>
- <span data-ttu-id="6cd6f-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-290">'Razor'</span></span>
- <span data-ttu-id="6cd6f-291">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-292">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-293">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-294">'Identity'</span></span>
- <span data-ttu-id="6cd6f-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-296">'Razor'</span></span>
- <span data-ttu-id="6cd6f-297">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-298">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-299">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-300">'Identity'</span></span>
- <span data-ttu-id="6cd6f-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-302">'Razor'</span></span>
- <span data-ttu-id="6cd6f-303">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-304">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-305">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-306">'Identity'</span></span>
- <span data-ttu-id="6cd6f-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-308">'Razor'</span></span>
- <span data-ttu-id="6cd6f-309">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-309">'SignalR' uid:</span></span> 

<span data-ttu-id="6cd6f-310">---------------: | :---title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-310">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-311">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-312">'Identity'</span></span>
- <span data-ttu-id="6cd6f-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-314">'Razor'</span></span>
- <span data-ttu-id="6cd6f-315">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-316">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-317">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-318">'Identity'</span></span>
- <span data-ttu-id="6cd6f-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-320">'Razor'</span></span>
- <span data-ttu-id="6cd6f-321">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-322">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-323">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-324">'Identity'</span></span>
- <span data-ttu-id="6cd6f-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-326">'Razor'</span></span>
- <span data-ttu-id="6cd6f-327">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-327">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-328">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-329">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-329">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-330">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-330">'Identity'</span></span>
- <span data-ttu-id="6cd6f-331">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-331">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-332">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-332">'Razor'</span></span>
- <span data-ttu-id="6cd6f-333">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-333">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-334">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-335">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-335">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-336">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-336">'Identity'</span></span>
- <span data-ttu-id="6cd6f-337">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-337">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-338">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-338">'Razor'</span></span>
- <span data-ttu-id="6cd6f-339">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-339">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-340">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-341">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-341">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-342">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-342">'Identity'</span></span>
- <span data-ttu-id="6cd6f-343">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-343">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-344">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-344">'Razor'</span></span>
- <span data-ttu-id="6cd6f-345">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-345">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-346">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-347">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-348">'Identity'</span></span>
- <span data-ttu-id="6cd6f-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-350">'Razor'</span></span>
- <span data-ttu-id="6cd6f-351">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-352">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-353">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-354">'Identity'</span></span>
- <span data-ttu-id="6cd6f-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-356">'Razor'</span></span>
- <span data-ttu-id="6cd6f-357">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-357">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-358">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-359">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-360">'Identity'</span></span>
- <span data-ttu-id="6cd6f-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-362">'Razor'</span></span>
- <span data-ttu-id="6cd6f-363">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-364">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-365">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-366">'Identity'</span></span>
- <span data-ttu-id="6cd6f-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-368">'Razor'</span></span>
- <span data-ttu-id="6cd6f-369">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-369">'SignalR' uid:</span></span> 

<span data-ttu-id="6cd6f-370">-------------: | :---title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-370">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-371">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-372">'Identity'</span></span>
- <span data-ttu-id="6cd6f-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-374">'Razor'</span></span>
- <span data-ttu-id="6cd6f-375">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-375">'SignalR' uid:</span></span> 

<span data-ttu-id="6cd6f-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="6cd6f-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="6cd6f-377">Esempio:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-377">Example:</span></span><br><span data-ttu-id="6cd6f-378">`services.AddSingleton<IMyDep, MyDep>();`| Sì | Sì | No | |`Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="6cd6f-378">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="6cd6f-379">Esempi:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-379">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="6cd6f-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));`| Sì | Sì | Sì | |`Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="6cd6f-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="6cd6f-381">Esempio:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-381">Example:</span></span><br><span data-ttu-id="6cd6f-382">`services.AddSingleton<MyDep>();`| Sì | No | No | |`AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="6cd6f-382">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="6cd6f-383">Esempi:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-383">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="6cd6f-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));`| No | Sì | Sì | |`AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="6cd6f-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="6cd6f-385">Esempi:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-385">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="6cd6f-386">`services.AddSingleton(new MyDep("A string!"));`| No | No | Sì |</span><span class="sxs-lookup"><span data-stu-id="6cd6f-386">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="6cd6f-387">Per ulteriori informazioni sull'eliminazione dei tipi, vedere la sezione [Eliminazione dei servizi](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-387">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="6cd6f-388">Uno scenario comune per più implementazioni è costituito dai [tipi di simulazioni per i test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-388">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="6cd6f-389">I metodi `TryAdd{LIFETIME}` registrano il servizio solo se esiste già un'implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-389">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="6cd6f-390">Nell'esempio seguente, la prima riga registra `MyDependency` per `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-390">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="6cd6f-391">La seconda riga non ha alcun effetto perché `IMyDependency` dispone già di un'implementazione registrata:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-391">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="6cd6f-392">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-392">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="6cd6f-393">I metodi [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrano il servizio solo se non esiste già un'implementazione *dello stesso tipo*.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-393">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="6cd6f-394">Più servizi vengono risolti tramite `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-394">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="6cd6f-395">Durante la registrazione di servizi, lo sviluppatore desidera aggiungere un'istanza solo se non ne è già stata aggiunta una dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-395">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="6cd6f-396">In genere, questo metodo viene utilizzato dagli autori di librerie per evitare di registrare due copie di un'istanza nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-396">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="6cd6f-397">Nell'esempio seguente, la prima riga registra `MyDep` per `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-397">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="6cd6f-398">La seconda riga registra `MyDep` per `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-398">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="6cd6f-399">La seconda riga non ha alcun effetto perché `IMyDep1` dispone già di un'implementazione registrata di `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-399">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="6cd6f-400">Comportamento dell'inserimento del costruttore</span><span class="sxs-lookup"><span data-stu-id="6cd6f-400">Constructor injection behavior</span></span>

<span data-ttu-id="6cd6f-401">I servizi possono essere risolti con due meccanismi:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-401">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="6cd6f-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Consente la creazione di oggetti senza registrazione del servizio nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="6cd6f-403">`ActivatorUtilities` viene usato con astrazioni esposte all'utente, ad esempio helper tag, controller MVC e strumenti di associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-403">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="6cd6f-404">I costruttori possono accettare argomenti non forniti tramite l'inserimento di dipendenze, ma gli argomenti devono assegnare valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-404">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="6cd6f-405">Quando i servizi vengono risolti da `IServiceProvider` o `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede un costruttore *pubblico* .</span><span class="sxs-lookup"><span data-stu-id="6cd6f-405">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="6cd6f-406">Quando i servizi vengono risolti da `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede l'esistenza di un solo costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-406">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="6cd6f-407">Sebbene siano supportati gli overload dei costruttori, può essere presente un solo overload i cui argomenti possono essere tutti specificati tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-407">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="6cd6f-408">Contesti di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="6cd6f-408">Entity Framework contexts</span></span>

<span data-ttu-id="6cd6f-409">I contesti di Entity Framework vengono in genere aggiunti al contenitore di servizi usando la [durata con ambito](#service-lifetimes) perché le operazioni di database delle app Web hanno di solito come ambito la richiesta client.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-409">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="6cd6f-410">L'ambito della durata predefinita è se una durata non è specificata da un overload [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) durante la registrazione del contesto del database.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-410">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="6cd6f-411">I servizi con una durata specificata non devono usare un contesto di database con una durata più breve rispetto al servizio.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-411">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="6cd6f-412">Opzioni di durata e di registrazione</span><span class="sxs-lookup"><span data-stu-id="6cd6f-412">Lifetime and registration options</span></span>

<span data-ttu-id="6cd6f-413">Per illustrare la differenza tra le opzioni di durata e registrazione, si considerino le interfacce seguenti che rappresentano le attività come un'operazione con un identificatore univoco, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-413">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="6cd6f-414">A seconda del modo in cui la durata di un servizio operativo viene configurata per le interfacce seguenti, il contenitore fornisce la stessa istanza o un'istanza diversa del servizio quando richiesto da una classe:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-414">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="6cd6f-415">Le interfacce vengono implementate nella classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-415">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="6cd6f-416">Il costruttore `Operation` genera un GUID se non ne viene fornito uno:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-416">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="6cd6f-417">Viene registrato un `OperationService` che dipende da ognuno degli altri tipi `Operation`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-417">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="6cd6f-418">Quando viene richiesto `OperationService` tramite l'inserimento delle dipendenze, riceve una nuova istanza di ogni servizio o un'istanza esistente in base alla durata del servizio dipendente.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-418">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="6cd6f-419">Se i servizi temporanei vengono creati quando vengono richiesti dal contenitore, `OperationId` del servizio `IOperationTransient` è diverso da `OperationId` di `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-419">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="6cd6f-420">`OperationService` riceve una nuova istanza della classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-420">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="6cd6f-421">La nuova istanza restituisce un `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-421">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="6cd6f-422">Se i servizi con ambito vengono creati per ogni richiesta client, `OperationId` del servizio `IOperationScoped` corrisponde a `OperationService` all'interno di una richiesta client.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-422">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="6cd6f-423">In tutte le richieste client entrambi i servizi condividono un valore `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-423">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="6cd6f-424">Se i servizi singleton e con istanza singleton vengono creati una sola volta e usati per tutte le richieste client e tutti i servizi, `OperationId` rimane costante in tutte le richieste di servizio.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-424">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="6cd6f-425">In `Startup.ConfigureServices`, ogni tipo viene aggiunto al contenitore in base alla durata denominata:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-425">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="6cd6f-426">Il servizio `IOperationSingletonInstance` usa un'istanza specifica con un ID noto di `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-426">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="6cd6f-427">È chiaro quando questo tipo è in uso (il relativo GUID è composto da tutti zero).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-427">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="6cd6f-428">L'app di esempio dimostra le durate degli oggetti all'interno e tra le singole richieste.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-428">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="6cd6f-429">L'`IndexModel` dell'app di esempio richiede ogni genere di tipo `IOperation` e `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-429">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="6cd6f-430">La pagina visualizza quindi tutti i valori `OperationId` della classe del modello di pagina e del servizio tramite assegnazioni di proprietà:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-430">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="6cd6f-431">L'output seguente mostra i risultati delle due richieste:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-431">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="6cd6f-432">**Prima richiesta:**</span><span class="sxs-lookup"><span data-stu-id="6cd6f-432">**First request:**</span></span>

<span data-ttu-id="6cd6f-433">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-433">Controller operations:</span></span>

<span data-ttu-id="6cd6f-434">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="6cd6f-434">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="6cd6f-435">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="6cd6f-435">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="6cd6f-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="6cd6f-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="6cd6f-437">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="6cd6f-437">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="6cd6f-438">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-438">`OperationService` operations:</span></span>

<span data-ttu-id="6cd6f-439">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="6cd6f-439">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="6cd6f-440">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="6cd6f-440">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="6cd6f-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="6cd6f-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="6cd6f-442">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="6cd6f-442">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="6cd6f-443">**Seconda richiesta:**</span><span class="sxs-lookup"><span data-stu-id="6cd6f-443">**Second request:**</span></span>

<span data-ttu-id="6cd6f-444">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-444">Controller operations:</span></span>

<span data-ttu-id="6cd6f-445">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="6cd6f-445">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="6cd6f-446">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="6cd6f-446">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="6cd6f-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="6cd6f-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="6cd6f-448">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="6cd6f-448">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="6cd6f-449">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-449">`OperationService` operations:</span></span>

<span data-ttu-id="6cd6f-450">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="6cd6f-450">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="6cd6f-451">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="6cd6f-451">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="6cd6f-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="6cd6f-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="6cd6f-453">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="6cd6f-453">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="6cd6f-454">Osservare i valori `OperationId` che variano all'interno della richiesta e da una richiesta e l'altra:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-454">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="6cd6f-455">Gli oggetti *temporanei* sono sempre diversi.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-455">*Transient* objects are always different.</span></span> <span data-ttu-id="6cd6f-456">Il valore `OperationId` temporaneo per la prima e la seconda richiesta client è diverso per entrambe le operazioni `OperationService` e in tutte le richieste client.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-456">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="6cd6f-457">Viene fornita una nuova istanza per ogni richiesta di servizio e richiesta client.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-457">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="6cd6f-458">Gli oggetti *con ambito* sono gli stessi all'interno di una richiesta client, ma variano nelle diverse richieste client.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-458">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="6cd6f-459">Gli oggetti *singleton* sono gli stessi per ogni oggetto e per ogni richiesta, indipendentemente dal fatto che un' `Operation` istanza venga fornita in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6cd6f-459">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="6cd6f-460">Chiamare i servizi da main</span><span class="sxs-lookup"><span data-stu-id="6cd6f-460">Call services from main</span></span>

<span data-ttu-id="6cd6f-461">Creare un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>IServiceScope con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) per risolvere un servizio con ambito nell'ambito di applicazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-461">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="6cd6f-462">Questo approccio è utile per l'accesso a un servizio con ambito all'avvio e per l'esecuzione di attività di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-462">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="6cd6f-463">L'esempio seguente indica come ottenere un contesto per `MyScopedService` in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-463">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="6cd6f-464">Convalida dell'ambito</span><span class="sxs-lookup"><span data-stu-id="6cd6f-464">Scope validation</span></span>

<span data-ttu-id="6cd6f-465">Quando l'app è in esecuzione nell'ambiente di sviluppo e chiama [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) per compilare l'host, il provider di servizi predefinito esegue i controlli per verificare che:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-465">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="6cd6f-466">I servizi con ambito non vengano risolti direttamente o indirettamente dal provider di servizi radice.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-466">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="6cd6f-467">I servizi con ambito non vengano inseriti direttamente o indirettamente in singleton.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-467">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="6cd6f-468">Il provider di servizi radice venga creato con la chiamata di <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-468">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="6cd6f-469">La durata del provider di servizi radice corrisponde alla durata dell'app/server quando il provider viene avviato con l'app e viene eliminato alla chiusura dell'app.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-469">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="6cd6f-470">I servizi con ambito vengono eliminati dal contenitore che li ha creati.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-470">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="6cd6f-471">Se un servizio con ambito viene creato nel contenitore radice, la durata del servizio viene di fatto convertita in singleton, perché il servizio viene eliminato solo dal contenitore radice alla chiusura dell'app/server.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-471">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="6cd6f-472">La convalida degli ambiti servizio rileva queste situazioni nel contesto della chiamata di `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-472">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="6cd6f-473">Per altre informazioni, vedere <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-473">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="6cd6f-474">Servizi di richiesta</span><span class="sxs-lookup"><span data-stu-id="6cd6f-474">Request Services</span></span>

<span data-ttu-id="6cd6f-475">I servizi disponibili all'interno di una richiesta ASP.NET Core da `HttpContext` vengono esposti tramite la raccolta [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-475">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="6cd6f-476">I servizi di richiesta rappresentano i servizi configurati e richiesti come parte dell'app.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-476">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="6cd6f-477">Quando gli oggetti specificano dipendenze, le dipendenze sono soddisfatte dai tipi individuati in `RequestServices`, non in `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-477">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="6cd6f-478">In generale, l'app non deve usare queste proprietà direttamente.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-478">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="6cd6f-479">Richiedere invece i tipi richiesti dalle classi tramite costruttori di classe e consentire al Framework di inserire le dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-479">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="6cd6f-480">Si ottengono classi più facili da testare.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-480">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="6cd6f-481">È consigliabile richiedere le dipendenze come parametri del costruttore per l'accesso alla raccolta `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-481">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="6cd6f-482">Progettare i servizi per l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="6cd6f-482">Design services for dependency injection</span></span>

<span data-ttu-id="6cd6f-483">Le procedure consigliate prevedono di:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-483">Best practices are to:</span></span>

* <span data-ttu-id="6cd6f-484">Progettare i servizi per usare l'inserimento delle dipendenze per ottenere le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-484">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="6cd6f-485">Evitare membri e classi statiche con stato.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-485">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="6cd6f-486">Progettare le app in modo da usare i servizi singleton, evitando così di creare uno stato globale.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-486">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="6cd6f-487">Evitare la creazione diretta di istanze delle classi dipendenti all'interno di servizi.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-487">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="6cd6f-488">La creazione diretta di istanze associa il codice a una determinata implementazione.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-488">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="6cd6f-489">Fare in modo che le classi siano piccole, con factoring corretto e facili da testare.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-489">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="6cd6f-490">Se una classe sembra avere troppe dipendenze inserite, ciò significa in genere che la classe ha un numero eccessivo di responsabilità e sta violando il [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (principio di responsabilità singola).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-490">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="6cd6f-491">Tentare di effettuare il refactoring della classe spostando alcune delle responsabilità in una nuova classe.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-491">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="6cd6f-492">Tenere presente che Razor le classi del modello della pagina delle pagine e le classi controller MVC devono concentrarsi sulle problematiche dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-492">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="6cd6f-493">Di conseguenza, le regole business e i dettagli di implementazione di accesso ai dati devono essere inseriti in classi appropriate per queste [problematiche separate](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-493">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="6cd6f-494">Eliminazione dei servizi</span><span class="sxs-lookup"><span data-stu-id="6cd6f-494">Disposal of services</span></span>

<span data-ttu-id="6cd6f-495">Il contenitore chiama <xref:System.IDisposable.Dispose*> per i tipi <xref:System.IDisposable> creati.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-495">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="6cd6f-496">Se un'istanza viene aggiunta al contenitore dal codice utente, non viene eliminata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-496">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="6cd6f-497">Nell'esempio seguente i servizi vengono creati dal contenitore del servizio ed eliminati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-497">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="6cd6f-498">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-498">In the following example:</span></span>

* <span data-ttu-id="6cd6f-499">Le istanze del servizio non vengono create dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-499">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="6cd6f-500">Le durate di servizio previste non sono note dal Framework.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-500">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="6cd6f-501">Il Framework non elimina automaticamente i servizi.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-501">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="6cd6f-502">Se i servizi non vengono eliminati in modo esplicito nel codice dello sviluppatore, vengono mantenuti fino a quando l'app non viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-502">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="6cd6f-503">Per informazioni sulle opzioni di eliminazione del servizio, vedere [quattro modi per eliminare IDisposable in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-503">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="6cd6f-504">Sostituzione del contenitore di servizi predefinito</span><span class="sxs-lookup"><span data-stu-id="6cd6f-504">Default service container replacement</span></span>

<span data-ttu-id="6cd6f-505">Il contenitore dei servizi incorporato è progettato per soddisfare le esigenze del Framework e della maggior parte delle app consumer.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-505">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="6cd6f-506">Si consiglia di usare il contenitore predefinito a meno che non sia necessaria una funzionalità specifica che il contenitore incorporato non supporta, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-506">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="6cd6f-507">Inserimento di proprietà</span><span class="sxs-lookup"><span data-stu-id="6cd6f-507">Property injection</span></span>
* <span data-ttu-id="6cd6f-508">Inserimento in base al nome</span><span class="sxs-lookup"><span data-stu-id="6cd6f-508">Injection based on name</span></span>
* <span data-ttu-id="6cd6f-509">Contenitori figlio</span><span class="sxs-lookup"><span data-stu-id="6cd6f-509">Child containers</span></span>
* <span data-ttu-id="6cd6f-510">Gestione della durata personalizzata</span><span class="sxs-lookup"><span data-stu-id="6cd6f-510">Custom lifetime management</span></span>
* <span data-ttu-id="6cd6f-511">Supporto di `Func<T>` per l'inizializzazione differita</span><span class="sxs-lookup"><span data-stu-id="6cd6f-511">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="6cd6f-512">Registrazione basata sulle convenzioni</span><span class="sxs-lookup"><span data-stu-id="6cd6f-512">Convention-based registration</span></span>

<span data-ttu-id="6cd6f-513">Con ASP.NET Core app è possibile usare i contenitori di terze parti seguenti:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-513">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="6cd6f-514">Autofac</span><span class="sxs-lookup"><span data-stu-id="6cd6f-514">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="6cd6f-515">DryIoc</span><span class="sxs-lookup"><span data-stu-id="6cd6f-515">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="6cd6f-516">Laura</span><span class="sxs-lookup"><span data-stu-id="6cd6f-516">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="6cd6f-517">LightInject</span><span class="sxs-lookup"><span data-stu-id="6cd6f-517">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="6cd6f-518">Lamar</span><span class="sxs-lookup"><span data-stu-id="6cd6f-518">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="6cd6f-519">Stashbox</span><span class="sxs-lookup"><span data-stu-id="6cd6f-519">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="6cd6f-520">Unity</span><span class="sxs-lookup"><span data-stu-id="6cd6f-520">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="6cd6f-521">Thread safety</span><span class="sxs-lookup"><span data-stu-id="6cd6f-521">Thread safety</span></span>

<span data-ttu-id="6cd6f-522">Creare servizi singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-522">Create thread-safe singleton services.</span></span> <span data-ttu-id="6cd6f-523">Se un servizio singleton ha una dipendenza in un servizio temporaneo, è necessario che anche il servizio temporaneo sia thread-safe, a seconda di come viene usato dal singleton.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-523">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="6cd6f-524">Il metodo factory di un singolo servizio, ad esempio il secondo argomento di [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), non deve essere thread-safe.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-524">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="6cd6f-525">Come nel caso di un costruttore di tipo (`static`), è garantito che venga chiamato una sola volta da un singolo thread.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-525">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="6cd6f-526">Raccomandazioni</span><span class="sxs-lookup"><span data-stu-id="6cd6f-526">Recommendations</span></span>

* <span data-ttu-id="6cd6f-527">La risoluzione basata sui servizi `async/await` e `Task` non è supportata.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-527">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="6cd6f-528">Dato che C# non supporta i costruttori asincroni, il modello consigliato consiste nell'usare i metodi asincroni dopo avere risolto in modo sincrono il servizio.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-528">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="6cd6f-529">Evitare di archiviare i dati e la configurazione direttamente nel contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-529">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="6cd6f-530">Ad esempio, il carrello acquisti di un utente non dovrebbe in genere essere aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-530">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="6cd6f-531">La configurazione deve usare il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-531">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="6cd6f-532">Analogamente, evitare gli oggetti "contenitori di dati" che hanno la sola funzione di consentire l'accesso ad altri oggetti.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-532">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="6cd6f-533">È preferibile richiedere l'elemento effettivo tramite inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-533">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="6cd6f-534">Evitare l'accesso statico ai servizi (ad esempio, la tipizzazione statica [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) da usare in altre posizioni).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-534">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="6cd6f-535">Evitare di usare il *modello di localizzatore del servizio*.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-535">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="6cd6f-536">Ad esempio, non richiamare <xref:System.IServiceProvider.GetService*> per ottenere un'istanza del servizio quando è invece possibile usare l'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-536">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="6cd6f-537">**Errata**</span><span class="sxs-lookup"><span data-stu-id="6cd6f-537">**Incorrect:**</span></span>

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

  <span data-ttu-id="6cd6f-538">**Corretto**:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-538">**Correct**:</span></span>

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

* <span data-ttu-id="6cd6f-539">Un'altra variazione del localizzatore del servizio da evitare è inserire una factory che risolve le dipendenze in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-539">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="6cd6f-540">Queste procedure combinano le strategie di [inversione del controllo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-540">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="6cd6f-541">Evitare l'accesso statico a `HttpContext` (ad esempio [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-541">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="6cd6f-542">È tuttavia possibile che in alcuni casi queste raccomandazioni debbano essere ignorate.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-542">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="6cd6f-543">Le eccezioni sono rare e principalmente si tratta di casi speciali all'interno del framework stesso.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-543">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="6cd6f-544">L'inserimento di dipendenze è un'*alternativa* ai modelli di accesso agli oggetti statici/globali.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-544">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="6cd6f-545">Se l'inserimento di dipendenze viene usato con l'accesso agli oggetti statico i vantaggi non saranno evidenti.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-545">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="6cd6f-546">Modelli consigliati per il multi-tenant in DI</span><span class="sxs-lookup"><span data-stu-id="6cd6f-546">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="6cd6f-547">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) fornisce il multi-tenant.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-547">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="6cd6f-548">Per ulteriori informazioni, vedere la [documentazione di Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-548">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="6cd6f-549">Vedere le app di esempio in https://github.com/OrchardCMS/OrchardCore.Samples per esempi di come creare app modulari e multi-tenant usando solo il Framework Orchard core senza le funzionalità specifiche del CMS.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-549">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6cd6f-550">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6cd6f-550">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="6cd6f-551">Scrittura di codice pulito in ASP.NET Core con inserimento delle dipendenze (MSDN)</span><span class="sxs-lookup"><span data-stu-id="6cd6f-551">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="6cd6f-552">Explicit Dependencies Principle (Principio delle dipendenze esplicite)</span><span class="sxs-lookup"><span data-stu-id="6cd6f-552">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="6cd6f-553">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler) (Contenitori di inversione del controllo e modello di inserimento delle dipendenze)</span><span class="sxs-lookup"><span data-stu-id="6cd6f-553">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="6cd6f-554">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Come registrare un servizio con più interfacce in ASP.NET Core DI)</span><span class="sxs-lookup"><span data-stu-id="6cd6f-554">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6cd6f-555">ASP.NET Core supporta il modello progettuale software per l'inserimento delle dipendenze, ovvero una tecnica per ottenere l'[inversione del controllo (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra classi e relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-555">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="6cd6f-556">Per altre informazioni specifiche per l'inserimento delle dipendenze all'interno di controller MVC, vedere <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-556">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="6cd6f-557">[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6cd6f-557">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="6cd6f-558">Panoramica dell'inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="6cd6f-558">Overview of dependency injection</span></span>

<span data-ttu-id="6cd6f-559">Una *dipendenza* è qualsiasi oggetto richiesto da un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-559">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="6cd6f-560">Esaminare la classe `MyDependency` seguente con un metodo `WriteMessage` da cui dipendono altre classi in un'app:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-560">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="6cd6f-561">È possibile creare un'istanza della classe `MyDependency` per rendere il metodo `WriteMessage`disponibile per una classe.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-561">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="6cd6f-562">La classe `MyDependency` è una dipendenza della classe `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-562">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="6cd6f-563">La classe crea e dipende direttamente dall'istanza `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-563">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="6cd6f-564">Le dipendenze del codice (come nell'esempio precedente) sono problematiche e devono essere evitate per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-564">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="6cd6f-565">Per sostituire `MyDependency` con un'implementazione diversa, la classe deve essere modificata.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-565">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="6cd6f-566">Se `MyDependency` ha dipendenze, devono essere configurate dalla classe.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-566">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="6cd6f-567">In un progetto di grandi dimensioni con più classi che dipendono da `MyDependency`, il codice di configurazione diventa sparso in tutta l'app.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-567">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="6cd6f-568">È difficile eseguire unit test di questa implementazione.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-568">This implementation is difficult to unit test.</span></span> <span data-ttu-id="6cd6f-569">L'app dovrebbe usare una classe `MyDependency` fittizia o stub, ma ciò non è possibile con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-569">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="6cd6f-570">L'inserimento delle dipendenze consente di risolvere questi problemi tramite:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-570">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="6cd6f-571">L'uso di un'interfaccia o di una classe di base per astrarre l'implementazione delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-571">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="6cd6f-572">La registrazione della dipendenza in un contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-572">Registration of the dependency in a service container.</span></span> <span data-ttu-id="6cd6f-573">ASP.NET Core offre il contenitore di servizi predefinito <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-573">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="6cd6f-574">I servizi vengono registrati nel metodo `Startup.ConfigureServices` dell'app.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-574">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="6cd6f-575">L'*inserimento* del servizio nel costruttore della classe in cui viene usato.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-575">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="6cd6f-576">Il framework si assume la responsabilità della creazione di un'istanza della dipendenza e della sua eliminazione quando non è più necessaria.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-576">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="6cd6f-577">Nell'[app di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l'interfaccia `IMyDependency` definisce un metodo che il servizio fornisce all'app:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-577">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="6cd6f-578">Questa interfaccia viene implementata da un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-578">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="6cd6f-579">`MyDependency` richiede <xref:Microsoft.Extensions.Logging.ILogger`1> nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-579">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="6cd6f-580">Non è insolito usare l'inserimento delle dipendenze in modo concatenato.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-580">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="6cd6f-581">Ogni dipendenza richiesta richiede a sua volta le proprie dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-581">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="6cd6f-582">Il contenitore risolve le dipendenze nel grafico e restituisce il servizio completamente risolto.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-582">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="6cd6f-583">Il set di dipendenze che devono essere risolte viene generalmente chiamato *albero delle dipendenze* o *grafico dipendenze* o *grafico degli oggetti*.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-583">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="6cd6f-584">`IMyDependency` e `ILogger<TCategoryName>` devono essere registrati nel contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-584">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="6cd6f-585">`IMyDependency` viene registrato in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-585">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6cd6f-586">`ILogger<TCategoryName>`viene registrato dall'infrastruttura di registrazione delle astrazioni, pertanto si tratta di un [servizio fornito dal Framework](#framework-provided-services) registrato per impostazione predefinita dal Framework.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-586">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="6cd6f-587">Il contenitore risolve `ILogger<TCategoryName>` avvalendosi di [tipi aperti (generici)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando l'esigenza di registrare ogni singolo [tipo costruito (generico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="6cd6f-587">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="6cd6f-588">Nell'app di esempio, il servizio `IMyDependency` viene registrato con il tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-588">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="6cd6f-589">La registrazione definisce come ambito della durata di servizio la durata di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-589">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="6cd6f-590">Le [durate dei servizi](#service-lifetimes) sono descritte più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-590">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="6cd6f-591">Ogni metodo di estensione `services.Add{SERVICE_NAME}` aggiunge (e potenzialmente configura) i servizi.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-591">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="6cd6f-592">Ad esempio, `services.AddMvc()` aggiunge le pagine dei servizi Razor e MVC require.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-592">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="6cd6f-593">È consigliabile che le app seguano questa convenzione.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-593">We recommended that apps follow this convention.</span></span> <span data-ttu-id="6cd6f-594">Inserire i metodi di estensione nello spazio dei nomi [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) per incapsulare i gruppi di registrazioni di servizio.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-594">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="6cd6f-595">Se il costruttore del servizio richiede un [tipo incorporato](/dotnet/csharp/language-reference/keywords/built-in-types-table), ad esempio `string`, è possibile inserirlo usando la [configurazione](xref:fundamentals/configuration/index) o il [modello di opzioni](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="6cd6f-595">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="6cd6f-596">È richiesta un'istanza del servizio tramite il costruttore di una classe in cui il servizio viene usato e assegnato a un campo privato.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-596">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="6cd6f-597">Il campo viene usato per accedere al servizio in base alle esigenze per l'intera classe.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-597">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="6cd6f-598">Nell'app di esempio, viene richiesta l'istanza `IMyDependency` usata per chiamare il metodo `WriteMessage` del servizio:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-598">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="6cd6f-599">Servizi inseriti all'avvio</span><span class="sxs-lookup"><span data-stu-id="6cd6f-599">Services injected into Startup</span></span>

<span data-ttu-id="6cd6f-600">`Startup`Quando si utilizza l'host generico () è possibile inserire nel costruttore solo i tipi di servizio seguenti <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="6cd6f-600">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="6cd6f-601">I servizi possono essere inseriti in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="6cd6f-601">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="6cd6f-602">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-602">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="6cd6f-603">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="6cd6f-603">Framework-provided services</span></span>

<span data-ttu-id="6cd6f-604">Il `Startup.ConfigureServices` metodo è responsabile della definizione dei servizi utilizzati dall'app, incluse le funzionalità della piattaforma, ad esempio Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-604">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="6cd6f-605">Inizialmente, l'oggetto `IServiceCollection` fornito a `ConfigureServices` dispone di servizi definiti dal Framework a seconda della [configurazione dell'host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-605">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="6cd6f-606">Non è insolito che un'app basata su un modello di ASP.NET Core disponga di centinaia di servizi registrati dal Framework.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-606">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="6cd6f-607">Nella tabella seguente è elencato un piccolo esempio di servizi registrati dal Framework.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-607">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="6cd6f-608">Tipo di servizio</span><span class="sxs-lookup"><span data-stu-id="6cd6f-608">Service Type</span></span> | <span data-ttu-id="6cd6f-609">Durata</span><span class="sxs-lookup"><span data-stu-id="6cd6f-609">Lifetime</span></span> |
| ---
<span data-ttu-id="6cd6f-610">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-611">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-611">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-612">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-612">'Identity'</span></span>
- <span data-ttu-id="6cd6f-613">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-613">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-614">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-614">'Razor'</span></span>
- <span data-ttu-id="6cd6f-615">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-615">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-616">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-616">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-617">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-617">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-618">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-618">'Identity'</span></span>
- <span data-ttu-id="6cd6f-619">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-619">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-620">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-620">'Razor'</span></span>
- <span data-ttu-id="6cd6f-621">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-621">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-622">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-623">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-623">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-624">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-624">'Identity'</span></span>
- <span data-ttu-id="6cd6f-625">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-625">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-626">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-626">'Razor'</span></span>
- <span data-ttu-id="6cd6f-627">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-627">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-628">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-629">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-630">'Identity'</span></span>
- <span data-ttu-id="6cd6f-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-632">'Razor'</span></span>
- <span data-ttu-id="6cd6f-633">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-633">'SignalR' uid:</span></span> 

<span data-ttu-id="6cd6f-634">------ | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-634">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-635">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-636">'Identity'</span></span>
- <span data-ttu-id="6cd6f-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-638">'Razor'</span></span>
- <span data-ttu-id="6cd6f-639">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-639">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-640">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-641">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-642">'Identity'</span></span>
- <span data-ttu-id="6cd6f-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-644">'Razor'</span></span>
- <span data-ttu-id="6cd6f-645">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-645">'SignalR' uid:</span></span> 

<span data-ttu-id="6cd6f-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Temporaneo | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Temporaneo | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Temporaneo | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Temporaneo | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span><span class="sxs-lookup"><span data-stu-id="6cd6f-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="6cd6f-647">Registrare servizi aggiuntivi con i metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="6cd6f-647">Register additional services with extension methods</span></span>

<span data-ttu-id="6cd6f-648">Quando è disponibile un metodo di estensione della raccolta di servizi per la registrazione di un servizio (e dei servizi dipendenti, se necessario), la convenzione consiste nell'usare un singolo metodo di estensione `Add{SERVICE_NAME}` per registrare tutti i servizi richiesti da tale servizio.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-648">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="6cd6f-649">Il codice seguente è un esempio di come aggiungere servizi aggiuntivi al contenitore usando i metodi di estensione [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="6cd6f-649">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="6cd6f-650">Per altre informazioni, vedere la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> nella documentazione delle API.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-650">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="6cd6f-651">Durate dei servizi</span><span class="sxs-lookup"><span data-stu-id="6cd6f-651">Service lifetimes</span></span>

<span data-ttu-id="6cd6f-652">Scegliere una durata appropriata per ogni servizio registrato.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-652">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="6cd6f-653">I servizi ASP.NET Core possono essere configurati con le impostazioni di durata seguenti:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-653">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="6cd6f-654">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="6cd6f-654">Transient</span></span>

<span data-ttu-id="6cd6f-655">I servizi con durata temporanea (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) vengono creati ogni volta che vengono richiesti dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-655">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="6cd6f-656">Questa impostazione di durata è consigliata per i servizi semplici senza stati.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-656">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="6cd6f-657">Con ambito</span><span class="sxs-lookup"><span data-stu-id="6cd6f-657">Scoped</span></span>

<span data-ttu-id="6cd6f-658">I servizi con durata con ambito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) vengono creati una volta per ogni richiesta client (connessione).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-658">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="6cd6f-659">Quando si usa un servizio con ambito in un middleware, inserire il servizio nel metodo `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-659">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="6cd6f-660">Non inserire tramite il [Costruttore Injection](xref:mvc/controllers/dependency-injection#constructor-injection) perché impone al servizio di comportarsi come un singleton.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-660">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="6cd6f-661">Per altre informazioni, vedere <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-661">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="6cd6f-662">Singleton</span><span class="sxs-lookup"><span data-stu-id="6cd6f-662">Singleton</span></span>

<span data-ttu-id="6cd6f-663">I servizi con durata singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) vengono creati la prima volta che vengono richiesti (o quando si esegue `Startup.ConfigureServices` e viene specificata un'istanza con la registrazione del servizio).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-663">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="6cd6f-664">Tutte le richieste successive usano la stessa istanza.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-664">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="6cd6f-665">Se l'app richiede un comportamento singleton, è consigliabile consentire al contenitore del servizio di gestire la durata del servizio.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-665">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="6cd6f-666">Non implementare lo schema progettuale singleton e fornire codice utente per gestire la durata dell'oggetto nella classe.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-666">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="6cd6f-667">Può essere pericoloso risolvere un servizio con ambito da un singleton.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-667">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="6cd6f-668">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-668">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="6cd6f-669">Metodi di registrazione del servizio</span><span class="sxs-lookup"><span data-stu-id="6cd6f-669">Service registration methods</span></span>

<span data-ttu-id="6cd6f-670">I metodi di estensione per la registrazione del servizio offrono overload che risultano utili in scenari specifici.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-670">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="6cd6f-671">Metodo</span><span class="sxs-lookup"><span data-stu-id="6cd6f-671">Method</span></span> | <span data-ttu-id="6cd6f-672">Automatico</span><span class="sxs-lookup"><span data-stu-id="6cd6f-672">Automatic</span></span><br><span data-ttu-id="6cd6f-673">oggetto</span><span class="sxs-lookup"><span data-stu-id="6cd6f-673">object</span></span><br><span data-ttu-id="6cd6f-674">eliminazione</span><span class="sxs-lookup"><span data-stu-id="6cd6f-674">disposal</span></span> | <span data-ttu-id="6cd6f-675">Multipli</span><span class="sxs-lookup"><span data-stu-id="6cd6f-675">Multiple</span></span><br><span data-ttu-id="6cd6f-676">implementazioni</span><span class="sxs-lookup"><span data-stu-id="6cd6f-676">implementations</span></span> | <span data-ttu-id="6cd6f-677">Pass args</span><span class="sxs-lookup"><span data-stu-id="6cd6f-677">Pass args</span></span> |
| ---
<span data-ttu-id="6cd6f-678">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-678">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-679">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-679">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-680">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-680">'Identity'</span></span>
- <span data-ttu-id="6cd6f-681">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-681">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-682">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-682">'Razor'</span></span>
- <span data-ttu-id="6cd6f-683">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-683">'SignalR' uid:</span></span> 

<span data-ttu-id="6cd6f-684">--- | :---title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-684">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-685">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-685">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-686">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-686">'Identity'</span></span>
- <span data-ttu-id="6cd6f-687">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-687">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-688">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-688">'Razor'</span></span>
- <span data-ttu-id="6cd6f-689">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-689">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-690">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-690">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-691">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-691">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-692">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-692">'Identity'</span></span>
- <span data-ttu-id="6cd6f-693">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-693">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-694">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-694">'Razor'</span></span>
- <span data-ttu-id="6cd6f-695">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-695">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-696">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-696">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-697">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-697">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-698">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-698">'Identity'</span></span>
- <span data-ttu-id="6cd6f-699">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-699">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-700">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-700">'Razor'</span></span>
- <span data-ttu-id="6cd6f-701">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-701">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-702">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-702">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-703">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-703">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-704">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-704">'Identity'</span></span>
- <span data-ttu-id="6cd6f-705">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-705">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-706">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-706">'Razor'</span></span>
- <span data-ttu-id="6cd6f-707">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-707">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-708">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-708">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-709">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-709">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-710">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-710">'Identity'</span></span>
- <span data-ttu-id="6cd6f-711">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-711">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-712">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-712">'Razor'</span></span>
- <span data-ttu-id="6cd6f-713">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-713">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-714">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-714">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-715">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-715">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-716">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-716">'Identity'</span></span>
- <span data-ttu-id="6cd6f-717">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-717">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-718">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-718">'Razor'</span></span>
- <span data-ttu-id="6cd6f-719">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-719">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-720">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-720">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-721">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-721">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-722">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-722">'Identity'</span></span>
- <span data-ttu-id="6cd6f-723">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-723">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-724">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-724">'Razor'</span></span>
- <span data-ttu-id="6cd6f-725">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-725">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-726">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-726">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-727">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-727">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-728">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-728">'Identity'</span></span>
- <span data-ttu-id="6cd6f-729">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-729">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-730">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-730">'Razor'</span></span>
- <span data-ttu-id="6cd6f-731">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-731">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-732">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-732">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-733">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-734">'Identity'</span></span>
- <span data-ttu-id="6cd6f-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-736">'Razor'</span></span>
- <span data-ttu-id="6cd6f-737">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-737">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-738">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-739">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-740">'Identity'</span></span>
- <span data-ttu-id="6cd6f-741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-741">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-742">'Razor'</span></span>
- <span data-ttu-id="6cd6f-743">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-744">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-745">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-746">'Identity'</span></span>
- <span data-ttu-id="6cd6f-747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-747">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-748">'Razor'</span></span>
- <span data-ttu-id="6cd6f-749">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-750">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-751">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-752">'Identity'</span></span>
- <span data-ttu-id="6cd6f-753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-753">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-754">'Razor'</span></span>
- <span data-ttu-id="6cd6f-755">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-755">'SignalR' uid:</span></span> 

<span data-ttu-id="6cd6f-756">---------------: | :---title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-756">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-757">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-758">'Identity'</span></span>
- <span data-ttu-id="6cd6f-759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-759">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-760">'Razor'</span></span>
- <span data-ttu-id="6cd6f-761">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-762">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-763">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-764">'Identity'</span></span>
- <span data-ttu-id="6cd6f-765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-765">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-766">'Razor'</span></span>
- <span data-ttu-id="6cd6f-767">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-768">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-769">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-770">'Identity'</span></span>
- <span data-ttu-id="6cd6f-771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-771">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-772">'Razor'</span></span>
- <span data-ttu-id="6cd6f-773">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-774">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-775">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-776">'Identity'</span></span>
- <span data-ttu-id="6cd6f-777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-777">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-778">'Razor'</span></span>
- <span data-ttu-id="6cd6f-779">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-780">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-781">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-782">'Identity'</span></span>
- <span data-ttu-id="6cd6f-783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-783">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-784">'Razor'</span></span>
- <span data-ttu-id="6cd6f-785">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-786">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-787">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-788">'Identity'</span></span>
- <span data-ttu-id="6cd6f-789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-789">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-790">'Razor'</span></span>
- <span data-ttu-id="6cd6f-791">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-792">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-793">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-794">'Identity'</span></span>
- <span data-ttu-id="6cd6f-795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-795">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-796">'Razor'</span></span>
- <span data-ttu-id="6cd6f-797">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-798">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-799">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-800">'Identity'</span></span>
- <span data-ttu-id="6cd6f-801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-801">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-802">'Razor'</span></span>
- <span data-ttu-id="6cd6f-803">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-804">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-805">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-806">'Identity'</span></span>
- <span data-ttu-id="6cd6f-807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-807">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-808">'Razor'</span></span>
- <span data-ttu-id="6cd6f-809">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6cd6f-810">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-811">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-812">'Identity'</span></span>
- <span data-ttu-id="6cd6f-813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-813">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-814">'Razor'</span></span>
- <span data-ttu-id="6cd6f-815">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-815">'SignalR' uid:</span></span> 

<span data-ttu-id="6cd6f-816">-------------: | :---title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-816">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6cd6f-817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-817">'Blazor'</span></span>
- <span data-ttu-id="6cd6f-818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-818">'Identity'</span></span>
- <span data-ttu-id="6cd6f-819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-819">'Let's Encrypt'</span></span>
- <span data-ttu-id="6cd6f-820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6cd6f-820">'Razor'</span></span>
- <span data-ttu-id="6cd6f-821">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6cd6f-821">'SignalR' uid:</span></span> 

<span data-ttu-id="6cd6f-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="6cd6f-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="6cd6f-823">Esempio:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-823">Example:</span></span><br><span data-ttu-id="6cd6f-824">`services.AddSingleton<IMyDep, MyDep>();`| Sì | Sì | No | |`Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="6cd6f-824">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="6cd6f-825">Esempi:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-825">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="6cd6f-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));`| Sì | Sì | Sì | |`Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="6cd6f-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="6cd6f-827">Esempio:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-827">Example:</span></span><br><span data-ttu-id="6cd6f-828">`services.AddSingleton<MyDep>();`| Sì | No | No | |`AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="6cd6f-828">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="6cd6f-829">Esempi:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-829">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="6cd6f-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));`| No | Sì | Sì | |`AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="6cd6f-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="6cd6f-831">Esempi:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-831">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="6cd6f-832">`services.AddSingleton(new MyDep("A string!"));`| No | No | Sì |</span><span class="sxs-lookup"><span data-stu-id="6cd6f-832">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="6cd6f-833">Per ulteriori informazioni sull'eliminazione dei tipi, vedere la sezione [Eliminazione dei servizi](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-833">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="6cd6f-834">Uno scenario comune per più implementazioni è costituito dai [tipi di simulazioni per i test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-834">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="6cd6f-835">I metodi `TryAdd{LIFETIME}` registrano il servizio solo se esiste già un'implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-835">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="6cd6f-836">Nell'esempio seguente, la prima riga registra `MyDependency` per `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-836">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="6cd6f-837">La seconda riga non ha alcun effetto perché `IMyDependency` dispone già di un'implementazione registrata:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-837">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="6cd6f-838">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-838">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="6cd6f-839">I metodi [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrano il servizio solo se non esiste già un'implementazione *dello stesso tipo*.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-839">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="6cd6f-840">Più servizi vengono risolti tramite `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-840">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="6cd6f-841">Durante la registrazione di servizi, lo sviluppatore desidera aggiungere un'istanza solo se non ne è già stata aggiunta una dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-841">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="6cd6f-842">In genere, questo metodo viene utilizzato dagli autori di librerie per evitare di registrare due copie di un'istanza nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-842">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="6cd6f-843">Nell'esempio seguente, la prima riga registra `MyDep` per `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-843">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="6cd6f-844">La seconda riga registra `MyDep` per `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-844">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="6cd6f-845">La seconda riga non ha alcun effetto perché `IMyDep1` dispone già di un'implementazione registrata di `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-845">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="6cd6f-846">Comportamento dell'inserimento del costruttore</span><span class="sxs-lookup"><span data-stu-id="6cd6f-846">Constructor injection behavior</span></span>

<span data-ttu-id="6cd6f-847">I servizi possono essere risolti con due meccanismi:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-847">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="6cd6f-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Consente la creazione di oggetti senza registrazione del servizio nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="6cd6f-849">`ActivatorUtilities` viene usato con astrazioni esposte all'utente, ad esempio helper tag, controller MVC e strumenti di associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-849">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="6cd6f-850">I costruttori possono accettare argomenti non forniti tramite l'inserimento di dipendenze, ma gli argomenti devono assegnare valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-850">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="6cd6f-851">Quando i servizi vengono risolti da `IServiceProvider` o `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede un costruttore *pubblico* .</span><span class="sxs-lookup"><span data-stu-id="6cd6f-851">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="6cd6f-852">Quando i servizi vengono risolti da `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede l'esistenza di un solo costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-852">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="6cd6f-853">Sebbene siano supportati gli overload dei costruttori, può essere presente un solo overload i cui argomenti possono essere tutti specificati tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-853">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="6cd6f-854">Contesti di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="6cd6f-854">Entity Framework contexts</span></span>

<span data-ttu-id="6cd6f-855">I contesti di Entity Framework vengono in genere aggiunti al contenitore di servizi usando la [durata con ambito](#service-lifetimes) perché le operazioni di database delle app Web hanno di solito come ambito la richiesta client.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-855">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="6cd6f-856">L'ambito della durata predefinita è se una durata non è specificata da un overload [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) durante la registrazione del contesto del database.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-856">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="6cd6f-857">I servizi con una durata specificata non devono usare un contesto di database con una durata più breve rispetto al servizio.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-857">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="6cd6f-858">Opzioni di durata e di registrazione</span><span class="sxs-lookup"><span data-stu-id="6cd6f-858">Lifetime and registration options</span></span>

<span data-ttu-id="6cd6f-859">Per illustrare la differenza tra le opzioni di durata e registrazione, si considerino le interfacce seguenti che rappresentano le attività come un'operazione con un identificatore univoco, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-859">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="6cd6f-860">A seconda del modo in cui la durata di un servizio operativo viene configurata per le interfacce seguenti, il contenitore fornisce la stessa istanza o un'istanza diversa del servizio quando richiesto da una classe:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-860">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="6cd6f-861">Le interfacce vengono implementate nella classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-861">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="6cd6f-862">Il costruttore `Operation` genera un GUID se non ne viene fornito uno:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-862">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="6cd6f-863">Viene registrato un `OperationService` che dipende da ognuno degli altri tipi `Operation`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-863">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="6cd6f-864">Quando viene richiesto `OperationService` tramite l'inserimento delle dipendenze, riceve una nuova istanza di ogni servizio o un'istanza esistente in base alla durata del servizio dipendente.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-864">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="6cd6f-865">Se i servizi temporanei vengono creati quando vengono richiesti dal contenitore, `OperationId` del servizio `IOperationTransient` è diverso da `OperationId` di `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-865">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="6cd6f-866">`OperationService` riceve una nuova istanza della classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-866">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="6cd6f-867">La nuova istanza restituisce un `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-867">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="6cd6f-868">Se i servizi con ambito vengono creati per ogni richiesta client, `OperationId` del servizio `IOperationScoped` corrisponde a `OperationService` all'interno di una richiesta client.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-868">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="6cd6f-869">In tutte le richieste client entrambi i servizi condividono un valore `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-869">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="6cd6f-870">Se i servizi singleton e con istanza singleton vengono creati una sola volta e usati per tutte le richieste client e tutti i servizi, `OperationId` rimane costante in tutte le richieste di servizio.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-870">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="6cd6f-871">In `Startup.ConfigureServices`, ogni tipo viene aggiunto al contenitore in base alla durata denominata:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-871">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="6cd6f-872">Il servizio `IOperationSingletonInstance` usa un'istanza specifica con un ID noto di `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-872">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="6cd6f-873">È chiaro quando questo tipo è in uso (il relativo GUID è composto da tutti zero).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-873">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="6cd6f-874">L'app di esempio dimostra le durate degli oggetti all'interno e tra le singole richieste.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-874">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="6cd6f-875">L'`IndexModel` dell'app di esempio richiede ogni genere di tipo `IOperation` e `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-875">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="6cd6f-876">La pagina visualizza quindi tutti i valori `OperationId` della classe del modello di pagina e del servizio tramite assegnazioni di proprietà:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-876">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="6cd6f-877">L'output seguente mostra i risultati delle due richieste:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-877">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="6cd6f-878">**Prima richiesta:**</span><span class="sxs-lookup"><span data-stu-id="6cd6f-878">**First request:**</span></span>

<span data-ttu-id="6cd6f-879">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-879">Controller operations:</span></span>

<span data-ttu-id="6cd6f-880">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="6cd6f-880">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="6cd6f-881">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="6cd6f-881">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="6cd6f-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="6cd6f-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="6cd6f-883">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="6cd6f-883">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="6cd6f-884">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-884">`OperationService` operations:</span></span>

<span data-ttu-id="6cd6f-885">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="6cd6f-885">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="6cd6f-886">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="6cd6f-886">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="6cd6f-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="6cd6f-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="6cd6f-888">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="6cd6f-888">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="6cd6f-889">**Seconda richiesta:**</span><span class="sxs-lookup"><span data-stu-id="6cd6f-889">**Second request:**</span></span>

<span data-ttu-id="6cd6f-890">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-890">Controller operations:</span></span>

<span data-ttu-id="6cd6f-891">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="6cd6f-891">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="6cd6f-892">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="6cd6f-892">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="6cd6f-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="6cd6f-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="6cd6f-894">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="6cd6f-894">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="6cd6f-895">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-895">`OperationService` operations:</span></span>

<span data-ttu-id="6cd6f-896">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="6cd6f-896">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="6cd6f-897">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="6cd6f-897">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="6cd6f-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="6cd6f-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="6cd6f-899">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="6cd6f-899">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="6cd6f-900">Osservare i valori `OperationId` che variano all'interno della richiesta e da una richiesta e l'altra:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-900">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="6cd6f-901">Gli oggetti *temporanei* sono sempre diversi.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-901">*Transient* objects are always different.</span></span> <span data-ttu-id="6cd6f-902">Il valore `OperationId` temporaneo per la prima e la seconda richiesta client è diverso per entrambe le operazioni `OperationService` e in tutte le richieste client.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-902">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="6cd6f-903">Viene fornita una nuova istanza per ogni richiesta di servizio e richiesta client.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-903">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="6cd6f-904">Gli oggetti *con ambito* sono gli stessi all'interno di una richiesta client, ma variano nelle diverse richieste client.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-904">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="6cd6f-905">Gli oggetti *singleton* sono gli stessi per ogni oggetto e per ogni richiesta, indipendentemente dal fatto che un' `Operation` istanza venga fornita in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6cd6f-905">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="6cd6f-906">Chiamare i servizi da main</span><span class="sxs-lookup"><span data-stu-id="6cd6f-906">Call services from main</span></span>

<span data-ttu-id="6cd6f-907">Creare un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>IServiceScope con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) per risolvere un servizio con ambito nell'ambito di applicazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-907">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="6cd6f-908">Questo approccio è utile per l'accesso a un servizio con ambito all'avvio e per l'esecuzione di attività di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-908">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="6cd6f-909">L'esempio seguente indica come ottenere un contesto per `MyScopedService` in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-909">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="6cd6f-910">Convalida dell'ambito</span><span class="sxs-lookup"><span data-stu-id="6cd6f-910">Scope validation</span></span>

<span data-ttu-id="6cd6f-911">Quando l'app viene eseguita nell'ambiente di sviluppo, il provider di servizi predefinito esegue controlli per verificare che:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-911">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="6cd6f-912">I servizi con ambito non vengano risolti direttamente o indirettamente dal provider di servizi radice.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-912">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="6cd6f-913">I servizi con ambito non vengano inseriti direttamente o indirettamente in singleton.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-913">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="6cd6f-914">Il provider di servizi radice venga creato con la chiamata di <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-914">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="6cd6f-915">La durata del provider di servizi radice corrisponde alla durata dell'app/server quando il provider viene avviato con l'app e viene eliminato alla chiusura dell'app.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-915">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="6cd6f-916">I servizi con ambito vengono eliminati dal contenitore che li ha creati.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-916">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="6cd6f-917">Se un servizio con ambito viene creato nel contenitore radice, la durata del servizio viene di fatto convertita in singleton, perché il servizio viene eliminato solo dal contenitore radice alla chiusura dell'app/server.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-917">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="6cd6f-918">La convalida degli ambiti servizio rileva queste situazioni nel contesto della chiamata di `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-918">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="6cd6f-919">Per altre informazioni, vedere <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-919">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="6cd6f-920">Servizi di richiesta</span><span class="sxs-lookup"><span data-stu-id="6cd6f-920">Request Services</span></span>

<span data-ttu-id="6cd6f-921">I servizi disponibili all'interno di una richiesta ASP.NET Core da `HttpContext` vengono esposti tramite la raccolta [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-921">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="6cd6f-922">I servizi di richiesta rappresentano i servizi configurati e richiesti come parte dell'app.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-922">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="6cd6f-923">Quando gli oggetti specificano dipendenze, le dipendenze sono soddisfatte dai tipi individuati in `RequestServices`, non in `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-923">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="6cd6f-924">In generale, l'app non deve usare queste proprietà direttamente.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-924">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="6cd6f-925">Richiedere invece i tipi richiesti dalle classi tramite i costruttori di classi e consentire al framework di inserire le dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-925">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="6cd6f-926">Si ottengono classi più facili da testare.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-926">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="6cd6f-927">È consigliabile richiedere le dipendenze come parametri del costruttore per l'accesso alla raccolta `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-927">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="6cd6f-928">Progettare i servizi per l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="6cd6f-928">Design services for dependency injection</span></span>

<span data-ttu-id="6cd6f-929">Le procedure consigliate prevedono di:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-929">Best practices are to:</span></span>

* <span data-ttu-id="6cd6f-930">Progettare i servizi per usare l'inserimento delle dipendenze per ottenere le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-930">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="6cd6f-931">Evitare membri e classi statiche con stato.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-931">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="6cd6f-932">Progettare le app in modo da usare i servizi singleton, evitando così di creare uno stato globale.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-932">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="6cd6f-933">Evitare la creazione diretta di istanze delle classi dipendenti all'interno di servizi.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-933">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="6cd6f-934">La creazione diretta di istanze associa il codice a una determinata implementazione.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-934">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="6cd6f-935">Fare in modo che le classi siano piccole, con factoring corretto e facili da testare.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-935">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="6cd6f-936">Se una classe sembra avere troppe dipendenze inserite, ciò significa in genere che la classe ha un numero eccessivo di responsabilità e sta violando il [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (principio di responsabilità singola).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-936">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="6cd6f-937">Tentare di effettuare il refactoring della classe spostando alcune delle responsabilità in una nuova classe.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-937">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="6cd6f-938">Tenere presente che Razor le classi del modello della pagina delle pagine e le classi controller MVC devono concentrarsi sulle problematiche dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-938">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="6cd6f-939">Di conseguenza, le regole business e i dettagli di implementazione di accesso ai dati devono essere inseriti in classi appropriate per queste [problematiche separate](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-939">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="6cd6f-940">Eliminazione dei servizi</span><span class="sxs-lookup"><span data-stu-id="6cd6f-940">Disposal of services</span></span>

<span data-ttu-id="6cd6f-941">Il contenitore chiama <xref:System.IDisposable.Dispose*> per i tipi <xref:System.IDisposable> creati.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-941">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="6cd6f-942">Se un'istanza viene aggiunta al contenitore dal codice utente, non viene eliminata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-942">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="6cd6f-943">Nell'esempio seguente i servizi vengono creati dal contenitore del servizio ed eliminati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-943">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="6cd6f-944">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-944">In the following example:</span></span>

* <span data-ttu-id="6cd6f-945">Le istanze del servizio non vengono create dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-945">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="6cd6f-946">Le durate di servizio previste non sono note dal Framework.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-946">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="6cd6f-947">Il Framework non elimina automaticamente i servizi.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-947">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="6cd6f-948">Se i servizi non vengono eliminati in modo esplicito nel codice dello sviluppatore, vengono mantenuti fino a quando l'app non viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-948">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="6cd6f-949">Sostituzione del contenitore di servizi predefinito</span><span class="sxs-lookup"><span data-stu-id="6cd6f-949">Default service container replacement</span></span>

<span data-ttu-id="6cd6f-950">Il contenitore dei servizi incorporato è progettato per soddisfare le esigenze del Framework e della maggior parte delle app consumer.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-950">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="6cd6f-951">Si consiglia di usare il contenitore predefinito a meno che non sia necessaria una funzionalità specifica che il contenitore incorporato non supporta, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-951">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="6cd6f-952">Inserimento di proprietà</span><span class="sxs-lookup"><span data-stu-id="6cd6f-952">Property injection</span></span>
* <span data-ttu-id="6cd6f-953">Inserimento in base al nome</span><span class="sxs-lookup"><span data-stu-id="6cd6f-953">Injection based on name</span></span>
* <span data-ttu-id="6cd6f-954">Contenitori figlio</span><span class="sxs-lookup"><span data-stu-id="6cd6f-954">Child containers</span></span>
* <span data-ttu-id="6cd6f-955">Gestione della durata personalizzata</span><span class="sxs-lookup"><span data-stu-id="6cd6f-955">Custom lifetime management</span></span>
* <span data-ttu-id="6cd6f-956">Supporto di `Func<T>` per l'inizializzazione differita</span><span class="sxs-lookup"><span data-stu-id="6cd6f-956">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="6cd6f-957">Registrazione basata sulle convenzioni</span><span class="sxs-lookup"><span data-stu-id="6cd6f-957">Convention-based registration</span></span>

<span data-ttu-id="6cd6f-958">Con ASP.NET Core app è possibile usare i contenitori di terze parti seguenti:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-958">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="6cd6f-959">Autofac</span><span class="sxs-lookup"><span data-stu-id="6cd6f-959">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="6cd6f-960">DryIoc</span><span class="sxs-lookup"><span data-stu-id="6cd6f-960">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="6cd6f-961">Laura</span><span class="sxs-lookup"><span data-stu-id="6cd6f-961">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="6cd6f-962">LightInject</span><span class="sxs-lookup"><span data-stu-id="6cd6f-962">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="6cd6f-963">Lamar</span><span class="sxs-lookup"><span data-stu-id="6cd6f-963">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="6cd6f-964">Stashbox</span><span class="sxs-lookup"><span data-stu-id="6cd6f-964">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="6cd6f-965">Unity</span><span class="sxs-lookup"><span data-stu-id="6cd6f-965">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="6cd6f-966">Thread safety</span><span class="sxs-lookup"><span data-stu-id="6cd6f-966">Thread safety</span></span>

<span data-ttu-id="6cd6f-967">Creare servizi singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-967">Create thread-safe singleton services.</span></span> <span data-ttu-id="6cd6f-968">Se un servizio singleton ha una dipendenza in un servizio temporaneo, è necessario che anche il servizio temporaneo sia thread-safe, a seconda di come viene usato dal singleton.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-968">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="6cd6f-969">Il metodo factory di un singolo servizio, ad esempio il secondo argomento di [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), non deve essere thread-safe.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-969">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="6cd6f-970">Come nel caso di un costruttore di tipo (`static`), è garantito che venga chiamato una sola volta da un singolo thread.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-970">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="6cd6f-971">Raccomandazioni</span><span class="sxs-lookup"><span data-stu-id="6cd6f-971">Recommendations</span></span>

* <span data-ttu-id="6cd6f-972">La risoluzione basata sui servizi `async/await` e `Task` non è supportata.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-972">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="6cd6f-973">Dato che C# non supporta i costruttori asincroni, il modello consigliato consiste nell'usare i metodi asincroni dopo avere risolto in modo sincrono il servizio.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-973">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="6cd6f-974">Evitare di archiviare i dati e la configurazione direttamente nel contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-974">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="6cd6f-975">Ad esempio, il carrello acquisti di un utente non dovrebbe in genere essere aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-975">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="6cd6f-976">La configurazione deve usare il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-976">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="6cd6f-977">Analogamente, evitare gli oggetti "contenitori di dati" che hanno la sola funzione di consentire l'accesso ad altri oggetti.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-977">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="6cd6f-978">È preferibile richiedere l'elemento effettivo tramite inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-978">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="6cd6f-979">Evitare l'accesso statico ai servizi (ad esempio, la tipizzazione statica [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) da usare in altre posizioni).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-979">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="6cd6f-980">Evitare di usare il *modello del localizzatore di servizi*, che combina [inversione delle strategie di controllo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="6cd6f-980">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="6cd6f-981">Non richiamare <xref:System.IServiceProvider.GetService*> per ottenere un'istanza del servizio quando si può usare invece di:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-981">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="6cd6f-982">**Errata**</span><span class="sxs-lookup"><span data-stu-id="6cd6f-982">**Incorrect:**</span></span>

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

    <span data-ttu-id="6cd6f-983">**Corretto**:</span><span class="sxs-lookup"><span data-stu-id="6cd6f-983">**Correct**:</span></span>

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

  * <span data-ttu-id="6cd6f-984">Evitare di inserire una factory che risolve le dipendenze in fase di esecuzione usando <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="6cd6f-984">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="6cd6f-985">Evitare l'accesso statico a `HttpContext` (ad esempio [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="6cd6f-985">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="6cd6f-986">È tuttavia possibile che in alcuni casi queste raccomandazioni debbano essere ignorate.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-986">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="6cd6f-987">Le eccezioni sono rare e principalmente si tratta di casi speciali all'interno del framework stesso.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-987">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="6cd6f-988">L'inserimento di dipendenze è un'*alternativa* ai modelli di accesso agli oggetti statici/globali.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-988">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="6cd6f-989">Se l'inserimento di dipendenze viene usato con l'accesso agli oggetti statico i vantaggi non saranno evidenti.</span><span class="sxs-lookup"><span data-stu-id="6cd6f-989">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6cd6f-990">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6cd6f-990">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="6cd6f-991">Scrittura di codice pulito in ASP.NET Core con inserimento delle dipendenze (MSDN)</span><span class="sxs-lookup"><span data-stu-id="6cd6f-991">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="6cd6f-992">Explicit Dependencies Principle (Principio delle dipendenze esplicite)</span><span class="sxs-lookup"><span data-stu-id="6cd6f-992">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="6cd6f-993">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler) (Contenitori di inversione del controllo e modello di inserimento delle dipendenze)</span><span class="sxs-lookup"><span data-stu-id="6cd6f-993">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="6cd6f-994">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Come registrare un servizio con più interfacce in ASP.NET Core DI)</span><span class="sxs-lookup"><span data-stu-id="6cd6f-994">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
