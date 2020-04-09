---
title: Inserimento delle dipendenze in ASP.NET Core
author: rick-anderson
description: Informazioni su come ASP.NET Core implementa l'inserimento delle dipendenze e su come usare questa funzionalità.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
uid: fundamentals/dependency-injection
ms.openlocfilehash: 943ea30c2e4887638f69b6dcdb7e323bcee40240
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405983"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="57743-103">Inserimento delle dipendenze in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57743-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="57743-104">Di [Steve Smith](https://ardalis.com/) e [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="57743-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="57743-105">ASP.NET Core supporta il modello progettuale software per l'inserimento delle dipendenze, ovvero una tecnica per ottenere l'[inversione del controllo (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra classi e relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="57743-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="57743-106">Per altre informazioni specifiche per l'inserimento delle dipendenze all'interno di controller MVC, vedere <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="57743-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="57743-107">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="57743-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="57743-108">Panoramica dell'inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="57743-108">Overview of dependency injection</span></span>

<span data-ttu-id="57743-109">Una *dipendenza* è qualsiasi oggetto richiesto da un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="57743-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="57743-110">Esaminare la classe `MyDependency` seguente con un metodo `WriteMessage` da cui dipendono altre classi in un'app:</span><span class="sxs-lookup"><span data-stu-id="57743-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="57743-111">È possibile creare un'istanza della classe `MyDependency` per rendere il metodo `WriteMessage`disponibile per una classe.</span><span class="sxs-lookup"><span data-stu-id="57743-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="57743-112">La classe `MyDependency` è una dipendenza della classe `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="57743-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="57743-113">La classe crea e dipende direttamente dall'istanza `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="57743-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="57743-114">Le dipendenze del codice (come nell'esempio precedente) sono problematiche e devono essere evitate per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="57743-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="57743-115">Per sostituire `MyDependency` con un'implementazione diversa, la classe deve essere modificata.</span><span class="sxs-lookup"><span data-stu-id="57743-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="57743-116">Se `MyDependency` ha dipendenze, devono essere configurate dalla classe.</span><span class="sxs-lookup"><span data-stu-id="57743-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="57743-117">In un progetto di grandi dimensioni con più classi che dipendono da `MyDependency`, il codice di configurazione diventa sparso in tutta l'app.</span><span class="sxs-lookup"><span data-stu-id="57743-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="57743-118">È difficile eseguire unit test di questa implementazione.</span><span class="sxs-lookup"><span data-stu-id="57743-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="57743-119">L'app dovrebbe usare una classe `MyDependency` fittizia o stub, ma ciò non è possibile con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="57743-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="57743-120">L'inserimento delle dipendenze consente di risolvere questi problemi tramite:</span><span class="sxs-lookup"><span data-stu-id="57743-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="57743-121">L'uso di un'interfaccia o di una classe di base per astrarre l'implementazione delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="57743-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="57743-122">La registrazione della dipendenza in un contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="57743-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="57743-123">ASP.NET Core offre il contenitore di servizi predefinito <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="57743-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="57743-124">I servizi vengono registrati nel metodo `Startup.ConfigureServices` dell'app.</span><span class="sxs-lookup"><span data-stu-id="57743-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="57743-125">L'*inserimento* del servizio nel costruttore della classe in cui viene usato.</span><span class="sxs-lookup"><span data-stu-id="57743-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="57743-126">Il framework si assume la responsabilità della creazione di un'istanza della dipendenza e della sua eliminazione quando non è più necessaria.</span><span class="sxs-lookup"><span data-stu-id="57743-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="57743-127">Nell'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l'interfaccia `IMyDependency` definisce un metodo che il servizio fornisce all'app:</span><span class="sxs-lookup"><span data-stu-id="57743-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="57743-128">Questa interfaccia viene implementata da un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="57743-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="57743-129">`MyDependency` richiede <xref:Microsoft.Extensions.Logging.ILogger`1> nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="57743-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="57743-130">Non è insolito usare l'inserimento delle dipendenze in modo concatenato.</span><span class="sxs-lookup"><span data-stu-id="57743-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="57743-131">Ogni dipendenza richiesta richiede a sua volta le proprie dipendenze.</span><span class="sxs-lookup"><span data-stu-id="57743-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="57743-132">Il contenitore risolve le dipendenze nel grafico e restituisce il servizio completamente risolto.</span><span class="sxs-lookup"><span data-stu-id="57743-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="57743-133">Il set di dipendenze che devono essere risolte viene generalmente chiamato *albero delle dipendenze* o *grafico dipendenze* o *grafico degli oggetti*.</span><span class="sxs-lookup"><span data-stu-id="57743-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="57743-134">`IMyDependency` e `ILogger<TCategoryName>` devono essere registrati nel contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="57743-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="57743-135">`IMyDependency` viene registrato in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="57743-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="57743-136">`ILogger<TCategoryName>`viene registrato dall'infrastruttura di astrazione di registrazione, pertanto è un [servizio fornito dal framework](#framework-provided-services) registrato per impostazione predefinita dal framework.</span><span class="sxs-lookup"><span data-stu-id="57743-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="57743-137">Il contenitore risolve `ILogger<TCategoryName>` avvalendosi di [tipi aperti (generici)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando l'esigenza di registrare ogni singolo [tipo costruito (generico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="57743-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="57743-138">Nell'app di esempio, il servizio `IMyDependency` viene registrato con il tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="57743-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="57743-139">La registrazione definisce come ambito della durata di servizio la durata di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="57743-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="57743-140">Le [durate dei servizi](#service-lifetimes) sono descritte più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="57743-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="57743-141">Ogni metodo di estensione `services.Add{SERVICE_NAME}` aggiunge (e potenzialmente configura) i servizi.</span><span class="sxs-lookup"><span data-stu-id="57743-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="57743-142">Ad esempio, `services.AddMvc()` aggiunge i servizi richiesti da Razor Pages e MVC.</span><span class="sxs-lookup"><span data-stu-id="57743-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="57743-143">È consigliabile che le app seguano questa convenzione.</span><span class="sxs-lookup"><span data-stu-id="57743-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="57743-144">Inserire i metodi di estensione nello spazio dei nomi [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) per incapsulare i gruppi di registrazioni di servizio.</span><span class="sxs-lookup"><span data-stu-id="57743-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="57743-145">Se il costruttore del servizio richiede un [tipo incorporato](/dotnet/csharp/language-reference/keywords/built-in-types-table), ad esempio `string`, è possibile inserirlo usando la [configurazione](xref:fundamentals/configuration/index) o il [modello di opzioni](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="57743-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="57743-146">È richiesta un'istanza del servizio tramite il costruttore di una classe in cui il servizio viene usato e assegnato a un campo privato.</span><span class="sxs-lookup"><span data-stu-id="57743-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="57743-147">Il campo viene usato per accedere al servizio in base alle esigenze per l'intera classe.</span><span class="sxs-lookup"><span data-stu-id="57743-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="57743-148">Nell'app di esempio, viene richiesta l'istanza `IMyDependency` usata per chiamare il metodo `WriteMessage` del servizio:</span><span class="sxs-lookup"><span data-stu-id="57743-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="57743-149">Servizi inseriti nell'avvio</span><span class="sxs-lookup"><span data-stu-id="57743-149">Services injected into Startup</span></span>

<span data-ttu-id="57743-150">Solo i seguenti tipi di servizio `Startup` possono essere inseriti<xref:Microsoft.Extensions.Hosting.IHostBuilder>nel costruttore quando si utilizza l'host generico ( ):</span><span class="sxs-lookup"><span data-stu-id="57743-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="57743-151">I servizi possono `Startup.Configure`essere iniettati in:</span><span class="sxs-lookup"><span data-stu-id="57743-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="57743-152">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="57743-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="57743-153">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="57743-153">Framework-provided services</span></span>

<span data-ttu-id="57743-154">Il `Startup.ConfigureServices` metodo è responsabile della definizione dei servizi utilizzati dall'app, incluse le funzionalità della piattaforma, ad esempio Entity Framework Core e ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="57743-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="57743-155">Inizialmente, `IServiceCollection` i `ConfigureServices` servizi forniti a hanno definito dal framework a seconda di [come è stato configurato l'host.](xref:fundamentals/index#host)</span><span class="sxs-lookup"><span data-stu-id="57743-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="57743-156">Non è raro che un'app basata su un modello ASP.NET Core disponga di centinaia di servizi registrati dal framework.</span><span class="sxs-lookup"><span data-stu-id="57743-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="57743-157">Nella tabella seguente è elencato un piccolo esempio di servizi registrati nel framework.</span><span class="sxs-lookup"><span data-stu-id="57743-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="57743-158">Tipo di servizio</span><span class="sxs-lookup"><span data-stu-id="57743-158">Service Type</span></span> | <span data-ttu-id="57743-159">Durata</span><span class="sxs-lookup"><span data-stu-id="57743-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="57743-160">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="57743-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="57743-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="57743-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="57743-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="57743-164">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="57743-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="57743-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="57743-166">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="57743-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="57743-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="57743-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="57743-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="57743-170">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="57743-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="57743-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="57743-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="57743-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="57743-174">Registrare servizi aggiuntivi con metodi di estensioneRegister additional services with extension methods</span><span class="sxs-lookup"><span data-stu-id="57743-174">Register additional services with extension methods</span></span>

<span data-ttu-id="57743-175">Quando è disponibile un metodo di estensione della raccolta di servizi per la registrazione di un servizio (e dei servizi dipendenti, se necessario), la convenzione consiste nell'usare un singolo metodo di estensione `Add{SERVICE_NAME}` per registrare tutti i servizi richiesti da tale servizio.</span><span class="sxs-lookup"><span data-stu-id="57743-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="57743-176">Il codice seguente è un esempio di come aggiungere servizi aggiuntivi al contenitore <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>usando i metodi di estensione [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e :</span><span class="sxs-lookup"><span data-stu-id="57743-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="57743-177">Per altre informazioni, vedere la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> nella documentazione delle API.</span><span class="sxs-lookup"><span data-stu-id="57743-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="57743-178">Durate dei servizi</span><span class="sxs-lookup"><span data-stu-id="57743-178">Service lifetimes</span></span>

<span data-ttu-id="57743-179">Scegliere una durata appropriata per ogni servizio registrato.</span><span class="sxs-lookup"><span data-stu-id="57743-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="57743-180">I servizi ASP.NET Core possono essere configurati con le impostazioni di durata seguenti:</span><span class="sxs-lookup"><span data-stu-id="57743-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="57743-181">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="57743-181">Transient</span></span>

<span data-ttu-id="57743-182">I servizi con durata temporanea (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) vengono creati ogni volta che vengono richiesti dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="57743-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="57743-183">Questa impostazione di durata è consigliata per i servizi semplici senza stati.</span><span class="sxs-lookup"><span data-stu-id="57743-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="57743-184">Con ambito</span><span class="sxs-lookup"><span data-stu-id="57743-184">Scoped</span></span>

<span data-ttu-id="57743-185">I servizi con durata con ambito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) vengono creati una volta per ogni richiesta client (connessione).</span><span class="sxs-lookup"><span data-stu-id="57743-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="57743-186">Quando si usa un servizio con ambito in un middleware, inserire il servizio nel metodo `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="57743-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="57743-187">Evitare l'inserimento tramite il costruttore, che impone al servizio il comportamento singleton.</span><span class="sxs-lookup"><span data-stu-id="57743-187">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="57743-188">Per altre informazioni, vedere <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="57743-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="57743-189">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-189">Singleton</span></span>

<span data-ttu-id="57743-190">I servizi con durata singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) vengono creati la prima volta che vengono richiesti (o quando si esegue `Startup.ConfigureServices` e viene specificata un'istanza con la registrazione del servizio).</span><span class="sxs-lookup"><span data-stu-id="57743-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="57743-191">Tutte le richieste successive usano la stessa istanza.</span><span class="sxs-lookup"><span data-stu-id="57743-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="57743-192">Se l'app richiede un comportamento singleton, è consigliabile consentire al contenitore del servizio di gestire la durata del servizio.</span><span class="sxs-lookup"><span data-stu-id="57743-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="57743-193">Non implementare lo schema progettuale singleton e fornire codice utente per gestire la durata dell'oggetto nella classe.</span><span class="sxs-lookup"><span data-stu-id="57743-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="57743-194">Può essere pericoloso risolvere un servizio con ambito da un singleton.</span><span class="sxs-lookup"><span data-stu-id="57743-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="57743-195">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="57743-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="57743-196">Metodi di registrazione del servizio</span><span class="sxs-lookup"><span data-stu-id="57743-196">Service registration methods</span></span>

<span data-ttu-id="57743-197">I metodi di estensione per la registrazione del servizio offrono overload utili in scenari specifici.</span><span class="sxs-lookup"><span data-stu-id="57743-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="57743-198">Metodo</span><span class="sxs-lookup"><span data-stu-id="57743-198">Method</span></span> | <span data-ttu-id="57743-199">Automatico</span><span class="sxs-lookup"><span data-stu-id="57743-199">Automatic</span></span><br><span data-ttu-id="57743-200">object</span><span class="sxs-lookup"><span data-stu-id="57743-200">object</span></span><br><span data-ttu-id="57743-201">eliminazione</span><span class="sxs-lookup"><span data-stu-id="57743-201">disposal</span></span> | <span data-ttu-id="57743-202">Multipli</span><span class="sxs-lookup"><span data-stu-id="57743-202">Multiple</span></span><br><span data-ttu-id="57743-203">implementazioni</span><span class="sxs-lookup"><span data-stu-id="57743-203">implementations</span></span> | <span data-ttu-id="57743-204">Pass args</span><span class="sxs-lookup"><span data-stu-id="57743-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="57743-205">Esempio:</span><span class="sxs-lookup"><span data-stu-id="57743-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="57743-206">Sì</span><span class="sxs-lookup"><span data-stu-id="57743-206">Yes</span></span> | <span data-ttu-id="57743-207">Sì</span><span class="sxs-lookup"><span data-stu-id="57743-207">Yes</span></span> | <span data-ttu-id="57743-208">No</span><span class="sxs-lookup"><span data-stu-id="57743-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="57743-209">Esempi:</span><span class="sxs-lookup"><span data-stu-id="57743-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="57743-210">Sì</span><span class="sxs-lookup"><span data-stu-id="57743-210">Yes</span></span> | <span data-ttu-id="57743-211">Sì</span><span class="sxs-lookup"><span data-stu-id="57743-211">Yes</span></span> | <span data-ttu-id="57743-212">Sì</span><span class="sxs-lookup"><span data-stu-id="57743-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="57743-213">Esempio:</span><span class="sxs-lookup"><span data-stu-id="57743-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="57743-214">Sì</span><span class="sxs-lookup"><span data-stu-id="57743-214">Yes</span></span> | <span data-ttu-id="57743-215">No</span><span class="sxs-lookup"><span data-stu-id="57743-215">No</span></span> | <span data-ttu-id="57743-216">No</span><span class="sxs-lookup"><span data-stu-id="57743-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="57743-217">Esempi:</span><span class="sxs-lookup"><span data-stu-id="57743-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="57743-218">No</span><span class="sxs-lookup"><span data-stu-id="57743-218">No</span></span> | <span data-ttu-id="57743-219">Sì</span><span class="sxs-lookup"><span data-stu-id="57743-219">Yes</span></span> | <span data-ttu-id="57743-220">Sì</span><span class="sxs-lookup"><span data-stu-id="57743-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="57743-221">Esempi:</span><span class="sxs-lookup"><span data-stu-id="57743-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="57743-222">No</span><span class="sxs-lookup"><span data-stu-id="57743-222">No</span></span> | <span data-ttu-id="57743-223">No</span><span class="sxs-lookup"><span data-stu-id="57743-223">No</span></span> | <span data-ttu-id="57743-224">Sì</span><span class="sxs-lookup"><span data-stu-id="57743-224">Yes</span></span> |

<span data-ttu-id="57743-225">Per ulteriori informazioni sull'eliminazione dei tipi, vedere la sezione [Eliminazione dei servizi](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="57743-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="57743-226">Uno scenario comune per più implementazioni è costituito dai [tipi di simulazioni per i test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="57743-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="57743-227">I metodi `TryAdd{LIFETIME}` registrano il servizio solo se esiste già un'implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="57743-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="57743-228">Nell'esempio seguente, la prima riga registra `MyDependency` per `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="57743-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="57743-229">La seconda riga non ha alcun effetto perché `IMyDependency` dispone già di un'implementazione registrata:</span><span class="sxs-lookup"><span data-stu-id="57743-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="57743-230">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="57743-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="57743-231">I metodi [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrano il servizio solo se non esiste già un'implementazione *dello stesso tipo*.</span><span class="sxs-lookup"><span data-stu-id="57743-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="57743-232">Più servizi vengono risolti tramite `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="57743-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="57743-233">Durante la registrazione di servizi, lo sviluppatore desidera aggiungere un'istanza solo se non ne è già stata aggiunta una dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="57743-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="57743-234">In genere, questo metodo viene utilizzato dagli autori di librerie per evitare di registrare due copie di un'istanza nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="57743-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="57743-235">Nell'esempio seguente, la prima riga registra `MyDep` per `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="57743-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="57743-236">La seconda riga registra `MyDep` per `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="57743-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="57743-237">La seconda riga non ha alcun effetto perché `IMyDep1` dispone già di un'implementazione registrata di `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="57743-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="57743-238">Comportamento dell'inserimento del costruttore</span><span class="sxs-lookup"><span data-stu-id="57743-238">Constructor injection behavior</span></span>

<span data-ttu-id="57743-239">I servizi possono essere risolti con due meccanismi:</span><span class="sxs-lookup"><span data-stu-id="57743-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="57743-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Consente la creazione di oggetti senza registrazione del servizio nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="57743-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="57743-241">`ActivatorUtilities` viene usato con astrazioni esposte all'utente, ad esempio helper tag, controller MVC e strumenti di associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="57743-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="57743-242">I costruttori possono accettare argomenti non forniti tramite l'inserimento di dipendenze, ma gli argomenti devono assegnare valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="57743-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="57743-243">Quando i servizi vengono risolti da `IServiceProvider` o `ActivatorUtilities`, l'inserimento del costruttore richiede un costruttore *pubblico*.</span><span class="sxs-lookup"><span data-stu-id="57743-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="57743-244">Quando i servizi vengono risolti da `ActivatorUtilities`, l'inserimento del costruttore richiede l'esistenza di un solo costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="57743-244">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="57743-245">Sebbene siano supportati gli overload dei costruttori, può essere presente un solo overload i cui argomenti possono essere tutti specificati tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="57743-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="57743-246">Contesti di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="57743-246">Entity Framework contexts</span></span>

<span data-ttu-id="57743-247">I contesti di Entity Framework vengono in genere aggiunti al contenitore di servizi usando la [durata con ambito](#service-lifetimes) perché le operazioni di database delle app Web hanno di solito come ambito la richiesta client.</span><span class="sxs-lookup"><span data-stu-id="57743-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="57743-248">La durata predefinita è con ambito se non viene specificata una durata con un overload [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) quando si registra il contesto del database.</span><span class="sxs-lookup"><span data-stu-id="57743-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="57743-249">I servizi con una durata specificata non devono usare un contesto di database con una durata più breve rispetto al servizio.</span><span class="sxs-lookup"><span data-stu-id="57743-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="57743-250">Opzioni di durata e di registrazione</span><span class="sxs-lookup"><span data-stu-id="57743-250">Lifetime and registration options</span></span>

<span data-ttu-id="57743-251">Per illustrare la differenza tra le opzioni di durata e registrazione, si considerino le interfacce seguenti che rappresentano le attività come un'operazione con un identificatore univoco, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="57743-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="57743-252">A seconda del modo in cui la durata di un servizio operativo viene configurata per le interfacce seguenti, il contenitore fornisce la stessa istanza o un'istanza diversa del servizio quando richiesto da una classe:</span><span class="sxs-lookup"><span data-stu-id="57743-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="57743-253">Le interfacce vengono implementate nella classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="57743-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="57743-254">Il costruttore `Operation` genera un GUID se non ne viene fornito uno:</span><span class="sxs-lookup"><span data-stu-id="57743-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="57743-255">Viene registrato un `OperationService` che dipende da ognuno degli altri tipi `Operation`.</span><span class="sxs-lookup"><span data-stu-id="57743-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="57743-256">Quando viene richiesto `OperationService` tramite l'inserimento delle dipendenze, riceve una nuova istanza di ogni servizio o un'istanza esistente in base alla durata del servizio dipendente.</span><span class="sxs-lookup"><span data-stu-id="57743-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="57743-257">Se i servizi temporanei vengono creati quando vengono richiesti dal contenitore, `OperationId` del servizio `IOperationTransient` è diverso da `OperationId` di `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="57743-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="57743-258">`OperationService` riceve una nuova istanza della classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="57743-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="57743-259">La nuova istanza restituisce un `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="57743-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="57743-260">Se i servizi con ambito vengono creati per ogni richiesta client, `OperationId` del servizio `IOperationScoped` corrisponde a `OperationService` all'interno di una richiesta client.</span><span class="sxs-lookup"><span data-stu-id="57743-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="57743-261">In tutte le richieste client entrambi i servizi condividono un valore `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="57743-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="57743-262">Se i servizi singleton e con istanza singleton vengono creati una sola volta e usati per tutte le richieste client e tutti i servizi, `OperationId` rimane costante in tutte le richieste di servizio.</span><span class="sxs-lookup"><span data-stu-id="57743-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="57743-263">In `Startup.ConfigureServices`, ogni tipo viene aggiunto al contenitore in base alla durata denominata:</span><span class="sxs-lookup"><span data-stu-id="57743-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="57743-264">Il servizio `IOperationSingletonInstance` usa un'istanza specifica con un ID noto di `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="57743-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="57743-265">È chiaro quando questo tipo è in uso (il relativo GUID è composto da tutti zero).</span><span class="sxs-lookup"><span data-stu-id="57743-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="57743-266">L'app di esempio dimostra le durate degli oggetti all'interno e tra le singole richieste.</span><span class="sxs-lookup"><span data-stu-id="57743-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="57743-267">L'`IndexModel` dell'app di esempio richiede ogni genere di tipo `IOperation` e `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="57743-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="57743-268">La pagina visualizza quindi tutti i valori `OperationId` della classe del modello di pagina e del servizio tramite assegnazioni di proprietà:</span><span class="sxs-lookup"><span data-stu-id="57743-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="57743-269">L'output seguente mostra i risultati delle due richieste:</span><span class="sxs-lookup"><span data-stu-id="57743-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="57743-270">**Prima richiesta:**</span><span class="sxs-lookup"><span data-stu-id="57743-270">**First request:**</span></span>

<span data-ttu-id="57743-271">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="57743-271">Controller operations:</span></span>

<span data-ttu-id="57743-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="57743-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="57743-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="57743-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="57743-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="57743-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="57743-275">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="57743-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="57743-276">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="57743-276">`OperationService` operations:</span></span>

<span data-ttu-id="57743-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="57743-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="57743-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="57743-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="57743-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="57743-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="57743-280">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="57743-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="57743-281">**Seconda richiesta:**</span><span class="sxs-lookup"><span data-stu-id="57743-281">**Second request:**</span></span>

<span data-ttu-id="57743-282">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="57743-282">Controller operations:</span></span>

<span data-ttu-id="57743-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="57743-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="57743-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="57743-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="57743-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="57743-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="57743-286">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="57743-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="57743-287">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="57743-287">`OperationService` operations:</span></span>

<span data-ttu-id="57743-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="57743-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="57743-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="57743-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="57743-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="57743-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="57743-291">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="57743-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="57743-292">Osservare i valori `OperationId` che variano all'interno della richiesta e da una richiesta e l'altra:</span><span class="sxs-lookup"><span data-stu-id="57743-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="57743-293">Gli oggetti *temporanei* sono sempre diversi.</span><span class="sxs-lookup"><span data-stu-id="57743-293">*Transient* objects are always different.</span></span> <span data-ttu-id="57743-294">Il valore `OperationId` temporaneo per la prima e la seconda richiesta client è diverso per entrambe le operazioni `OperationService` e in tutte le richieste client.</span><span class="sxs-lookup"><span data-stu-id="57743-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="57743-295">Viene fornita una nuova istanza per ogni richiesta di servizio e richiesta client.</span><span class="sxs-lookup"><span data-stu-id="57743-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="57743-296">Gli oggetti *con ambito* sono gli stessi all'interno di una richiesta client, ma variano nelle diverse richieste client.</span><span class="sxs-lookup"><span data-stu-id="57743-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="57743-297">Gli oggetti *Singleton* sono gli stessi per ogni `Operation` oggetto e `Startup.ConfigureServices`ogni richiesta, indipendentemente dal fatto che un'istanza venga fornita in .</span><span class="sxs-lookup"><span data-stu-id="57743-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="57743-298">Chiamare i servizi da main</span><span class="sxs-lookup"><span data-stu-id="57743-298">Call services from main</span></span>

<span data-ttu-id="57743-299">Creare un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>IServiceScope con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) per risolvere un servizio con ambito nell'ambito di applicazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="57743-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="57743-300">Questo approccio è utile per l'accesso a un servizio con ambito all'avvio e per l'esecuzione di attività di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="57743-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="57743-301">L'esempio seguente indica come ottenere un contesto per `MyScopedService` in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="57743-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="57743-302">Convalida dell'ambito</span><span class="sxs-lookup"><span data-stu-id="57743-302">Scope validation</span></span>

<span data-ttu-id="57743-303">Quando l'app è in esecuzione nell'ambiente di sviluppo e chiama CreateDefaultBuilder per compilare l'host, il provider di servizi predefinito esegue controlli per verificare che:When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span><span class="sxs-lookup"><span data-stu-id="57743-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="57743-304">I servizi con ambito non vengano risolti direttamente o indirettamente dal provider di servizi radice.</span><span class="sxs-lookup"><span data-stu-id="57743-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="57743-305">I servizi con ambito non vengano inseriti direttamente o indirettamente in singleton.</span><span class="sxs-lookup"><span data-stu-id="57743-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="57743-306">Il provider di servizi radice venga creato con la chiamata di <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="57743-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="57743-307">La durata del provider di servizi radice corrisponde alla durata dell'app/server quando il provider viene avviato con l'app e viene eliminato alla chiusura dell'app.</span><span class="sxs-lookup"><span data-stu-id="57743-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="57743-308">I servizi con ambito vengono eliminati dal contenitore che li ha creati.</span><span class="sxs-lookup"><span data-stu-id="57743-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="57743-309">Se un servizio con ambito viene creato nel contenitore radice, la durata del servizio viene di fatto convertita in singleton, perché il servizio viene eliminato solo dal contenitore radice alla chiusura dell'app/server.</span><span class="sxs-lookup"><span data-stu-id="57743-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="57743-310">La convalida degli ambiti servizio rileva queste situazioni nel contesto della chiamata di `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="57743-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="57743-311">Per altre informazioni, vedere <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="57743-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="57743-312">Servizi di richiesta</span><span class="sxs-lookup"><span data-stu-id="57743-312">Request Services</span></span>

<span data-ttu-id="57743-313">I servizi disponibili all'interno di una richiesta ASP.NET Core da `HttpContext` vengono esposti tramite la raccolta [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="57743-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="57743-314">I servizi di richiesta rappresentano i servizi configurati e richiesti come parte dell'app.</span><span class="sxs-lookup"><span data-stu-id="57743-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="57743-315">Quando gli oggetti specificano dipendenze, le dipendenze sono soddisfatte dai tipi individuati in `RequestServices`, non in `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="57743-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="57743-316">In generale, l'app non deve usare queste proprietà direttamente.</span><span class="sxs-lookup"><span data-stu-id="57743-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="57743-317">Richiedere invece i tipi necessari alle classi tramite i costruttori di classe e consentire al framework di inserire le dipendenze.</span><span class="sxs-lookup"><span data-stu-id="57743-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="57743-318">Si ottengono classi più facili da testare.</span><span class="sxs-lookup"><span data-stu-id="57743-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="57743-319">È consigliabile richiedere le dipendenze come parametri del costruttore per l'accesso alla raccolta `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="57743-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="57743-320">Progettare i servizi per l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="57743-320">Design services for dependency injection</span></span>

<span data-ttu-id="57743-321">Le procedure consigliate prevedono di:</span><span class="sxs-lookup"><span data-stu-id="57743-321">Best practices are to:</span></span>

* <span data-ttu-id="57743-322">Progettare i servizi per usare l'inserimento delle dipendenze per ottenere le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="57743-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="57743-323">Evitare classi e membri statici con stato.</span><span class="sxs-lookup"><span data-stu-id="57743-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="57743-324">Progetta invece le app per usare i servizi singleton, evitando di creare uno stato globale.</span><span class="sxs-lookup"><span data-stu-id="57743-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="57743-325">Evitare la creazione diretta di istanze delle classi dipendenti all'interno di servizi.</span><span class="sxs-lookup"><span data-stu-id="57743-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="57743-326">La creazione diretta di istanze associa il codice a una determinata implementazione.</span><span class="sxs-lookup"><span data-stu-id="57743-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="57743-327">Fare in modo che le classi siano piccole, con factoring corretto e facili da testare.</span><span class="sxs-lookup"><span data-stu-id="57743-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="57743-328">Se una classe sembra avere troppe dipendenze inserite, ciò significa in genere che la classe ha un numero eccessivo di responsabilità e sta violando il [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (principio di responsabilità singola).</span><span class="sxs-lookup"><span data-stu-id="57743-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="57743-329">Tentare di effettuare il refactoring della classe spostando alcune delle responsabilità in una nuova classe.</span><span class="sxs-lookup"><span data-stu-id="57743-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="57743-330">Tenere presente che le classi del modello di pagina Razor Pages e le classi del controller MVC devono essere incentrate sulle problematiche dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="57743-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="57743-331">Di conseguenza, le regole business e i dettagli di implementazione di accesso ai dati devono essere inseriti in classi appropriate per queste [problematiche separate](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="57743-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="57743-332">Eliminazione dei servizi</span><span class="sxs-lookup"><span data-stu-id="57743-332">Disposal of services</span></span>

<span data-ttu-id="57743-333">Il contenitore chiama <xref:System.IDisposable.Dispose*> per i tipi <xref:System.IDisposable> creati.</span><span class="sxs-lookup"><span data-stu-id="57743-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="57743-334">Se un'istanza viene aggiunta al contenitore dal codice utente, non viene eliminata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="57743-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="57743-335">Nell'esempio seguente i servizi vengono creati dal contenitore dei servizi ed eliminati automaticamente:In the following example, the services are created by the service container and disposed automatically:</span><span class="sxs-lookup"><span data-stu-id="57743-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="57743-336">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="57743-336">In the following example:</span></span>

* <span data-ttu-id="57743-337">Le istanze del servizio non vengono create dal contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="57743-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="57743-338">Le durate del servizio previste non sono note dal framework.</span><span class="sxs-lookup"><span data-stu-id="57743-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="57743-339">Il framework non elimina automaticamente i servizi.</span><span class="sxs-lookup"><span data-stu-id="57743-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="57743-340">Se i servizi non vengono eliminati in modo esplicito nel codice dello sviluppatore, vengono mantenuti fino all'arresto dell'app.</span><span class="sxs-lookup"><span data-stu-id="57743-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="57743-341">Per una descrizione delle opzioni di eliminazione dei servizi, vedere [Quattro modi per eliminare IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span><span class="sxs-lookup"><span data-stu-id="57743-341">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="57743-342">Sostituzione del contenitore di servizi predefinito</span><span class="sxs-lookup"><span data-stu-id="57743-342">Default service container replacement</span></span>

<span data-ttu-id="57743-343">Il contenitore di servizi predefinito è progettato per soddisfare le esigenze del framework e della maggior parte delle app consumer.</span><span class="sxs-lookup"><span data-stu-id="57743-343">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="57743-344">È consigliabile usare il contenitore predefinito, a meno che non sia necessaria una funzionalità specifica non supportata dal contenitore incorporato, ad esempio:We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span><span class="sxs-lookup"><span data-stu-id="57743-344">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="57743-345">Inserimento di proprietà</span><span class="sxs-lookup"><span data-stu-id="57743-345">Property injection</span></span>
* <span data-ttu-id="57743-346">Inserimento in base al nome</span><span class="sxs-lookup"><span data-stu-id="57743-346">Injection based on name</span></span>
* <span data-ttu-id="57743-347">Contenitori figlio</span><span class="sxs-lookup"><span data-stu-id="57743-347">Child containers</span></span>
* <span data-ttu-id="57743-348">Gestione della durata personalizzata</span><span class="sxs-lookup"><span data-stu-id="57743-348">Custom lifetime management</span></span>
* <span data-ttu-id="57743-349">Supporto di `Func<T>` per l'inizializzazione differita</span><span class="sxs-lookup"><span data-stu-id="57743-349">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="57743-350">Registrazione basata su convenzioni</span><span class="sxs-lookup"><span data-stu-id="57743-350">Convention-based registration</span></span>

<span data-ttu-id="57743-351">I seguenti contenitori di terze parti possono essere usati con le app ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="57743-351">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="57743-352">Autofac</span><span class="sxs-lookup"><span data-stu-id="57743-352">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="57743-353">DryIoc</span><span class="sxs-lookup"><span data-stu-id="57743-353">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="57743-354">Laura</span><span class="sxs-lookup"><span data-stu-id="57743-354">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="57743-355">LightInject</span><span class="sxs-lookup"><span data-stu-id="57743-355">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="57743-356">Lamar</span><span class="sxs-lookup"><span data-stu-id="57743-356">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="57743-357">Scatola di lavoro</span><span class="sxs-lookup"><span data-stu-id="57743-357">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="57743-358">Unity</span><span class="sxs-lookup"><span data-stu-id="57743-358">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="57743-359">Thread safety</span><span class="sxs-lookup"><span data-stu-id="57743-359">Thread safety</span></span>

<span data-ttu-id="57743-360">Creare servizi singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="57743-360">Create thread-safe singleton services.</span></span> <span data-ttu-id="57743-361">Se un servizio singleton ha una dipendenza in un servizio temporaneo, è necessario che anche il servizio temporaneo sia thread-safe, a seconda di come viene usato dal singleton.</span><span class="sxs-lookup"><span data-stu-id="57743-361">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="57743-362">Non è necessario che il metodo factory del singolo servizio, ad esempio il secondo argomento per [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), sia thread-safe.</span><span class="sxs-lookup"><span data-stu-id="57743-362">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="57743-363">Come nel caso di un costruttore di tipo (`static`), è garantito che venga chiamato una sola volta da un singolo thread.</span><span class="sxs-lookup"><span data-stu-id="57743-363">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="57743-364">Consigli</span><span class="sxs-lookup"><span data-stu-id="57743-364">Recommendations</span></span>

* <span data-ttu-id="57743-365">La risoluzione basata sui servizi `async/await` e `Task` non è supportata.</span><span class="sxs-lookup"><span data-stu-id="57743-365">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="57743-366">Dato che C# non supporta i costruttori asincroni, il modello consigliato consiste nell'usare i metodi asincroni dopo avere risolto in modo sincrono il servizio.</span><span class="sxs-lookup"><span data-stu-id="57743-366">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="57743-367">Evitare di archiviare i dati e la configurazione direttamente nel contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="57743-367">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="57743-368">Ad esempio, il carrello acquisti di un utente non dovrebbe in genere essere aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="57743-368">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="57743-369">La configurazione deve usare il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="57743-369">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="57743-370">Analogamente, evitare gli oggetti "contenitori di dati" che hanno la sola funzione di consentire l'accesso ad altri oggetti.</span><span class="sxs-lookup"><span data-stu-id="57743-370">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="57743-371">È preferibile richiedere l'elemento effettivo tramite inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="57743-371">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="57743-372">Evitare l'accesso statico ai servizi (ad esempio, la tipizzazione statica [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) da usare in altre posizioni).</span><span class="sxs-lookup"><span data-stu-id="57743-372">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="57743-373">Evitare di usare il *modello di localizzatore del servizio*.</span><span class="sxs-lookup"><span data-stu-id="57743-373">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="57743-374">Ad esempio, non richiamare <xref:System.IServiceProvider.GetService*> per ottenere un'istanza del servizio quando è invece possibile usare l'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="57743-374">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="57743-375">**Errato:**</span><span class="sxs-lookup"><span data-stu-id="57743-375">**Incorrect:**</span></span>

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

  <span data-ttu-id="57743-376">**Corretto**:</span><span class="sxs-lookup"><span data-stu-id="57743-376">**Correct**:</span></span>

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

* <span data-ttu-id="57743-377">Un'altra variazione del localizzatore del servizio da evitare è inserire una factory che risolve le dipendenze in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="57743-377">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="57743-378">Queste procedure combinano le strategie di [inversione del controllo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="57743-378">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="57743-379">Evitare l'accesso statico a `HttpContext` (ad esempio [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="57743-379">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="57743-380">È tuttavia possibile che in alcuni casi queste raccomandazioni debbano essere ignorate.</span><span class="sxs-lookup"><span data-stu-id="57743-380">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="57743-381">Le eccezioni sono rare e principalmente si tratta di casi speciali all'interno del framework stesso.</span><span class="sxs-lookup"><span data-stu-id="57743-381">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="57743-382">L'inserimento di dipendenze è un'*alternativa* ai modelli di accesso agli oggetti statici/globali.</span><span class="sxs-lookup"><span data-stu-id="57743-382">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="57743-383">Se l'inserimento di dipendenze viene usato con l'accesso agli oggetti statico i vantaggi non saranno evidenti.</span><span class="sxs-lookup"><span data-stu-id="57743-383">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="57743-384">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="57743-384">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="57743-385">Scrittura di codice pulito in ASP.NET Core con inserimento delle dipendenze (MSDN)</span><span class="sxs-lookup"><span data-stu-id="57743-385">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="57743-386">Explicit Dependencies Principle (Principio delle dipendenze esplicite)</span><span class="sxs-lookup"><span data-stu-id="57743-386">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="57743-387">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler) (Contenitori di inversione del controllo e modello di inserimento delle dipendenze)</span><span class="sxs-lookup"><span data-stu-id="57743-387">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="57743-388">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Come registrare un servizio con più interfacce in ASP.NET Core DI)</span><span class="sxs-lookup"><span data-stu-id="57743-388">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="57743-389">ASP.NET Core supporta il modello progettuale software per l'inserimento delle dipendenze, ovvero una tecnica per ottenere l'[inversione del controllo (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra classi e relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="57743-389">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="57743-390">Per altre informazioni specifiche per l'inserimento delle dipendenze all'interno di controller MVC, vedere <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="57743-390">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="57743-391">[Visualizzare o scaricare codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="57743-391">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="57743-392">Panoramica dell'inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="57743-392">Overview of dependency injection</span></span>

<span data-ttu-id="57743-393">Una *dipendenza* è qualsiasi oggetto richiesto da un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="57743-393">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="57743-394">Esaminare la classe `MyDependency` seguente con un metodo `WriteMessage` da cui dipendono altre classi in un'app:</span><span class="sxs-lookup"><span data-stu-id="57743-394">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="57743-395">È possibile creare un'istanza della classe `MyDependency` per rendere il metodo `WriteMessage`disponibile per una classe.</span><span class="sxs-lookup"><span data-stu-id="57743-395">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="57743-396">La classe `MyDependency` è una dipendenza della classe `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="57743-396">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="57743-397">La classe crea e dipende direttamente dall'istanza `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="57743-397">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="57743-398">Le dipendenze del codice (come nell'esempio precedente) sono problematiche e devono essere evitate per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="57743-398">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="57743-399">Per sostituire `MyDependency` con un'implementazione diversa, la classe deve essere modificata.</span><span class="sxs-lookup"><span data-stu-id="57743-399">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="57743-400">Se `MyDependency` ha dipendenze, devono essere configurate dalla classe.</span><span class="sxs-lookup"><span data-stu-id="57743-400">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="57743-401">In un progetto di grandi dimensioni con più classi che dipendono da `MyDependency`, il codice di configurazione diventa sparso in tutta l'app.</span><span class="sxs-lookup"><span data-stu-id="57743-401">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="57743-402">È difficile eseguire unit test di questa implementazione.</span><span class="sxs-lookup"><span data-stu-id="57743-402">This implementation is difficult to unit test.</span></span> <span data-ttu-id="57743-403">L'app dovrebbe usare una classe `MyDependency` fittizia o stub, ma ciò non è possibile con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="57743-403">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="57743-404">L'inserimento delle dipendenze consente di risolvere questi problemi tramite:</span><span class="sxs-lookup"><span data-stu-id="57743-404">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="57743-405">L'uso di un'interfaccia o di una classe di base per astrarre l'implementazione delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="57743-405">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="57743-406">La registrazione della dipendenza in un contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="57743-406">Registration of the dependency in a service container.</span></span> <span data-ttu-id="57743-407">ASP.NET Core offre il contenitore di servizi predefinito <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="57743-407">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="57743-408">I servizi vengono registrati nel metodo `Startup.ConfigureServices` dell'app.</span><span class="sxs-lookup"><span data-stu-id="57743-408">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="57743-409">L'*inserimento* del servizio nel costruttore della classe in cui viene usato.</span><span class="sxs-lookup"><span data-stu-id="57743-409">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="57743-410">Il framework si assume la responsabilità della creazione di un'istanza della dipendenza e della sua eliminazione quando non è più necessaria.</span><span class="sxs-lookup"><span data-stu-id="57743-410">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="57743-411">Nell'[app di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l'interfaccia `IMyDependency` definisce un metodo che il servizio fornisce all'app:</span><span class="sxs-lookup"><span data-stu-id="57743-411">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="57743-412">Questa interfaccia viene implementata da un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="57743-412">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="57743-413">`MyDependency` richiede <xref:Microsoft.Extensions.Logging.ILogger`1> nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="57743-413">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="57743-414">Non è insolito usare l'inserimento delle dipendenze in modo concatenato.</span><span class="sxs-lookup"><span data-stu-id="57743-414">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="57743-415">Ogni dipendenza richiesta richiede a sua volta le proprie dipendenze.</span><span class="sxs-lookup"><span data-stu-id="57743-415">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="57743-416">Il contenitore risolve le dipendenze nel grafico e restituisce il servizio completamente risolto.</span><span class="sxs-lookup"><span data-stu-id="57743-416">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="57743-417">Il set di dipendenze che devono essere risolte viene generalmente chiamato *albero delle dipendenze* o *grafico dipendenze* o *grafico degli oggetti*.</span><span class="sxs-lookup"><span data-stu-id="57743-417">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="57743-418">`IMyDependency` e `ILogger<TCategoryName>` devono essere registrati nel contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="57743-418">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="57743-419">`IMyDependency` viene registrato in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="57743-419">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="57743-420">`ILogger<TCategoryName>`viene registrato dall'infrastruttura di astrazione di registrazione, pertanto è un [servizio fornito dal framework](#framework-provided-services) registrato per impostazione predefinita dal framework.</span><span class="sxs-lookup"><span data-stu-id="57743-420">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="57743-421">Il contenitore risolve `ILogger<TCategoryName>` avvalendosi di [tipi aperti (generici)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando l'esigenza di registrare ogni singolo [tipo costruito (generico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="57743-421">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="57743-422">Nell'app di esempio, il servizio `IMyDependency` viene registrato con il tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="57743-422">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="57743-423">La registrazione definisce come ambito della durata di servizio la durata di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="57743-423">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="57743-424">Le [durate dei servizi](#service-lifetimes) sono descritte più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="57743-424">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="57743-425">Ogni metodo di estensione `services.Add{SERVICE_NAME}` aggiunge (e potenzialmente configura) i servizi.</span><span class="sxs-lookup"><span data-stu-id="57743-425">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="57743-426">Ad esempio, `services.AddMvc()` aggiunge i servizi richiesti da Razor Pages e MVC.</span><span class="sxs-lookup"><span data-stu-id="57743-426">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="57743-427">È consigliabile che le app seguano questa convenzione.</span><span class="sxs-lookup"><span data-stu-id="57743-427">We recommended that apps follow this convention.</span></span> <span data-ttu-id="57743-428">Inserire i metodi di estensione nello spazio dei nomi [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) per incapsulare i gruppi di registrazioni di servizio.</span><span class="sxs-lookup"><span data-stu-id="57743-428">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="57743-429">Se il costruttore del servizio richiede un [tipo incorporato](/dotnet/csharp/language-reference/keywords/built-in-types-table), ad esempio `string`, è possibile inserirlo usando la [configurazione](xref:fundamentals/configuration/index) o il [modello di opzioni](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="57743-429">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="57743-430">È richiesta un'istanza del servizio tramite il costruttore di una classe in cui il servizio viene usato e assegnato a un campo privato.</span><span class="sxs-lookup"><span data-stu-id="57743-430">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="57743-431">Il campo viene usato per accedere al servizio in base alle esigenze per l'intera classe.</span><span class="sxs-lookup"><span data-stu-id="57743-431">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="57743-432">Nell'app di esempio, viene richiesta l'istanza `IMyDependency` usata per chiamare il metodo `WriteMessage` del servizio:</span><span class="sxs-lookup"><span data-stu-id="57743-432">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="57743-433">Servizi inseriti nell'avvio</span><span class="sxs-lookup"><span data-stu-id="57743-433">Services injected into Startup</span></span>

<span data-ttu-id="57743-434">Solo i seguenti tipi di servizio `Startup` possono essere inseriti<xref:Microsoft.Extensions.Hosting.IHostBuilder>nel costruttore quando si utilizza l'host generico ( ):</span><span class="sxs-lookup"><span data-stu-id="57743-434">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="57743-435">I servizi possono `Startup.Configure`essere iniettati in:</span><span class="sxs-lookup"><span data-stu-id="57743-435">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="57743-436">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="57743-436">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="57743-437">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="57743-437">Framework-provided services</span></span>

<span data-ttu-id="57743-438">Il `Startup.ConfigureServices` metodo è responsabile della definizione dei servizi utilizzati dall'app, incluse le funzionalità della piattaforma, ad esempio Entity Framework Core e ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="57743-438">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="57743-439">Inizialmente, `IServiceCollection` i `ConfigureServices` servizi forniti a hanno definito dal framework a seconda di [come è stato configurato l'host.](xref:fundamentals/index#host)</span><span class="sxs-lookup"><span data-stu-id="57743-439">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="57743-440">Non è raro che un'app basata su un modello ASP.NET Core disponga di centinaia di servizi registrati dal framework.</span><span class="sxs-lookup"><span data-stu-id="57743-440">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="57743-441">Nella tabella seguente è elencato un piccolo esempio di servizi registrati nel framework.</span><span class="sxs-lookup"><span data-stu-id="57743-441">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="57743-442">Tipo di servizio</span><span class="sxs-lookup"><span data-stu-id="57743-442">Service Type</span></span> | <span data-ttu-id="57743-443">Durata</span><span class="sxs-lookup"><span data-stu-id="57743-443">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="57743-444">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="57743-444">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="57743-445">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-445">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="57743-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-446">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="57743-447">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-447">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="57743-448">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="57743-448">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="57743-449">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-449">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="57743-450">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="57743-450">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="57743-451">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-451">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="57743-452">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-452">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="57743-453">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-453">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="57743-454">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="57743-454">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="57743-455">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-455">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="57743-456">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-456">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="57743-457">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-457">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="57743-458">Registrare servizi aggiuntivi con metodi di estensioneRegister additional services with extension methods</span><span class="sxs-lookup"><span data-stu-id="57743-458">Register additional services with extension methods</span></span>

<span data-ttu-id="57743-459">Quando è disponibile un metodo di estensione della raccolta di servizi per la registrazione di un servizio (e dei servizi dipendenti, se necessario), la convenzione consiste nell'usare un singolo metodo di estensione `Add{SERVICE_NAME}` per registrare tutti i servizi richiesti da tale servizio.</span><span class="sxs-lookup"><span data-stu-id="57743-459">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="57743-460">Il codice seguente è un esempio di come aggiungere servizi aggiuntivi al contenitore <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>usando i metodi di estensione [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e :</span><span class="sxs-lookup"><span data-stu-id="57743-460">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="57743-461">Per altre informazioni, vedere la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> nella documentazione delle API.</span><span class="sxs-lookup"><span data-stu-id="57743-461">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="57743-462">Durate dei servizi</span><span class="sxs-lookup"><span data-stu-id="57743-462">Service lifetimes</span></span>

<span data-ttu-id="57743-463">Scegliere una durata appropriata per ogni servizio registrato.</span><span class="sxs-lookup"><span data-stu-id="57743-463">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="57743-464">I servizi ASP.NET Core possono essere configurati con le impostazioni di durata seguenti:</span><span class="sxs-lookup"><span data-stu-id="57743-464">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="57743-465">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="57743-465">Transient</span></span>

<span data-ttu-id="57743-466">I servizi con durata temporanea (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) vengono creati ogni volta che vengono richiesti dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="57743-466">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="57743-467">Questa impostazione di durata è consigliata per i servizi semplici senza stati.</span><span class="sxs-lookup"><span data-stu-id="57743-467">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="57743-468">Con ambito</span><span class="sxs-lookup"><span data-stu-id="57743-468">Scoped</span></span>

<span data-ttu-id="57743-469">I servizi con durata con ambito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) vengono creati una volta per ogni richiesta client (connessione).</span><span class="sxs-lookup"><span data-stu-id="57743-469">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="57743-470">Quando si usa un servizio con ambito in un middleware, inserire il servizio nel metodo `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="57743-470">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="57743-471">Evitare l'inserimento tramite il costruttore, che impone al servizio il comportamento singleton.</span><span class="sxs-lookup"><span data-stu-id="57743-471">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="57743-472">Per altre informazioni, vedere <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="57743-472">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="57743-473">Singleton</span><span class="sxs-lookup"><span data-stu-id="57743-473">Singleton</span></span>

<span data-ttu-id="57743-474">I servizi con durata singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) vengono creati la prima volta che vengono richiesti (o quando si esegue `Startup.ConfigureServices` e viene specificata un'istanza con la registrazione del servizio).</span><span class="sxs-lookup"><span data-stu-id="57743-474">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="57743-475">Tutte le richieste successive usano la stessa istanza.</span><span class="sxs-lookup"><span data-stu-id="57743-475">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="57743-476">Se l'app richiede un comportamento singleton, è consigliabile consentire al contenitore del servizio di gestire la durata del servizio.</span><span class="sxs-lookup"><span data-stu-id="57743-476">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="57743-477">Non implementare lo schema progettuale singleton e fornire codice utente per gestire la durata dell'oggetto nella classe.</span><span class="sxs-lookup"><span data-stu-id="57743-477">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="57743-478">Può essere pericoloso risolvere un servizio con ambito da un singleton.</span><span class="sxs-lookup"><span data-stu-id="57743-478">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="57743-479">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="57743-479">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="57743-480">Metodi di registrazione del servizio</span><span class="sxs-lookup"><span data-stu-id="57743-480">Service registration methods</span></span>

<span data-ttu-id="57743-481">I metodi di estensione per la registrazione del servizio offrono overload utili in scenari specifici.</span><span class="sxs-lookup"><span data-stu-id="57743-481">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="57743-482">Metodo</span><span class="sxs-lookup"><span data-stu-id="57743-482">Method</span></span> | <span data-ttu-id="57743-483">Automatico</span><span class="sxs-lookup"><span data-stu-id="57743-483">Automatic</span></span><br><span data-ttu-id="57743-484">object</span><span class="sxs-lookup"><span data-stu-id="57743-484">object</span></span><br><span data-ttu-id="57743-485">eliminazione</span><span class="sxs-lookup"><span data-stu-id="57743-485">disposal</span></span> | <span data-ttu-id="57743-486">Multipli</span><span class="sxs-lookup"><span data-stu-id="57743-486">Multiple</span></span><br><span data-ttu-id="57743-487">implementazioni</span><span class="sxs-lookup"><span data-stu-id="57743-487">implementations</span></span> | <span data-ttu-id="57743-488">Pass args</span><span class="sxs-lookup"><span data-stu-id="57743-488">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="57743-489">Esempio:</span><span class="sxs-lookup"><span data-stu-id="57743-489">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="57743-490">Sì</span><span class="sxs-lookup"><span data-stu-id="57743-490">Yes</span></span> | <span data-ttu-id="57743-491">Sì</span><span class="sxs-lookup"><span data-stu-id="57743-491">Yes</span></span> | <span data-ttu-id="57743-492">No</span><span class="sxs-lookup"><span data-stu-id="57743-492">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="57743-493">Esempi:</span><span class="sxs-lookup"><span data-stu-id="57743-493">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="57743-494">Sì</span><span class="sxs-lookup"><span data-stu-id="57743-494">Yes</span></span> | <span data-ttu-id="57743-495">Sì</span><span class="sxs-lookup"><span data-stu-id="57743-495">Yes</span></span> | <span data-ttu-id="57743-496">Sì</span><span class="sxs-lookup"><span data-stu-id="57743-496">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="57743-497">Esempio:</span><span class="sxs-lookup"><span data-stu-id="57743-497">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="57743-498">Sì</span><span class="sxs-lookup"><span data-stu-id="57743-498">Yes</span></span> | <span data-ttu-id="57743-499">No</span><span class="sxs-lookup"><span data-stu-id="57743-499">No</span></span> | <span data-ttu-id="57743-500">No</span><span class="sxs-lookup"><span data-stu-id="57743-500">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="57743-501">Esempi:</span><span class="sxs-lookup"><span data-stu-id="57743-501">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="57743-502">No</span><span class="sxs-lookup"><span data-stu-id="57743-502">No</span></span> | <span data-ttu-id="57743-503">Sì</span><span class="sxs-lookup"><span data-stu-id="57743-503">Yes</span></span> | <span data-ttu-id="57743-504">Sì</span><span class="sxs-lookup"><span data-stu-id="57743-504">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="57743-505">Esempi:</span><span class="sxs-lookup"><span data-stu-id="57743-505">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="57743-506">No</span><span class="sxs-lookup"><span data-stu-id="57743-506">No</span></span> | <span data-ttu-id="57743-507">No</span><span class="sxs-lookup"><span data-stu-id="57743-507">No</span></span> | <span data-ttu-id="57743-508">Sì</span><span class="sxs-lookup"><span data-stu-id="57743-508">Yes</span></span> |

<span data-ttu-id="57743-509">Per ulteriori informazioni sull'eliminazione dei tipi, vedere la sezione [Eliminazione dei servizi](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="57743-509">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="57743-510">Uno scenario comune per più implementazioni è costituito dai [tipi di simulazioni per i test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="57743-510">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="57743-511">I metodi `TryAdd{LIFETIME}` registrano il servizio solo se esiste già un'implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="57743-511">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="57743-512">Nell'esempio seguente, la prima riga registra `MyDependency` per `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="57743-512">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="57743-513">La seconda riga non ha alcun effetto perché `IMyDependency` dispone già di un'implementazione registrata:</span><span class="sxs-lookup"><span data-stu-id="57743-513">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="57743-514">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="57743-514">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="57743-515">I metodi [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrano il servizio solo se non esiste già un'implementazione *dello stesso tipo*.</span><span class="sxs-lookup"><span data-stu-id="57743-515">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="57743-516">Più servizi vengono risolti tramite `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="57743-516">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="57743-517">Durante la registrazione di servizi, lo sviluppatore desidera aggiungere un'istanza solo se non ne è già stata aggiunta una dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="57743-517">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="57743-518">In genere, questo metodo viene utilizzato dagli autori di librerie per evitare di registrare due copie di un'istanza nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="57743-518">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="57743-519">Nell'esempio seguente, la prima riga registra `MyDep` per `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="57743-519">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="57743-520">La seconda riga registra `MyDep` per `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="57743-520">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="57743-521">La seconda riga non ha alcun effetto perché `IMyDep1` dispone già di un'implementazione registrata di `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="57743-521">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="57743-522">Comportamento dell'inserimento del costruttore</span><span class="sxs-lookup"><span data-stu-id="57743-522">Constructor injection behavior</span></span>

<span data-ttu-id="57743-523">I servizi possono essere risolti con due meccanismi:</span><span class="sxs-lookup"><span data-stu-id="57743-523">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="57743-524"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Consente la creazione di oggetti senza registrazione del servizio nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="57743-524"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="57743-525">`ActivatorUtilities` viene usato con astrazioni esposte all'utente, ad esempio helper tag, controller MVC e strumenti di associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="57743-525">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="57743-526">I costruttori possono accettare argomenti non forniti tramite l'inserimento di dipendenze, ma gli argomenti devono assegnare valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="57743-526">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="57743-527">Quando i servizi vengono risolti da `IServiceProvider` o `ActivatorUtilities`, l'inserimento del costruttore richiede un costruttore *pubblico*.</span><span class="sxs-lookup"><span data-stu-id="57743-527">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="57743-528">Quando i servizi vengono risolti da `ActivatorUtilities`, l'inserimento del costruttore richiede l'esistenza di un solo costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="57743-528">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="57743-529">Sebbene siano supportati gli overload dei costruttori, può essere presente un solo overload i cui argomenti possono essere tutti specificati tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="57743-529">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="57743-530">Contesti di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="57743-530">Entity Framework contexts</span></span>

<span data-ttu-id="57743-531">I contesti di Entity Framework vengono in genere aggiunti al contenitore di servizi usando la [durata con ambito](#service-lifetimes) perché le operazioni di database delle app Web hanno di solito come ambito la richiesta client.</span><span class="sxs-lookup"><span data-stu-id="57743-531">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="57743-532">La durata predefinita è con ambito se non viene specificata una durata con un overload [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) quando si registra il contesto del database.</span><span class="sxs-lookup"><span data-stu-id="57743-532">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="57743-533">I servizi con una durata specificata non devono usare un contesto di database con una durata più breve rispetto al servizio.</span><span class="sxs-lookup"><span data-stu-id="57743-533">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="57743-534">Opzioni di durata e di registrazione</span><span class="sxs-lookup"><span data-stu-id="57743-534">Lifetime and registration options</span></span>

<span data-ttu-id="57743-535">Per illustrare la differenza tra le opzioni di durata e registrazione, si considerino le interfacce seguenti che rappresentano le attività come un'operazione con un identificatore univoco, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="57743-535">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="57743-536">A seconda del modo in cui la durata di un servizio operativo viene configurata per le interfacce seguenti, il contenitore fornisce la stessa istanza o un'istanza diversa del servizio quando richiesto da una classe:</span><span class="sxs-lookup"><span data-stu-id="57743-536">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="57743-537">Le interfacce vengono implementate nella classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="57743-537">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="57743-538">Il costruttore `Operation` genera un GUID se non ne viene fornito uno:</span><span class="sxs-lookup"><span data-stu-id="57743-538">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="57743-539">Viene registrato un `OperationService` che dipende da ognuno degli altri tipi `Operation`.</span><span class="sxs-lookup"><span data-stu-id="57743-539">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="57743-540">Quando viene richiesto `OperationService` tramite l'inserimento delle dipendenze, riceve una nuova istanza di ogni servizio o un'istanza esistente in base alla durata del servizio dipendente.</span><span class="sxs-lookup"><span data-stu-id="57743-540">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="57743-541">Se i servizi temporanei vengono creati quando vengono richiesti dal contenitore, `OperationId` del servizio `IOperationTransient` è diverso da `OperationId` di `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="57743-541">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="57743-542">`OperationService` riceve una nuova istanza della classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="57743-542">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="57743-543">La nuova istanza restituisce un `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="57743-543">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="57743-544">Se i servizi con ambito vengono creati per ogni richiesta client, `OperationId` del servizio `IOperationScoped` corrisponde a `OperationService` all'interno di una richiesta client.</span><span class="sxs-lookup"><span data-stu-id="57743-544">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="57743-545">In tutte le richieste client entrambi i servizi condividono un valore `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="57743-545">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="57743-546">Se i servizi singleton e con istanza singleton vengono creati una sola volta e usati per tutte le richieste client e tutti i servizi, `OperationId` rimane costante in tutte le richieste di servizio.</span><span class="sxs-lookup"><span data-stu-id="57743-546">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="57743-547">In `Startup.ConfigureServices`, ogni tipo viene aggiunto al contenitore in base alla durata denominata:</span><span class="sxs-lookup"><span data-stu-id="57743-547">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="57743-548">Il servizio `IOperationSingletonInstance` usa un'istanza specifica con un ID noto di `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="57743-548">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="57743-549">È chiaro quando questo tipo è in uso (il relativo GUID è composto da tutti zero).</span><span class="sxs-lookup"><span data-stu-id="57743-549">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="57743-550">L'app di esempio dimostra le durate degli oggetti all'interno e tra le singole richieste.</span><span class="sxs-lookup"><span data-stu-id="57743-550">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="57743-551">L'`IndexModel` dell'app di esempio richiede ogni genere di tipo `IOperation` e `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="57743-551">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="57743-552">La pagina visualizza quindi tutti i valori `OperationId` della classe del modello di pagina e del servizio tramite assegnazioni di proprietà:</span><span class="sxs-lookup"><span data-stu-id="57743-552">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="57743-553">L'output seguente mostra i risultati delle due richieste:</span><span class="sxs-lookup"><span data-stu-id="57743-553">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="57743-554">**Prima richiesta:**</span><span class="sxs-lookup"><span data-stu-id="57743-554">**First request:**</span></span>

<span data-ttu-id="57743-555">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="57743-555">Controller operations:</span></span>

<span data-ttu-id="57743-556">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="57743-556">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="57743-557">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="57743-557">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="57743-558">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="57743-558">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="57743-559">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="57743-559">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="57743-560">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="57743-560">`OperationService` operations:</span></span>

<span data-ttu-id="57743-561">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="57743-561">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="57743-562">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="57743-562">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="57743-563">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="57743-563">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="57743-564">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="57743-564">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="57743-565">**Seconda richiesta:**</span><span class="sxs-lookup"><span data-stu-id="57743-565">**Second request:**</span></span>

<span data-ttu-id="57743-566">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="57743-566">Controller operations:</span></span>

<span data-ttu-id="57743-567">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="57743-567">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="57743-568">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="57743-568">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="57743-569">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="57743-569">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="57743-570">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="57743-570">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="57743-571">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="57743-571">`OperationService` operations:</span></span>

<span data-ttu-id="57743-572">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="57743-572">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="57743-573">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="57743-573">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="57743-574">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="57743-574">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="57743-575">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="57743-575">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="57743-576">Osservare i valori `OperationId` che variano all'interno della richiesta e da una richiesta e l'altra:</span><span class="sxs-lookup"><span data-stu-id="57743-576">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="57743-577">Gli oggetti *temporanei* sono sempre diversi.</span><span class="sxs-lookup"><span data-stu-id="57743-577">*Transient* objects are always different.</span></span> <span data-ttu-id="57743-578">Il valore `OperationId` temporaneo per la prima e la seconda richiesta client è diverso per entrambe le operazioni `OperationService` e in tutte le richieste client.</span><span class="sxs-lookup"><span data-stu-id="57743-578">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="57743-579">Viene fornita una nuova istanza per ogni richiesta di servizio e richiesta client.</span><span class="sxs-lookup"><span data-stu-id="57743-579">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="57743-580">Gli oggetti *con ambito* sono gli stessi all'interno di una richiesta client, ma variano nelle diverse richieste client.</span><span class="sxs-lookup"><span data-stu-id="57743-580">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="57743-581">Gli oggetti *Singleton* sono gli stessi per ogni `Operation` oggetto e `Startup.ConfigureServices`ogni richiesta, indipendentemente dal fatto che un'istanza venga fornita in .</span><span class="sxs-lookup"><span data-stu-id="57743-581">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="57743-582">Chiamare i servizi da main</span><span class="sxs-lookup"><span data-stu-id="57743-582">Call services from main</span></span>

<span data-ttu-id="57743-583">Creare un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>IServiceScope con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) per risolvere un servizio con ambito nell'ambito di applicazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="57743-583">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="57743-584">Questo approccio è utile per l'accesso a un servizio con ambito all'avvio e per l'esecuzione di attività di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="57743-584">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="57743-585">L'esempio seguente indica come ottenere un contesto per `MyScopedService` in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="57743-585">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="57743-586">Convalida dell'ambito</span><span class="sxs-lookup"><span data-stu-id="57743-586">Scope validation</span></span>

<span data-ttu-id="57743-587">Quando l'app viene eseguita nell'ambiente di sviluppo, il provider di servizi predefinito esegue controlli per verificare che:</span><span class="sxs-lookup"><span data-stu-id="57743-587">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="57743-588">I servizi con ambito non vengano risolti direttamente o indirettamente dal provider di servizi radice.</span><span class="sxs-lookup"><span data-stu-id="57743-588">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="57743-589">I servizi con ambito non vengano inseriti direttamente o indirettamente in singleton.</span><span class="sxs-lookup"><span data-stu-id="57743-589">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="57743-590">Il provider di servizi radice venga creato con la chiamata di <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="57743-590">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="57743-591">La durata del provider di servizi radice corrisponde alla durata dell'app/server quando il provider viene avviato con l'app e viene eliminato alla chiusura dell'app.</span><span class="sxs-lookup"><span data-stu-id="57743-591">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="57743-592">I servizi con ambito vengono eliminati dal contenitore che li ha creati.</span><span class="sxs-lookup"><span data-stu-id="57743-592">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="57743-593">Se un servizio con ambito viene creato nel contenitore radice, la durata del servizio viene di fatto convertita in singleton, perché il servizio viene eliminato solo dal contenitore radice alla chiusura dell'app/server.</span><span class="sxs-lookup"><span data-stu-id="57743-593">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="57743-594">La convalida degli ambiti servizio rileva queste situazioni nel contesto della chiamata di `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="57743-594">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="57743-595">Per altre informazioni, vedere <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="57743-595">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="57743-596">Servizi di richiesta</span><span class="sxs-lookup"><span data-stu-id="57743-596">Request Services</span></span>

<span data-ttu-id="57743-597">I servizi disponibili all'interno di una richiesta ASP.NET Core da `HttpContext` vengono esposti tramite la raccolta [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="57743-597">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="57743-598">I servizi di richiesta rappresentano i servizi configurati e richiesti come parte dell'app.</span><span class="sxs-lookup"><span data-stu-id="57743-598">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="57743-599">Quando gli oggetti specificano dipendenze, le dipendenze sono soddisfatte dai tipi individuati in `RequestServices`, non in `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="57743-599">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="57743-600">In generale, l'app non deve usare queste proprietà direttamente.</span><span class="sxs-lookup"><span data-stu-id="57743-600">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="57743-601">Richiedere invece i tipi richiesti dalle classi tramite i costruttori di classi e consentire al framework di inserire le dipendenze.</span><span class="sxs-lookup"><span data-stu-id="57743-601">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="57743-602">Si ottengono classi più facili da testare.</span><span class="sxs-lookup"><span data-stu-id="57743-602">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="57743-603">È consigliabile richiedere le dipendenze come parametri del costruttore per l'accesso alla raccolta `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="57743-603">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="57743-604">Progettare i servizi per l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="57743-604">Design services for dependency injection</span></span>

<span data-ttu-id="57743-605">Le procedure consigliate prevedono di:</span><span class="sxs-lookup"><span data-stu-id="57743-605">Best practices are to:</span></span>

* <span data-ttu-id="57743-606">Progettare i servizi per usare l'inserimento delle dipendenze per ottenere le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="57743-606">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="57743-607">Evitare classi e membri statici con stato.</span><span class="sxs-lookup"><span data-stu-id="57743-607">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="57743-608">Progetta invece le app per usare i servizi singleton, evitando di creare uno stato globale.</span><span class="sxs-lookup"><span data-stu-id="57743-608">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="57743-609">Evitare la creazione diretta di istanze delle classi dipendenti all'interno di servizi.</span><span class="sxs-lookup"><span data-stu-id="57743-609">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="57743-610">La creazione diretta di istanze associa il codice a una determinata implementazione.</span><span class="sxs-lookup"><span data-stu-id="57743-610">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="57743-611">Fare in modo che le classi siano piccole, con factoring corretto e facili da testare.</span><span class="sxs-lookup"><span data-stu-id="57743-611">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="57743-612">Se una classe sembra avere troppe dipendenze inserite, ciò significa in genere che la classe ha un numero eccessivo di responsabilità e sta violando il [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (principio di responsabilità singola).</span><span class="sxs-lookup"><span data-stu-id="57743-612">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="57743-613">Tentare di effettuare il refactoring della classe spostando alcune delle responsabilità in una nuova classe.</span><span class="sxs-lookup"><span data-stu-id="57743-613">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="57743-614">Tenere presente che le classi del modello di pagina Razor Pages e le classi del controller MVC devono essere incentrate sulle problematiche dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="57743-614">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="57743-615">Di conseguenza, le regole business e i dettagli di implementazione di accesso ai dati devono essere inseriti in classi appropriate per queste [problematiche separate](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="57743-615">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="57743-616">Eliminazione dei servizi</span><span class="sxs-lookup"><span data-stu-id="57743-616">Disposal of services</span></span>

<span data-ttu-id="57743-617">Il contenitore chiama <xref:System.IDisposable.Dispose*> per i tipi <xref:System.IDisposable> creati.</span><span class="sxs-lookup"><span data-stu-id="57743-617">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="57743-618">Se un'istanza viene aggiunta al contenitore dal codice utente, non viene eliminata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="57743-618">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="57743-619">Nell'esempio seguente i servizi vengono creati dal contenitore dei servizi ed eliminati automaticamente:In the following example, the services are created by the service container and disposed automatically:</span><span class="sxs-lookup"><span data-stu-id="57743-619">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="57743-620">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="57743-620">In the following example:</span></span>

* <span data-ttu-id="57743-621">Le istanze del servizio non vengono create dal contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="57743-621">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="57743-622">Le durate del servizio previste non sono note dal framework.</span><span class="sxs-lookup"><span data-stu-id="57743-622">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="57743-623">Il framework non elimina automaticamente i servizi.</span><span class="sxs-lookup"><span data-stu-id="57743-623">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="57743-624">Se i servizi non vengono eliminati in modo esplicito nel codice dello sviluppatore, vengono mantenuti fino all'arresto dell'app.</span><span class="sxs-lookup"><span data-stu-id="57743-624">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="57743-625">Sostituzione del contenitore di servizi predefinito</span><span class="sxs-lookup"><span data-stu-id="57743-625">Default service container replacement</span></span>

<span data-ttu-id="57743-626">Il contenitore di servizi predefinito è progettato per soddisfare le esigenze del framework e della maggior parte delle app consumer.</span><span class="sxs-lookup"><span data-stu-id="57743-626">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="57743-627">È consigliabile usare il contenitore predefinito, a meno che non sia necessaria una funzionalità specifica non supportata dal contenitore incorporato, ad esempio:We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span><span class="sxs-lookup"><span data-stu-id="57743-627">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="57743-628">Inserimento di proprietà</span><span class="sxs-lookup"><span data-stu-id="57743-628">Property injection</span></span>
* <span data-ttu-id="57743-629">Inserimento in base al nome</span><span class="sxs-lookup"><span data-stu-id="57743-629">Injection based on name</span></span>
* <span data-ttu-id="57743-630">Contenitori figlio</span><span class="sxs-lookup"><span data-stu-id="57743-630">Child containers</span></span>
* <span data-ttu-id="57743-631">Gestione della durata personalizzata</span><span class="sxs-lookup"><span data-stu-id="57743-631">Custom lifetime management</span></span>
* <span data-ttu-id="57743-632">Supporto di `Func<T>` per l'inizializzazione differita</span><span class="sxs-lookup"><span data-stu-id="57743-632">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="57743-633">Registrazione basata su convenzioni</span><span class="sxs-lookup"><span data-stu-id="57743-633">Convention-based registration</span></span>

<span data-ttu-id="57743-634">I seguenti contenitori di terze parti possono essere usati con le app ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="57743-634">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="57743-635">Autofac</span><span class="sxs-lookup"><span data-stu-id="57743-635">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="57743-636">DryIoc</span><span class="sxs-lookup"><span data-stu-id="57743-636">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="57743-637">Laura</span><span class="sxs-lookup"><span data-stu-id="57743-637">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="57743-638">LightInject</span><span class="sxs-lookup"><span data-stu-id="57743-638">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="57743-639">Lamar</span><span class="sxs-lookup"><span data-stu-id="57743-639">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="57743-640">Scatola di lavoro</span><span class="sxs-lookup"><span data-stu-id="57743-640">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="57743-641">Unity</span><span class="sxs-lookup"><span data-stu-id="57743-641">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="57743-642">Thread safety</span><span class="sxs-lookup"><span data-stu-id="57743-642">Thread safety</span></span>

<span data-ttu-id="57743-643">Creare servizi singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="57743-643">Create thread-safe singleton services.</span></span> <span data-ttu-id="57743-644">Se un servizio singleton ha una dipendenza in un servizio temporaneo, è necessario che anche il servizio temporaneo sia thread-safe, a seconda di come viene usato dal singleton.</span><span class="sxs-lookup"><span data-stu-id="57743-644">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="57743-645">Non è necessario che il metodo factory del singolo servizio, ad esempio il secondo argomento per [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), sia thread-safe.</span><span class="sxs-lookup"><span data-stu-id="57743-645">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="57743-646">Come nel caso di un costruttore di tipo (`static`), è garantito che venga chiamato una sola volta da un singolo thread.</span><span class="sxs-lookup"><span data-stu-id="57743-646">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="57743-647">Consigli</span><span class="sxs-lookup"><span data-stu-id="57743-647">Recommendations</span></span>

* <span data-ttu-id="57743-648">La risoluzione basata sui servizi `async/await` e `Task` non è supportata.</span><span class="sxs-lookup"><span data-stu-id="57743-648">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="57743-649">Dato che C# non supporta i costruttori asincroni, il modello consigliato consiste nell'usare i metodi asincroni dopo avere risolto in modo sincrono il servizio.</span><span class="sxs-lookup"><span data-stu-id="57743-649">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="57743-650">Evitare di archiviare i dati e la configurazione direttamente nel contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="57743-650">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="57743-651">Ad esempio, il carrello acquisti di un utente non dovrebbe in genere essere aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="57743-651">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="57743-652">La configurazione deve usare il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="57743-652">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="57743-653">Analogamente, evitare gli oggetti "contenitori di dati" che hanno la sola funzione di consentire l'accesso ad altri oggetti.</span><span class="sxs-lookup"><span data-stu-id="57743-653">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="57743-654">È preferibile richiedere l'elemento effettivo tramite inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="57743-654">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="57743-655">Evitare l'accesso statico ai servizi (ad esempio, la tipizzazione statica [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) da usare in altre posizioni).</span><span class="sxs-lookup"><span data-stu-id="57743-655">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="57743-656">Evitare di utilizzare il modello di *localizzatore*del servizio , che combina le strategie [di inversione del controllo.](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)</span><span class="sxs-lookup"><span data-stu-id="57743-656">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="57743-657">Non richiamare <xref:System.IServiceProvider.GetService*> per ottenere un'istanza del servizio quando è possibile usare DI invece:Do't invoke to obtain a service instance when you can use DI instead:</span><span class="sxs-lookup"><span data-stu-id="57743-657">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="57743-658">**Errato:**</span><span class="sxs-lookup"><span data-stu-id="57743-658">**Incorrect:**</span></span>

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

    <span data-ttu-id="57743-659">**Corretto**:</span><span class="sxs-lookup"><span data-stu-id="57743-659">**Correct**:</span></span>

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

  * <span data-ttu-id="57743-660">Evitare di inserire una factory che <xref:System.IServiceProvider.GetService*>risolve le dipendenze in fase di esecuzione utilizzando .</span><span class="sxs-lookup"><span data-stu-id="57743-660">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="57743-661">Evitare l'accesso statico a `HttpContext` (ad esempio [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="57743-661">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="57743-662">È tuttavia possibile che in alcuni casi queste raccomandazioni debbano essere ignorate.</span><span class="sxs-lookup"><span data-stu-id="57743-662">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="57743-663">Le eccezioni sono rare e principalmente si tratta di casi speciali all'interno del framework stesso.</span><span class="sxs-lookup"><span data-stu-id="57743-663">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="57743-664">L'inserimento di dipendenze è un'*alternativa* ai modelli di accesso agli oggetti statici/globali.</span><span class="sxs-lookup"><span data-stu-id="57743-664">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="57743-665">Se l'inserimento di dipendenze viene usato con l'accesso agli oggetti statico i vantaggi non saranno evidenti.</span><span class="sxs-lookup"><span data-stu-id="57743-665">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="57743-666">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="57743-666">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="57743-667">Scrittura di codice pulito in ASP.NET Core con inserimento delle dipendenze (MSDN)</span><span class="sxs-lookup"><span data-stu-id="57743-667">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="57743-668">Explicit Dependencies Principle (Principio delle dipendenze esplicite)</span><span class="sxs-lookup"><span data-stu-id="57743-668">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="57743-669">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler) (Contenitori di inversione del controllo e modello di inserimento delle dipendenze)</span><span class="sxs-lookup"><span data-stu-id="57743-669">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="57743-670">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Come registrare un servizio con più interfacce in ASP.NET Core DI)</span><span class="sxs-lookup"><span data-stu-id="57743-670">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
