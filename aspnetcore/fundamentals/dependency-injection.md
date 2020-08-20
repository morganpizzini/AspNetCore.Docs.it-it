---
title: Inserimento delle dipendenze in ASP.NET Core
author: rick-anderson
description: Informazioni su come ASP.NET Core implementa l'inserimento delle dipendenze e su come usare questa funzionalità.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: ececea3c7cc2f0cdf39bbfd29feec061f9bc6764
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628794"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="83493-103">Inserimento delle dipendenze in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83493-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="83493-104">Di [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="83493-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="83493-105">ASP.NET Core supporta lo schema progettuale di software per l'inserimento di dipendenze, una tecnica per ottenere l'[IoC (Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra le classi e le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="83493-106">Per altre informazioni specifiche per l'inserimento delle dipendenze all'interno di controller MVC, vedere <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="83493-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="83493-107">Per ulteriori informazioni sull'inserimento delle dipendenze delle opzioni, vedere <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="83493-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="83493-108">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="83493-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="83493-109">Panoramica dell'inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="83493-109">Overview of dependency injection</span></span>

<span data-ttu-id="83493-110">Una *dipendenza* è qualsiasi oggetto richiesto da un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="83493-110">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="83493-111">Esaminare la classe `MyDependency` seguente con un metodo `WriteMessage` da cui dipendono altre classi in un'app:</span><span class="sxs-lookup"><span data-stu-id="83493-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public void WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="83493-112">È possibile creare un'istanza della classe `MyDependency` per rendere il metodo `WriteMessage`disponibile per una classe.</span><span class="sxs-lookup"><span data-stu-id="83493-112">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="83493-113">La classe `MyDependency` è una dipendenza della classe `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="83493-113">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage(
            "IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="83493-114">La classe crea e dipende direttamente dall'istanza `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="83493-114">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="83493-115">Le dipendenze del codice, ad esempio l'esempio precedente, sono problematiche e devono essere evitate per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="83493-115">Code dependencies, such as the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="83493-116">Per sostituire `MyDependency` con un'implementazione diversa, la classe deve essere modificata.</span><span class="sxs-lookup"><span data-stu-id="83493-116">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="83493-117">Se `MyDependency` ha dipendenze, devono essere configurate dalla classe.</span><span class="sxs-lookup"><span data-stu-id="83493-117">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="83493-118">In un progetto di grandi dimensioni con più classi che dipendono da `MyDependency`, il codice di configurazione diventa sparso in tutta l'app.</span><span class="sxs-lookup"><span data-stu-id="83493-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="83493-119">È difficile eseguire unit test di questa implementazione.</span><span class="sxs-lookup"><span data-stu-id="83493-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="83493-120">L'app dovrebbe usare una classe `MyDependency` fittizia o stub, ma ciò non è possibile con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="83493-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="83493-121">L'inserimento delle dipendenze consente di risolvere questi problemi tramite:</span><span class="sxs-lookup"><span data-stu-id="83493-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="83493-122">L'uso di un'interfaccia o di una classe di base per astrarre l'implementazione delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="83493-123">La registrazione della dipendenza in un contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="83493-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="83493-124">ASP.NET Core offre il contenitore di servizi predefinito <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="83493-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="83493-125">I servizi vengono registrati nel metodo `Startup.ConfigureServices` dell'app.</span><span class="sxs-lookup"><span data-stu-id="83493-125">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="83493-126">L'*inserimento* del servizio nel costruttore della classe in cui viene usato.</span><span class="sxs-lookup"><span data-stu-id="83493-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="83493-127">Il framework si assume la responsabilità della creazione di un'istanza della dipendenza e della sua eliminazione quando non è più necessaria.</span><span class="sxs-lookup"><span data-stu-id="83493-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="83493-128">Nell'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l'interfaccia `IMyDependency` definisce un metodo che il servizio fornisce all'app:</span><span class="sxs-lookup"><span data-stu-id="83493-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="83493-129">Questa interfaccia viene implementata da un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="83493-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="83493-130">Nell'app di esempio, il servizio `IMyDependency` viene registrato con il tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="83493-130">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="83493-131">Il <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> metodo registra il servizio con una durata con ambito, la durata di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="83493-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="83493-132">Le [durate dei servizi](#service-lifetimes) sono descritte più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="83493-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="83493-133">Nell'app di esempio, viene richiesta l'istanza `IMyDependency` usata per chiamare il metodo `WriteMessage` del servizio:</span><span class="sxs-lookup"><span data-stu-id="83493-133">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="83493-134">Con il modello DI inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="83493-134">With the DI pattern:</span></span>

* <span data-ttu-id="83493-135">Il controller non usa il tipo concreto `MyDependency` , bensì solo l'interfaccia `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="83493-135">The controller doesn't use the concrete type `MyDependency`, only the interface `IMyDependency`.</span></span> <span data-ttu-id="83493-136">Questo consente di modificare facilmente l'implementazione utilizzata dal controller senza modificare il controller.</span><span class="sxs-lookup"><span data-stu-id="83493-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="83493-137">Il controller non crea un'istanza di `MyDependency` , ma viene creato dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-137">The controller doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="83493-138">L'implementazione dell' `IMyDependency` interfaccia può essere migliorata usando l'API di registrazione incorporata:</span><span class="sxs-lookup"><span data-stu-id="83493-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="83493-139">Il `ConfigureServices` metodo aggiornato registra la nuova `IMyDependency` implementazione:</span><span class="sxs-lookup"><span data-stu-id="83493-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="83493-140">`MyDependency2` richiede un oggetto <xref:Microsoft.Extensions.Logging.ILogger`1> nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="83493-140">`MyDependency2` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in the constructor.</span></span> <span data-ttu-id="83493-141">Non è insolito usare l'inserimento delle dipendenze in modo concatenato.</span><span class="sxs-lookup"><span data-stu-id="83493-141">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="83493-142">Ogni dipendenza richiesta richiede a sua volta le proprie dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-142">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="83493-143">Il contenitore risolve le dipendenze nel grafico e restituisce il servizio completamente risolto.</span><span class="sxs-lookup"><span data-stu-id="83493-143">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="83493-144">Il set di dipendenze che devono essere risolte viene generalmente chiamato *albero delle dipendenze* o *grafico dipendenze* o *grafico degli oggetti*.</span><span class="sxs-lookup"><span data-stu-id="83493-144">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="83493-145">`ILogger<TCategoryName>` è un [servizio fornito dal Framework](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="83493-145">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="83493-146">Il contenitore si risolve sfruttando `ILogger<TCategoryName>` i vantaggi dei [tipi aperti (generici)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando la necessità di registrare tutti i tipi [costruiti (generici)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="83493-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="83493-147">Nella terminologia relativa all'inserimento delle dipendenze, un servizio:</span><span class="sxs-lookup"><span data-stu-id="83493-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="83493-148">È in genere un oggetto che fornisce un servizio ad altro codice nell'app, ad esempio il `IMyDependency` servizio.</span><span class="sxs-lookup"><span data-stu-id="83493-148">Is typically an object that provides a service to other code in the app, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="83493-149">Non è correlato a un servizio Web, anche se il servizio può utilizzare un servizio Web.</span><span class="sxs-lookup"><span data-stu-id="83493-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="83493-150">Il Framework fornisce un sistema di [registrazione](xref:fundamentals/logging/index) affidabile.</span><span class="sxs-lookup"><span data-stu-id="83493-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="83493-151">Le `IMyDependency` implementazioni sono state scritte per dimostrare la funzionalità di base di, non per implementare la registrazione.</span><span class="sxs-lookup"><span data-stu-id="83493-151">The `IMyDependency` implementations were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="83493-152">La maggior parte delle app non deve scrivere logger.</span><span class="sxs-lookup"><span data-stu-id="83493-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="83493-153">Il codice seguente illustra l'uso della registrazione predefinita, che non richiede la registrazione dei servizi in `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="83493-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="83493-154">Con il codice precedente non è necessario aggiornare `ConfigureServices` perché la [registrazione](xref:fundamentals/logging/index) viene fornita dal Framework:</span><span class="sxs-lookup"><span data-stu-id="83493-154">Using the preceding code, there is no need to update `ConfigureServices` because [logging](xref:fundamentals/logging/index) is provided by the framework:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a><span data-ttu-id="83493-155">Servizi inseriti all'avvio</span><span class="sxs-lookup"><span data-stu-id="83493-155">Services injected into Startup</span></span>

<span data-ttu-id="83493-156">`Startup`Quando si utilizza l'host generico () è possibile inserire nel costruttore solo i tipi di servizio seguenti <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="83493-156">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="83493-157">I servizi possono essere inseriti in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="83493-157">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="83493-158">Per ulteriori informazioni, vedere <xref:fundamentals/startup> e [accedere alla configurazione all'avvio](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="83493-158">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="83493-159">Registrare servizi aggiuntivi con i metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="83493-159">Register additional services with extension methods</span></span>

<span data-ttu-id="83493-160">Quando è disponibile un metodo di estensione della raccolta di servizi per la registrazione di un servizio:</span><span class="sxs-lookup"><span data-stu-id="83493-160">When a service collection extension method is available to register a service:</span></span>

* <span data-ttu-id="83493-161">La convenzione prevede l'uso di un singolo `Add{SERVICE_NAME}` metodo di estensione per registrare tutti i servizi richiesti dal servizio.</span><span class="sxs-lookup"><span data-stu-id="83493-161">The convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>
* <span data-ttu-id="83493-162">Vengono inoltre registrati i servizi dipendenti.</span><span class="sxs-lookup"><span data-stu-id="83493-162">The dependent services are also registered.</span></span>

<span data-ttu-id="83493-163">Il codice seguente viene generato dal Razor modello di pagine usando singoli account utente e Mostra come aggiungere altri servizi al contenitore usando i metodi di estensione <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="83493-163">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

<span data-ttu-id="83493-164">Per altre informazioni, vedere <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> e <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="83493-164">For more information, see <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> and <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="83493-165">Per istruzioni sulla scrittura di un metodo di estensione per la registrazione dei servizi, vedere la sezione [combinazione di raccolta di servizi](#csc) .</span><span class="sxs-lookup"><span data-stu-id="83493-165">See the section [Combining service collection](#csc) for instructions on writing an extension method to register services.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="83493-166">Durate del servizio</span><span class="sxs-lookup"><span data-stu-id="83493-166">Service lifetimes</span></span>

<span data-ttu-id="83493-167">Scegliere una durata appropriata per ogni servizio registrato.</span><span class="sxs-lookup"><span data-stu-id="83493-167">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="83493-168">I servizi ASP.NET Core possono essere configurati con le impostazioni di durata seguenti:</span><span class="sxs-lookup"><span data-stu-id="83493-168">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="83493-169">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="83493-169">Transient</span></span>

<span data-ttu-id="83493-170">I servizi con durata temporanea (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) vengono creati ogni volta che vengono richiesti dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="83493-170">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="83493-171">Questa impostazione di durata è consigliata per i servizi semplici senza stati.</span><span class="sxs-lookup"><span data-stu-id="83493-171">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="83493-172">Nelle app che elaborano le richieste, i servizi temporanei vengono eliminati alla fine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="83493-172">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="83493-173">Con ambito</span><span class="sxs-lookup"><span data-stu-id="83493-173">Scoped</span></span>

<span data-ttu-id="83493-174">I servizi con durata con ambito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) vengono creati una volta per ogni richiesta client (connessione).</span><span class="sxs-lookup"><span data-stu-id="83493-174">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="83493-175">Quando si usa Entity Framework Core, il <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> metodo di estensione registra i `DbContext` tipi con una durata con ambito per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="83493-175">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="83493-176">Nelle app che elaborano le richieste, i servizi con ambito vengono eliminati alla fine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="83493-176">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="83493-177">Usare i servizi con ambito nel middleware con uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="83493-177">Use scoped services in middleware with one of the following approaches:</span></span>

* <span data-ttu-id="83493-178">Inserire il servizio nel `Invoke` metodo o `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="83493-178">Inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="83493-179">L'inserimento in base all' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) genera un'eccezione in fase di esecuzione perché impone al servizio di comportarsi come un singleton.</span><span class="sxs-lookup"><span data-stu-id="83493-179">Injecting by [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws an exception at run time because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="83493-180">L'esempio nelle [Opzioni durata e registrazione](#lifetime-and-registration-options) utilizza l' `InvokeAsync` approccio.</span><span class="sxs-lookup"><span data-stu-id="83493-180">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) uses the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="83493-181">[Middleware basato su Factory](<xref:fundamentals/middleware/extensibility>).</span><span class="sxs-lookup"><span data-stu-id="83493-181">[Factory-based middleware](<xref:fundamentals/middleware/extensibility>).</span></span> <span data-ttu-id="83493-182">I metodi di estensione <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> verificano se il tipo registrato del middleware implementa <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span><span class="sxs-lookup"><span data-stu-id="83493-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="83493-183">In caso affermativo, viene usata l'istanza di <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> registrata nel contenitore per risolvere l'implementazione <xref:Microsoft.AspNetCore.Http.IMiddleware>, invece di usare la logica di attivazione del middleware basata sulle convenzioni.</span><span class="sxs-lookup"><span data-stu-id="83493-183">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="83493-184">Il middleware è registrato come un servizio con ambito o temporaneo nel contenitore dei servizi dell'app.</span><span class="sxs-lookup"><span data-stu-id="83493-184">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="83493-185">Per altre informazioni, vedere <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="83493-185">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

<span data-ttu-id="83493-186">***Non*** risolvere un servizio con ambito da un singleton.</span><span class="sxs-lookup"><span data-stu-id="83493-186">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="83493-187">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="83493-187">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="83493-188">È bene:</span><span class="sxs-lookup"><span data-stu-id="83493-188">It's fine to:</span></span>

* <span data-ttu-id="83493-189">Risolvere un servizio singleton da un servizio con ambito o temporaneo.</span><span class="sxs-lookup"><span data-stu-id="83493-189">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="83493-190">Risolvere un servizio con ambito da un altro servizio con ambito o temporaneo.</span><span class="sxs-lookup"><span data-stu-id="83493-190">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="83493-191">Per impostazione predefinita, nell'ambiente di sviluppo la risoluzione di un servizio da un altro servizio con durata più lunga genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="83493-191">By default, in the development environment, resolving a service from another service with longer lifetime throws an exception.</span></span> <span data-ttu-id="83493-192">Per ulteriori informazioni, vedere [Convalida dell'ambito](#sv).</span><span class="sxs-lookup"><span data-stu-id="83493-192">For more information, see [Scope validation](#sv).</span></span>

### <a name="singleton"></a><span data-ttu-id="83493-193">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-193">Singleton</span></span>

<span data-ttu-id="83493-194">I servizi di durata singleton ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) vengono creati:</span><span class="sxs-lookup"><span data-stu-id="83493-194">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created either:</span></span>

* <span data-ttu-id="83493-195">La prima volta che viene richiesto.</span><span class="sxs-lookup"><span data-stu-id="83493-195">The first time they're requested.</span></span>
* <span data-ttu-id="83493-196">Dallo sviluppatore, quando si fornisce un'istanza di implementazione direttamente al contenitore.</span><span class="sxs-lookup"><span data-stu-id="83493-196">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="83493-197">Questo approccio è raramente necessario.</span><span class="sxs-lookup"><span data-stu-id="83493-197">This approach is rarely needed.</span></span>

<span data-ttu-id="83493-198">Tutte le richieste successive usano la stessa istanza.</span><span class="sxs-lookup"><span data-stu-id="83493-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="83493-199">Se l'app richiede il comportamento singleton, consentire al contenitore del servizio di gestire la durata del servizio.</span><span class="sxs-lookup"><span data-stu-id="83493-199">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="83493-200">Non implementare il modello di progettazione singleton e fornire codice per eliminare il singleton.</span><span class="sxs-lookup"><span data-stu-id="83493-200">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="83493-201">I servizi non devono mai essere eliminati dal codice che ha risolto il servizio da un contenitore.</span><span class="sxs-lookup"><span data-stu-id="83493-201">Services should never be disposed by code that resolved the service from a container.</span></span> <span data-ttu-id="83493-202">Se un tipo o una factory viene registrata come singleton, il contenitore eliminerà il singleton automaticamente.</span><span class="sxs-lookup"><span data-stu-id="83493-202">If a type or factory is registered as a singleton, the container will dispose the singleton automatically.</span></span>

<span data-ttu-id="83493-203">I servizi singleton devono essere thread-safe e spesso utilizzati nei servizi senza stato.</span><span class="sxs-lookup"><span data-stu-id="83493-203">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="83493-204">Nelle app che elaborano le richieste, i servizi Singleton vengono eliminati quando il <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> viene eliminato alla chiusura dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="83493-204">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="83493-205">Poiché la memoria non viene rilasciata finché l'app non viene arrestata, è necessario prendere in considerazione l'uso della memoria con un singleton.</span><span class="sxs-lookup"><span data-stu-id="83493-205">Because memory is not released until the app is shut down, memory use with a singleton must be considered.</span></span>

> [!WARNING]
> <span data-ttu-id="83493-206">***Non*** risolvere un servizio con ambito da un singleton.</span><span class="sxs-lookup"><span data-stu-id="83493-206">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="83493-207">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="83493-207">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="83493-208">È possibile risolvere un servizio singleton da un servizio con ambito o temporaneo.</span><span class="sxs-lookup"><span data-stu-id="83493-208">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="83493-209">Metodi di registrazione del servizio</span><span class="sxs-lookup"><span data-stu-id="83493-209">Service registration methods</span></span>

<span data-ttu-id="83493-210">I metodi di estensione per la registrazione del servizio offrono overload che risultano utili in scenari specifici.</span><span class="sxs-lookup"><span data-stu-id="83493-210">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="83493-211">Metodo</span><span class="sxs-lookup"><span data-stu-id="83493-211">Method</span></span> | <span data-ttu-id="83493-212">Automatico</span><span class="sxs-lookup"><span data-stu-id="83493-212">Automatic</span></span><br><span data-ttu-id="83493-213">object</span><span class="sxs-lookup"><span data-stu-id="83493-213">object</span></span><br><span data-ttu-id="83493-214">eliminazione</span><span class="sxs-lookup"><span data-stu-id="83493-214">disposal</span></span> | <span data-ttu-id="83493-215">Più elementi</span><span class="sxs-lookup"><span data-stu-id="83493-215">Multiple</span></span><br><span data-ttu-id="83493-216">implementazioni</span><span class="sxs-lookup"><span data-stu-id="83493-216">implementations</span></span> | <span data-ttu-id="83493-217">Pass args</span><span class="sxs-lookup"><span data-stu-id="83493-217">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="83493-218">Esempio:</span><span class="sxs-lookup"><span data-stu-id="83493-218">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="83493-219">Sì</span><span class="sxs-lookup"><span data-stu-id="83493-219">Yes</span></span> | <span data-ttu-id="83493-220">Sì</span><span class="sxs-lookup"><span data-stu-id="83493-220">Yes</span></span> | <span data-ttu-id="83493-221">No</span><span class="sxs-lookup"><span data-stu-id="83493-221">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="83493-222">Esempi:</span><span class="sxs-lookup"><span data-stu-id="83493-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="83493-223">Sì</span><span class="sxs-lookup"><span data-stu-id="83493-223">Yes</span></span> | <span data-ttu-id="83493-224">Sì</span><span class="sxs-lookup"><span data-stu-id="83493-224">Yes</span></span> | <span data-ttu-id="83493-225">Sì</span><span class="sxs-lookup"><span data-stu-id="83493-225">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="83493-226">Esempio:</span><span class="sxs-lookup"><span data-stu-id="83493-226">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="83493-227">Sì</span><span class="sxs-lookup"><span data-stu-id="83493-227">Yes</span></span> | <span data-ttu-id="83493-228">No</span><span class="sxs-lookup"><span data-stu-id="83493-228">No</span></span> | <span data-ttu-id="83493-229">No</span><span class="sxs-lookup"><span data-stu-id="83493-229">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="83493-230">Esempi:</span><span class="sxs-lookup"><span data-stu-id="83493-230">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | <span data-ttu-id="83493-231">No</span><span class="sxs-lookup"><span data-stu-id="83493-231">No</span></span> | <span data-ttu-id="83493-232">Sì</span><span class="sxs-lookup"><span data-stu-id="83493-232">Yes</span></span> | <span data-ttu-id="83493-233">Sì</span><span class="sxs-lookup"><span data-stu-id="83493-233">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="83493-234">Esempi:</span><span class="sxs-lookup"><span data-stu-id="83493-234">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | <span data-ttu-id="83493-235">No</span><span class="sxs-lookup"><span data-stu-id="83493-235">No</span></span> | <span data-ttu-id="83493-236">No</span><span class="sxs-lookup"><span data-stu-id="83493-236">No</span></span> | <span data-ttu-id="83493-237">Sì</span><span class="sxs-lookup"><span data-stu-id="83493-237">Yes</span></span> |

<span data-ttu-id="83493-238">Per ulteriori informazioni sull'eliminazione dei tipi, vedere la sezione [Eliminazione dei servizi](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="83493-238">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="83493-239">Uno scenario comune per più implementazioni è costituito dai [tipi di simulazioni per i test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="83493-239">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="83493-240">`TryAdd{LIFETIME}` i metodi registrano il servizio se non è già stata registrata un'implementazione.</span><span class="sxs-lookup"><span data-stu-id="83493-240">`TryAdd{LIFETIME}` methods register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="83493-241">Nell'esempio seguente, la prima riga registra `MyDependency` per `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="83493-241">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="83493-242">La seconda riga non ha alcun effetto perché `IMyDependency` dispone già di un'implementazione registrata:</span><span class="sxs-lookup"><span data-stu-id="83493-242">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="83493-243">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="83493-243">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="83493-244">I metodi [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrano il servizio se non esiste già un'implementazione *dello stesso tipo*.</span><span class="sxs-lookup"><span data-stu-id="83493-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="83493-245">Più servizi vengono risolti tramite `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="83493-245">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="83493-246">Quando si registrano i servizi, lo sviluppatore deve aggiungere un'istanza se uno degli stessi tipi non è già stato aggiunto.</span><span class="sxs-lookup"><span data-stu-id="83493-246">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="83493-247">In genere, gli autori `TryAddEnumerable` di librerie utilizzano per evitare la registrazione di più copie di un'implementazione nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="83493-247">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="83493-248">Nell'esempio seguente, la prima riga registra `MyDep` per `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="83493-248">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="83493-249">La seconda riga registra `MyDep` per `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="83493-249">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="83493-250">La seconda riga non ha alcun effetto perché `IMyDep1` dispone già di un'implementazione registrata di `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="83493-250">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

<span data-ttu-id="83493-251">La registrazione del servizio è in genere indipendente dall'ordine, tranne quando si registrano più implementazioni dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="83493-251">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="83493-252">`IServiceCollection` è una raccolta di <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> .</span><span class="sxs-lookup"><span data-stu-id="83493-252">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span></span> <span data-ttu-id="83493-253">Il codice seguente illustra come aggiungere un servizio con un costruttore:</span><span class="sxs-lookup"><span data-stu-id="83493-253">The following code shows how to add a service with a constructor:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="83493-254">I `Add{LIFETIME}` metodi usano lo stesso approccio.</span><span class="sxs-lookup"><span data-stu-id="83493-254">The `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="83493-255">Ad esempio, vedere il [codice sorgente per AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="83493-255">For example, see the [source code to AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="83493-256">Comportamento dell'inserimento del costruttore</span><span class="sxs-lookup"><span data-stu-id="83493-256">Constructor injection behavior</span></span>

<span data-ttu-id="83493-257">I servizi possono essere risolti con due meccanismi:</span><span class="sxs-lookup"><span data-stu-id="83493-257">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="83493-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="83493-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="83493-259">Crea oggetti senza registrazione del servizio nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-259">Creates objects without service registration in the dependency injection container.</span></span>
  * <span data-ttu-id="83493-260">Usato con le funzionalità del Framework, ad esempio gli [Helper Tag](xref:mvc/views/tag-helpers/intro), i controller MVC e gli elementi di [associazione di modelli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="83493-260">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="83493-261">I costruttori possono accettare argomenti non forniti tramite l'inserimento di dipendenze, ma gli argomenti devono assegnare valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="83493-261">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="83493-262">Quando i servizi vengono risolti da `IServiceProvider` o `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede un costruttore *pubblico* .</span><span class="sxs-lookup"><span data-stu-id="83493-262">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="83493-263">Quando i servizi vengono risolti da `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede l'esistenza di un solo costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="83493-263">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="83493-264">Sebbene siano supportati gli overload dei costruttori, può essere presente un solo overload i cui argomenti possono essere tutti specificati tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-264">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="83493-265">Contesti di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="83493-265">Entity Framework contexts</span></span>

<span data-ttu-id="83493-266">I contesti di Entity Framework vengono in genere aggiunti al contenitore di servizi usando la [durata con ambito](#service-lifetimes) perché le operazioni di database delle app Web hanno di solito come ambito la richiesta client.</span><span class="sxs-lookup"><span data-stu-id="83493-266">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="83493-267">L'ambito della durata predefinita è se una durata non è specificata da un overload [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) durante la registrazione del contesto del database.</span><span class="sxs-lookup"><span data-stu-id="83493-267">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="83493-268">I servizi con una durata specificata non devono usare un contesto di database con una durata più breve rispetto al servizio.</span><span class="sxs-lookup"><span data-stu-id="83493-268">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="83493-269">Opzioni di durata e di registrazione</span><span class="sxs-lookup"><span data-stu-id="83493-269">Lifetime and registration options</span></span>

<span data-ttu-id="83493-270">Per dimostrare la differenza tra le opzioni di durata e di registrazione, prendere in considerazione le interfacce seguenti che rappresentano le attività come un'operazione con un identificatore `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="83493-270">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="83493-271">A seconda di come viene configurata la durata del servizio di un'operazione per le interfacce seguenti, il contenitore fornisce la stessa istanza o un'istanza diversa del servizio quando richiesto da una classe:</span><span class="sxs-lookup"><span data-stu-id="83493-271">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="83493-272">Le interfacce vengono implementate nella classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="83493-272">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="83493-273">Il `Operation` costruttore genera gli ultimi 4 caratteri di un GUID se non ne viene fornito uno:</span><span class="sxs-lookup"><span data-stu-id="83493-273">The `Operation` constructor generates the last 4 characters of a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

<span data-ttu-id="83493-274">In `Startup.ConfigureServices`, ogni tipo viene aggiunto al contenitore in base alla durata denominata:</span><span class="sxs-lookup"><span data-stu-id="83493-274">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="83493-275">L'app di esempio illustra le durate degli oggetti all'interno e tra le richieste.</span><span class="sxs-lookup"><span data-stu-id="83493-275">The sample app demonstrates object lifetimes within and between requests.</span></span> <span data-ttu-id="83493-276">Il middleware e l'app di esempio `IndexModel` richiede ogni tipo di `IOperation` tipo e registra `OperationId` :</span><span class="sxs-lookup"><span data-stu-id="83493-276">The sample app's `IndexModel` and middleware requests each kind of `IOperation` type and logs the `OperationId`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="83493-277">Il middleware è simile a `IndexModel` e risolve gli stessi servizi:</span><span class="sxs-lookup"><span data-stu-id="83493-277">The middleware is similar to the `IndexModel` and resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="83493-278">I servizi con ambito devono essere risolti nel `InvokeAsync` Metodo:</span><span class="sxs-lookup"><span data-stu-id="83493-278">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="83493-279">L'output del logger Mostra:</span><span class="sxs-lookup"><span data-stu-id="83493-279">The logger output shows:</span></span>

* <span data-ttu-id="83493-280">Gli oggetti *temporanei* sono sempre diversi.</span><span class="sxs-lookup"><span data-stu-id="83493-280">*Transient* objects are always different.</span></span> <span data-ttu-id="83493-281">Il `OperationId` valore temporaneo è diverso in `IndexModel` e nel middleware.</span><span class="sxs-lookup"><span data-stu-id="83493-281">The transient `OperationId` value is different in the `IndexModel` and the middleware.</span></span>
* <span data-ttu-id="83493-282">Gli oggetti con *ambito* sono gli stessi in ogni richiesta, ma sono diversi in ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="83493-282">*Scoped* objects are the same in each request but different across each request.</span></span>
* <span data-ttu-id="83493-283">Gli oggetti *singleton* sono gli stessi per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="83493-283">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="83493-284">Per ridurre l'output di registrazione, impostare "Logging: LogLevel: Microsoft: Error" nella *appsettings.Development.jssu* file:</span><span class="sxs-lookup"><span data-stu-id="83493-284">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="83493-285">Chiamare i servizi da main</span><span class="sxs-lookup"><span data-stu-id="83493-285">Call services from main</span></span>

<span data-ttu-id="83493-286">Creare un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>IServiceScope con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) per risolvere un servizio con ambito nell'ambito di applicazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="83493-286">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="83493-287">Questo approccio è utile per accedere a un servizio con ambito all'avvio per eseguire le attività di inizializzazione:</span><span class="sxs-lookup"><span data-stu-id="83493-287">This approach is useful to access a scoped service at startup to run initialization tasks:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var scope = host.Services.CreateScope())
        {
            var services = scope.ServiceProvider;

            try
            {
                SeedData.Initialize(services);
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred seeding the DB.");
            }
        }

        host.Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="83493-288">Il codice precedente è relativo all' [aggiunta dell'inizializzatore di seme](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) nell' Razor esercitazione sulle pagine.</span><span class="sxs-lookup"><span data-stu-id="83493-288">The preceding code is from [Add the seed initializer](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) in the Razor Pages tutorial.</span></span>

<span data-ttu-id="83493-289">L'esempio seguente indica come ottenere un contesto per `IMyDependency` in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="83493-289">The following example shows how to obtain a context for the `IMyDependency` in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="83493-290">Convalida dell'ambito</span><span class="sxs-lookup"><span data-stu-id="83493-290">Scope validation</span></span>

<span data-ttu-id="83493-291">Quando l'app è in esecuzione nell' [ambiente di sviluppo](xref:fundamentals/environments) e chiama [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) per compilare l'host, il provider di servizi predefinito esegue i controlli per verificare che:</span><span class="sxs-lookup"><span data-stu-id="83493-291">When the app is running in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="83493-292">I servizi con ambito non vengano risolti direttamente o indirettamente dal provider di servizi radice.</span><span class="sxs-lookup"><span data-stu-id="83493-292">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="83493-293">I servizi con ambito non vengano inseriti direttamente o indirettamente in singleton.</span><span class="sxs-lookup"><span data-stu-id="83493-293">Scoped services aren't directly or indirectly injected into singletons.</span></span>
* <span data-ttu-id="83493-294">I servizi temporanei non vengono inseriti direttamente o indirettamente in Singleton o servizi con ambito.</span><span class="sxs-lookup"><span data-stu-id="83493-294">Transient services aren't directly or indirectly injected into singletons or scoped services.</span></span>

<span data-ttu-id="83493-295">Il provider di servizi radice venga creato con la chiamata di <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="83493-295">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="83493-296">La durata del provider di servizi radice corrisponde alla durata dell'app quando il provider inizia con l'app e viene eliminato quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="83493-296">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="83493-297">I servizi con ambito vengono eliminati dal contenitore che li ha creati.</span><span class="sxs-lookup"><span data-stu-id="83493-297">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="83493-298">Se nel contenitore radice viene creato un servizio con ambito, la durata del servizio viene innalzata di livello a Singleton perché viene eliminata dal contenitore radice solo quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="83493-298">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app is shut down.</span></span> <span data-ttu-id="83493-299">La convalida degli ambiti servizio rileva queste situazioni nel contesto della chiamata di `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="83493-299">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="83493-300">Per ulteriori informazioni, vedere [Convalida dell'ambito](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="83493-300">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="83493-301">Servizi di richiesta</span><span class="sxs-lookup"><span data-stu-id="83493-301">Request Services</span></span>

<span data-ttu-id="83493-302">I servizi disponibili all'interno di una richiesta ASP.NET Core da `HttpContext` vengono esposti tramite la raccolta [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="83493-302">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="83493-303">I servizi di richiesta rappresentano i servizi configurati e richiesti come parte dell'app.</span><span class="sxs-lookup"><span data-stu-id="83493-303">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="83493-304">Quando gli oggetti specificano dipendenze, le dipendenze sono soddisfatte dai tipi individuati in `RequestServices`, non in `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="83493-304">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="83493-305">In generale, l'app non deve usare queste proprietà direttamente.</span><span class="sxs-lookup"><span data-stu-id="83493-305">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="83493-306">Richiedere invece i tipi richiesti dalle classi tramite costruttori di classe e consentire al Framework di inserire le dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-306">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="83493-307">Si ottengono classi più facili da testare.</span><span class="sxs-lookup"><span data-stu-id="83493-307">This yields classes that are easier to test.</span></span>

<span data-ttu-id="83493-308">ASP.NET Core crea un ambito per ogni richiesta ed `RequestServices` espone il provider di servizi con ambito.</span><span class="sxs-lookup"><span data-stu-id="83493-308">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="83493-309">Tutti i servizi con ambito sono validi fino a quando la richiesta è attiva.</span><span class="sxs-lookup"><span data-stu-id="83493-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="83493-310">È consigliabile richiedere le dipendenze come parametri del costruttore per l'accesso alla raccolta `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="83493-310">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="83493-311">Progettare i servizi per l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="83493-311">Design services for dependency injection</span></span>

<span data-ttu-id="83493-312">Le procedure consigliate prevedono di:</span><span class="sxs-lookup"><span data-stu-id="83493-312">Best practices are to:</span></span>

* <span data-ttu-id="83493-313">Progettare i servizi per usare l'inserimento delle dipendenze per ottenere le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-313">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="83493-314">Evitare membri e classi statiche con stato.</span><span class="sxs-lookup"><span data-stu-id="83493-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="83493-315">Progettare le app in modo da usare i servizi singleton, evitando così di creare uno stato globale.</span><span class="sxs-lookup"><span data-stu-id="83493-315">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="83493-316">Evitare la creazione diretta di istanze delle classi dipendenti all'interno di servizi.</span><span class="sxs-lookup"><span data-stu-id="83493-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="83493-317">La creazione diretta di istanze associa il codice a una determinata implementazione.</span><span class="sxs-lookup"><span data-stu-id="83493-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="83493-318">Fare in modo che le classi siano piccole, con factoring corretto e facili da testare.</span><span class="sxs-lookup"><span data-stu-id="83493-318">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="83493-319">Se una classe sembra contenere troppe dipendenze inserite, questo è in genere un segno che la classe ha troppe responsabilità e viola il principio di [singola responsabilità (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="83493-319">If a class seems to have too many injected dependencies, that's generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="83493-320">Tentare di effettuare il refactoring della classe spostando alcune delle responsabilità in una nuova classe.</span><span class="sxs-lookup"><span data-stu-id="83493-320">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="83493-321">Tenere presente che Razor le classi del modello della pagina delle pagine e le classi controller MVC devono concentrarsi sulle problematiche dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="83493-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="83493-322">Eliminazione dei servizi</span><span class="sxs-lookup"><span data-stu-id="83493-322">Disposal of services</span></span>

<span data-ttu-id="83493-323">Il contenitore chiama <xref:System.IDisposable.Dispose*> per i tipi <xref:System.IDisposable> creati.</span><span class="sxs-lookup"><span data-stu-id="83493-323">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="83493-324">I servizi non devono mai essere eliminati da un codice che ha risolto il servizio da un contenitore.</span><span class="sxs-lookup"><span data-stu-id="83493-324">Services should never be disposed by any code that resolved the service from a container.</span></span> <span data-ttu-id="83493-325">Se un tipo o una factory viene registrata come singleton, il contenitore elimina il singleton.</span><span class="sxs-lookup"><span data-stu-id="83493-325">If a type or factory is registered as a singleton, the container disposes the singleton.</span></span>

<span data-ttu-id="83493-326">Nell'esempio seguente i servizi vengono creati dal contenitore del servizio ed eliminati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="83493-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="83493-327">La console di debug Visualizza il seguente output dopo ogni aggiornamento della pagina di indice:</span><span class="sxs-lookup"><span data-stu-id="83493-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="83493-328">Servizi non creati dal contenitore del servizio</span><span class="sxs-lookup"><span data-stu-id="83493-328">Services not created by the service container</span></span>
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
<span data-ttu-id="83493-329">Osservare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="83493-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="83493-330">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="83493-330">In the preceding code:</span></span>

* <span data-ttu-id="83493-331">Le istanze del servizio non vengono create dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="83493-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="83493-332">Le durate di servizio previste non sono note dal Framework.</span><span class="sxs-lookup"><span data-stu-id="83493-332">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="83493-333">Il Framework non elimina automaticamente i servizi.</span><span class="sxs-lookup"><span data-stu-id="83493-333">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="83493-334">Se i servizi non vengono eliminati in modo esplicito nel codice dello sviluppatore, vengono mantenuti fino a quando l'app non viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="83493-334">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="83493-335">Linee guida IDisposable per istanze temporanee e condivise</span><span class="sxs-lookup"><span data-stu-id="83493-335">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="83493-336">Durata temporanea e limitata</span><span class="sxs-lookup"><span data-stu-id="83493-336">Transient, limited lifetime</span></span>

<span data-ttu-id="83493-337">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="83493-337">**Scenario**</span></span>

<span data-ttu-id="83493-338">L'app richiede un' <xref:System.IDisposable> istanza con una durata temporanea per uno degli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="83493-338">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="83493-339">L'istanza viene risolta nell'ambito radice (contenitore radice).</span><span class="sxs-lookup"><span data-stu-id="83493-339">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="83493-340">L'istanza deve essere eliminata prima della fine dell'ambito.</span><span class="sxs-lookup"><span data-stu-id="83493-340">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="83493-341">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="83493-341">**Solution**</span></span>

<span data-ttu-id="83493-342">Usare il modello factory per creare un'istanza all'esterno dell'ambito padre.</span><span class="sxs-lookup"><span data-stu-id="83493-342">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="83493-343">In questa situazione, in genere l'app dispone di un `Create` metodo che chiama direttamente il costruttore del tipo finale.</span><span class="sxs-lookup"><span data-stu-id="83493-343">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="83493-344">Se il tipo finale ha altre dipendenze, la factory può:</span><span class="sxs-lookup"><span data-stu-id="83493-344">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="83493-345">Ricevere un oggetto <xref:System.IServiceProvider> nel relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="83493-345">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="83493-346">Usare <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> per creare un'istanza dell'istanza all'esterno del contenitore, usando il contenitore per le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="83493-347">Istanza condivisa, durata limitata</span><span class="sxs-lookup"><span data-stu-id="83493-347">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="83493-348">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="83493-348">**Scenario**</span></span>

<span data-ttu-id="83493-349">L'app richiede un' <xref:System.IDisposable> istanza condivisa tra più servizi, ma <xref:System.IDisposable> deve avere una durata limitata.</span><span class="sxs-lookup"><span data-stu-id="83493-349">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="83493-350">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="83493-350">**Solution**</span></span>

<span data-ttu-id="83493-351">Registrare l'istanza con una durata con ambito.</span><span class="sxs-lookup"><span data-stu-id="83493-351">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="83493-352">Usare <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> per avviare e creare un nuovo oggetto <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="83493-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="83493-353">Usare l'ambito <xref:System.IServiceProvider> per ottenere i servizi richiesti.</span><span class="sxs-lookup"><span data-stu-id="83493-353">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="83493-354">Eliminare l'ambito quando la durata deve terminare.</span><span class="sxs-lookup"><span data-stu-id="83493-354">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="83493-355">Linee guida generali sull'IDisposable</span><span class="sxs-lookup"><span data-stu-id="83493-355">General IDisposable guidelines</span></span>

* <span data-ttu-id="83493-356">Non registrare <xref:System.IDisposable> istanze con un ambito temporaneo.</span><span class="sxs-lookup"><span data-stu-id="83493-356">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="83493-357">Usare invece il modello Factory.</span><span class="sxs-lookup"><span data-stu-id="83493-357">Use the factory pattern instead.</span></span>
* <span data-ttu-id="83493-358">Non risolvere istanze temporanee o <xref:System.IDisposable> con ambito nell'ambito radice.</span><span class="sxs-lookup"><span data-stu-id="83493-358">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="83493-359">L'unica eccezione generale si verifica quando l'app crea/ricrea ed Elimina <xref:System.IServiceProvider> , che non è un modello ideale.</span><span class="sxs-lookup"><span data-stu-id="83493-359">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="83493-360">Per <xref:System.IDisposable> la ricezione di una dipendenza tramite di non è necessario che il ricevitore implementi <xref:System.IDisposable> se stesso.</span><span class="sxs-lookup"><span data-stu-id="83493-360">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="83493-361">Il ricevitore della <xref:System.IDisposable> dipendenza non deve chiamare <xref:System.IDisposable.Dispose%2A> su tale dipendenza.</span><span class="sxs-lookup"><span data-stu-id="83493-361">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="83493-362">Gli ambiti devono essere usati per controllare la durata dei servizi.</span><span class="sxs-lookup"><span data-stu-id="83493-362">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="83493-363">Gli ambiti non sono gerarchici e non esiste alcuna connessione speciale tra gli ambiti.</span><span class="sxs-lookup"><span data-stu-id="83493-363">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="83493-364">Sostituzione del contenitore di servizi predefinito</span><span class="sxs-lookup"><span data-stu-id="83493-364">Default service container replacement</span></span>

<span data-ttu-id="83493-365">Il contenitore dei servizi incorporato è progettato per soddisfare le esigenze del Framework e della maggior parte delle app consumer.</span><span class="sxs-lookup"><span data-stu-id="83493-365">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="83493-366">Si consiglia di usare il contenitore predefinito a meno che non sia necessaria una funzionalità specifica che il contenitore incorporato non supporta, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="83493-366">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="83493-367">Inserimento di proprietà</span><span class="sxs-lookup"><span data-stu-id="83493-367">Property injection</span></span>
* <span data-ttu-id="83493-368">Inserimento in base al nome</span><span class="sxs-lookup"><span data-stu-id="83493-368">Injection based on name</span></span>
* <span data-ttu-id="83493-369">Contenitori figlio</span><span class="sxs-lookup"><span data-stu-id="83493-369">Child containers</span></span>
* <span data-ttu-id="83493-370">Gestione della durata personalizzata</span><span class="sxs-lookup"><span data-stu-id="83493-370">Custom lifetime management</span></span>
* <span data-ttu-id="83493-371">Supporto di `Func<T>` per l'inizializzazione differita</span><span class="sxs-lookup"><span data-stu-id="83493-371">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="83493-372">Registrazione basata sulle convenzioni</span><span class="sxs-lookup"><span data-stu-id="83493-372">Convention-based registration</span></span>

<span data-ttu-id="83493-373">Con ASP.NET Core app è possibile usare i contenitori di terze parti seguenti:</span><span class="sxs-lookup"><span data-stu-id="83493-373">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="83493-374">Autofac</span><span class="sxs-lookup"><span data-stu-id="83493-374">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="83493-375">DryIoc</span><span class="sxs-lookup"><span data-stu-id="83493-375">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="83493-376">Laura</span><span class="sxs-lookup"><span data-stu-id="83493-376">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="83493-377">LightInject</span><span class="sxs-lookup"><span data-stu-id="83493-377">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="83493-378">Lamar</span><span class="sxs-lookup"><span data-stu-id="83493-378">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="83493-379">Stashbox</span><span class="sxs-lookup"><span data-stu-id="83493-379">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="83493-380">Unity</span><span class="sxs-lookup"><span data-stu-id="83493-380">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="83493-381">Thread safety</span><span class="sxs-lookup"><span data-stu-id="83493-381">Thread safety</span></span>

<span data-ttu-id="83493-382">Creare servizi singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="83493-382">Create thread-safe singleton services.</span></span> <span data-ttu-id="83493-383">Se un servizio singleton ha una dipendenza in un servizio temporaneo, è necessario che anche il servizio temporaneo sia thread-safe, a seconda di come viene usato dal singleton.</span><span class="sxs-lookup"><span data-stu-id="83493-383">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="83493-384">Il metodo factory di un singolo servizio, ad esempio il secondo argomento di [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), non deve essere thread-safe.</span><span class="sxs-lookup"><span data-stu-id="83493-384">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="83493-385">Come nel caso di un costruttore di tipo (`static`), è garantito che venga chiamato una sola volta da un singolo thread.</span><span class="sxs-lookup"><span data-stu-id="83493-385">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="83493-386">Consigli</span><span class="sxs-lookup"><span data-stu-id="83493-386">Recommendations</span></span>

* <span data-ttu-id="83493-387">La risoluzione basata sui servizi `async/await` e `Task` non è supportata.</span><span class="sxs-lookup"><span data-stu-id="83493-387">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="83493-388">C# non supporta costruttori asincroni.</span><span class="sxs-lookup"><span data-stu-id="83493-388">C# does not support asynchronous constructors.</span></span> <span data-ttu-id="83493-389">Il modello consigliato prevede l'uso di metodi asincroni dopo la risoluzione sincrona del servizio.</span><span class="sxs-lookup"><span data-stu-id="83493-389">The recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="83493-390">Evitare di archiviare i dati e la configurazione direttamente nel contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="83493-390">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="83493-391">Ad esempio, il carrello acquisti di un utente non dovrebbe in genere essere aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="83493-391">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="83493-392">La configurazione deve usare il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="83493-392">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="83493-393">Analogamente, evitare gli oggetti "contenitori di dati" che hanno la sola funzione di consentire l'accesso ad altri oggetti.</span><span class="sxs-lookup"><span data-stu-id="83493-393">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="83493-394">È preferibile richiedere l'elemento effettivo tramite inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-394">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="83493-395">Evitare l'accesso statico ai servizi.</span><span class="sxs-lookup"><span data-stu-id="83493-395">Avoid static access to services.</span></span> <span data-ttu-id="83493-396">Ad esempio, evitare di digitare in modo statico [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) per l'uso altrove).</span><span class="sxs-lookup"><span data-stu-id="83493-396">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>
* <span data-ttu-id="83493-397">Mantieni le fabbriche veloci e sincrone.</span><span class="sxs-lookup"><span data-stu-id="83493-397">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="83493-398">Evitare di usare il *modello di localizzatore del servizio*.</span><span class="sxs-lookup"><span data-stu-id="83493-398">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="83493-399">Ad esempio, non richiamare <xref:System.IServiceProvider.GetService*> per ottenere un'istanza del servizio quando è invece possibile usare l'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="83493-399">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="83493-400">**Non corretto:**</span><span class="sxs-lookup"><span data-stu-id="83493-400">**Incorrect:**</span></span>

    ![Codice errato](dependency-injection/_static/bad.png)

  <span data-ttu-id="83493-402">**Corretto**:</span><span class="sxs-lookup"><span data-stu-id="83493-402">**Correct**:</span></span>

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
* <span data-ttu-id="83493-403">Un'altra variazione del localizzatore del servizio da evitare è inserire una factory che risolve le dipendenze in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="83493-403">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="83493-404">Queste procedure combinano le strategie di [inversione del controllo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="83493-404">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="83493-405">Evitare l'accesso statico a `HttpContext` (ad esempio [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="83493-405">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="83493-406">Evitare chiamate a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="83493-406">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="83493-407">`BuildServiceProvider`La chiamata in genere si verifica quando lo sviluppatore desidera risolvere un servizio in `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="83493-407">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="83493-408">Si consideri, ad esempio, il caso in cui è necessario ottenere la `LoginPath` configurazione da.</span><span class="sxs-lookup"><span data-stu-id="83493-408">For example, consider the case where you need go get the `LoginPath` from configuration.</span></span> <span data-ttu-id="83493-409">Evitare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="83493-409">Avoid the following code:</span></span>

  ![codice errato durante la chiamata di BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="83493-411">Nell'immagine precedente, selezionando la linea ondulata verde in `services.BuildServiceProvider` viene visualizzato l'avviso ASP0000 seguente:</span><span class="sxs-lookup"><span data-stu-id="83493-411">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>
    * <span data-ttu-id="83493-412">ASP0000 la chiamata a' BuildServiceProvider ' dal codice dell'applicazione comporta una copia aggiuntiva dei servizi singleton creati.</span><span class="sxs-lookup"><span data-stu-id="83493-412">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="83493-413">Prendere in considerazione alternative come la dipendenza che inserisce i servizi come parametri per "Configure".</span><span class="sxs-lookup"><span data-stu-id="83493-413">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

   <span data-ttu-id="83493-414">`BuildServiceProvider`La chiamata di crea un secondo contenitore, che può creare singleton incompleti e causare riferimenti a oggetti grafici tra più contenitori.</span><span class="sxs-lookup"><span data-stu-id="83493-414">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span> <span data-ttu-id="83493-415">Un modo corretto per ottenere `LoginPath` è usare il modello di opzione con di:</span><span class="sxs-lookup"><span data-stu-id="83493-415">A correct way to get `LoginPath` is using the option pattern with DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="83493-416">I servizi temporanei Disposable vengono acquisiti dal contenitore per l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="83493-416">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="83493-417">Questa operazione può comportare una perdita di memoria se risolta dal contenitore di primo livello.</span><span class="sxs-lookup"><span data-stu-id="83493-417">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="83493-418">Abilitare la convalida dell'ambito per assicurarsi che l'app non disponga di servizi con ambito per l'acquisizione di singleton.</span><span class="sxs-lookup"><span data-stu-id="83493-418">Enable scope validation to make sure the app doesn't have scoped services capturing singletons.</span></span> <span data-ttu-id="83493-419">Per ulteriori informazioni, vedere [Convalida dell'ambito](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="83493-419">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="83493-420">È tuttavia possibile che in alcuni casi queste raccomandazioni debbano essere ignorate.</span><span class="sxs-lookup"><span data-stu-id="83493-420">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="83493-421">Le eccezioni sono rari, soprattutto casi speciali all'interno del Framework stesso.</span><span class="sxs-lookup"><span data-stu-id="83493-421">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="83493-422">L'inserimento di dipendenze è un'*alternativa* ai modelli di accesso agli oggetti statici/globali.</span><span class="sxs-lookup"><span data-stu-id="83493-422">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="83493-423">Se l'inserimento di dipendenze viene usato con l'accesso agli oggetti statico i vantaggi non saranno evidenti.</span><span class="sxs-lookup"><span data-stu-id="83493-423">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="83493-424">Modelli consigliati per il multi-tenant in DI</span><span class="sxs-lookup"><span data-stu-id="83493-424">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="83493-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) fornisce il multi-tenant.</span><span class="sxs-lookup"><span data-stu-id="83493-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="83493-426">Per ulteriori informazioni, vedere la [documentazione di Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="83493-426">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="83493-427">Vedere le app di esempio in https://github.com/OrchardCMS/OrchardCore.Samples per esempi di come creare app modulari e multi-tenant usando solo il Framework Orchard core senza le funzionalità specifiche del CMS.</span><span class="sxs-lookup"><span data-stu-id="83493-427">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="83493-428">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="83493-428">Framework-provided services</span></span>

<span data-ttu-id="83493-429">Il `Startup.ConfigureServices` metodo è responsabile della definizione dei servizi utilizzati dall'app, incluse le funzionalità della piattaforma, ad esempio Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="83493-429">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="83493-430">Inizialmente, l'oggetto `IServiceCollection` fornito a `ConfigureServices` dispone di servizi definiti dal Framework a seconda della [configurazione dell'host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="83493-430">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="83493-431">Le app basate su un modello di ASP.NET Core hanno più di 250 servizi registrati dal Framework.</span><span class="sxs-lookup"><span data-stu-id="83493-431">Apps based on an ASP.NET Core templates have more than 250 services registered by the framework.</span></span> <span data-ttu-id="83493-432">Nella tabella seguente è elencato un piccolo esempio di servizi registrati dal Framework.</span><span class="sxs-lookup"><span data-stu-id="83493-432">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="83493-433">Tipo di servizio</span><span class="sxs-lookup"><span data-stu-id="83493-433">Service Type</span></span> | <span data-ttu-id="83493-434">Durata</span><span class="sxs-lookup"><span data-stu-id="83493-434">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="83493-435">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="83493-435">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | <span data-ttu-id="83493-436">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | <span data-ttu-id="83493-437">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="83493-438">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="83493-439">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="83493-439">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="83493-440">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-440">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="83493-441">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="83493-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="83493-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="83493-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-443">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="83493-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-444">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="83493-445">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="83493-445">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="83493-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-446">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="83493-447">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-447">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="83493-448">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-448">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="83493-449">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="83493-449">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="83493-450">Modelli DI conferenza NDC per lo sviluppo DI app DI</span><span class="sxs-lookup"><span data-stu-id="83493-450">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="83493-451">Quattro modi per eliminare IDisposable in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83493-451">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="83493-452">Scrittura di codice pulito in ASP.NET Core con inserimento delle dipendenze (MSDN)</span><span class="sxs-lookup"><span data-stu-id="83493-452">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="83493-453">Explicit Dependencies Principle (Principio delle dipendenze esplicite)</span><span class="sxs-lookup"><span data-stu-id="83493-453">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="83493-454">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler) (Contenitori di inversione del controllo e modello di inserimento delle dipendenze)</span><span class="sxs-lookup"><span data-stu-id="83493-454">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="83493-455">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Come registrare un servizio con più interfacce in ASP.NET Core DI)</span><span class="sxs-lookup"><span data-stu-id="83493-455">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="83493-456">Di [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="83493-456">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="83493-457">ASP.NET Core supporta lo schema progettuale di software per l'inserimento di dipendenze, una tecnica per ottenere l'[IoC (Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra le classi e le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-457">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="83493-458">Per altre informazioni specifiche per l'inserimento delle dipendenze all'interno di controller MVC, vedere <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="83493-458">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="83493-459">[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="83493-459">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="83493-460">Panoramica dell'inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="83493-460">Overview of dependency injection</span></span>

<span data-ttu-id="83493-461">Una *dipendenza* è qualsiasi oggetto richiesto da un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="83493-461">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="83493-462">Esaminare la classe `MyDependency` seguente con un metodo `WriteMessage` da cui dipendono altre classi in un'app:</span><span class="sxs-lookup"><span data-stu-id="83493-462">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="83493-463">È possibile creare un'istanza della classe `MyDependency` per rendere il metodo `WriteMessage`disponibile per una classe.</span><span class="sxs-lookup"><span data-stu-id="83493-463">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="83493-464">La classe `MyDependency` è una dipendenza della classe `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="83493-464">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="83493-465">La classe crea e dipende direttamente dall'istanza `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="83493-465">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="83493-466">Le dipendenze del codice (come nell'esempio precedente) sono problematiche e devono essere evitate per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="83493-466">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="83493-467">Per sostituire `MyDependency` con un'implementazione diversa, la classe deve essere modificata.</span><span class="sxs-lookup"><span data-stu-id="83493-467">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="83493-468">Se `MyDependency` ha dipendenze, devono essere configurate dalla classe.</span><span class="sxs-lookup"><span data-stu-id="83493-468">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="83493-469">In un progetto di grandi dimensioni con più classi che dipendono da `MyDependency`, il codice di configurazione diventa sparso in tutta l'app.</span><span class="sxs-lookup"><span data-stu-id="83493-469">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="83493-470">È difficile eseguire unit test di questa implementazione.</span><span class="sxs-lookup"><span data-stu-id="83493-470">This implementation is difficult to unit test.</span></span> <span data-ttu-id="83493-471">L'app dovrebbe usare una classe `MyDependency` fittizia o stub, ma ciò non è possibile con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="83493-471">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="83493-472">L'inserimento delle dipendenze consente di risolvere questi problemi tramite:</span><span class="sxs-lookup"><span data-stu-id="83493-472">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="83493-473">L'uso di un'interfaccia o di una classe di base per astrarre l'implementazione delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-473">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="83493-474">La registrazione della dipendenza in un contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="83493-474">Registration of the dependency in a service container.</span></span> <span data-ttu-id="83493-475">ASP.NET Core offre il contenitore di servizi predefinito <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="83493-475">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="83493-476">I servizi vengono registrati nel metodo `Startup.ConfigureServices` dell'app.</span><span class="sxs-lookup"><span data-stu-id="83493-476">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="83493-477">L'*inserimento* del servizio nel costruttore della classe in cui viene usato.</span><span class="sxs-lookup"><span data-stu-id="83493-477">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="83493-478">Il framework si assume la responsabilità della creazione di un'istanza della dipendenza e della sua eliminazione quando non è più necessaria.</span><span class="sxs-lookup"><span data-stu-id="83493-478">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="83493-479">Nell'[app di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l'interfaccia `IMyDependency` definisce un metodo che il servizio fornisce all'app:</span><span class="sxs-lookup"><span data-stu-id="83493-479">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="83493-480">Questa interfaccia viene implementata da un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="83493-480">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="83493-481">`MyDependency` richiede <xref:Microsoft.Extensions.Logging.ILogger`1> nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="83493-481">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="83493-482">Non è insolito usare l'inserimento delle dipendenze in modo concatenato.</span><span class="sxs-lookup"><span data-stu-id="83493-482">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="83493-483">Ogni dipendenza richiesta richiede a sua volta le proprie dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-483">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="83493-484">Il contenitore risolve le dipendenze nel grafico e restituisce il servizio completamente risolto.</span><span class="sxs-lookup"><span data-stu-id="83493-484">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="83493-485">Il set di dipendenze che devono essere risolte viene generalmente chiamato *albero delle dipendenze* o *grafico dipendenze* o *grafico degli oggetti*.</span><span class="sxs-lookup"><span data-stu-id="83493-485">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="83493-486">`IMyDependency` e `ILogger<TCategoryName>` devono essere registrati nel contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="83493-486">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="83493-487">`IMyDependency` viene registrato in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="83493-487">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="83493-488">`ILogger<TCategoryName>` viene registrato dall'infrastruttura di registrazione delle astrazioni, pertanto si tratta di un [servizio fornito dal Framework](#framework-provided-services) registrato per impostazione predefinita dal Framework.</span><span class="sxs-lookup"><span data-stu-id="83493-488">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="83493-489">Il contenitore risolve `ILogger<TCategoryName>` avvalendosi di [tipi aperti (generici)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando l'esigenza di registrare ogni singolo [tipo costruito (generico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="83493-489">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="83493-490">Nell'app di esempio, il servizio `IMyDependency` viene registrato con il tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="83493-490">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="83493-491">La registrazione definisce come ambito della durata di servizio la durata di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="83493-491">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="83493-492">Le [durate dei servizi](#service-lifetimes) sono descritte più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="83493-492">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="83493-493">Ogni metodo di estensione `services.Add{SERVICE_NAME}` aggiunge (e potenzialmente configura) i servizi.</span><span class="sxs-lookup"><span data-stu-id="83493-493">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="83493-494">Ad esempio, `services.AddMvc()` aggiunge le pagine dei servizi Razor e MVC require.</span><span class="sxs-lookup"><span data-stu-id="83493-494">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="83493-495">È consigliabile che le app seguano questa convenzione.</span><span class="sxs-lookup"><span data-stu-id="83493-495">We recommended that apps follow this convention.</span></span> <span data-ttu-id="83493-496">Inserire i metodi di estensione nello spazio dei nomi [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) per incapsulare i gruppi di registrazioni di servizio.</span><span class="sxs-lookup"><span data-stu-id="83493-496">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="83493-497">Se il costruttore del servizio richiede un [tipo incorporato](/dotnet/csharp/language-reference/keywords/built-in-types-table), ad esempio `string`, è possibile inserirlo usando la [configurazione](xref:fundamentals/configuration/index) o il [modello di opzioni](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="83493-497">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="83493-498">È richiesta un'istanza del servizio tramite il costruttore di una classe in cui il servizio viene usato e assegnato a un campo privato.</span><span class="sxs-lookup"><span data-stu-id="83493-498">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="83493-499">Il campo viene usato per accedere al servizio in base alle esigenze per l'intera classe.</span><span class="sxs-lookup"><span data-stu-id="83493-499">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="83493-500">Nell'app di esempio, viene richiesta l'istanza `IMyDependency` usata per chiamare il metodo `WriteMessage` del servizio:</span><span class="sxs-lookup"><span data-stu-id="83493-500">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="83493-501">Servizi inseriti all'avvio</span><span class="sxs-lookup"><span data-stu-id="83493-501">Services injected into Startup</span></span>

<span data-ttu-id="83493-502">`Startup`Quando si utilizza l'host generico () è possibile inserire nel costruttore solo i tipi di servizio seguenti <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="83493-502">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="83493-503">I servizi possono essere inseriti in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="83493-503">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="83493-504">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="83493-504">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="83493-505">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="83493-505">Framework-provided services</span></span>

<span data-ttu-id="83493-506">Il `Startup.ConfigureServices` metodo è responsabile della definizione dei servizi utilizzati dall'app, incluse le funzionalità della piattaforma, ad esempio Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="83493-506">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="83493-507">Inizialmente, l'oggetto `IServiceCollection` fornito a `ConfigureServices` dispone di servizi definiti dal Framework a seconda della [configurazione dell'host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="83493-507">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="83493-508">Non è insolito che un'app basata su un modello di ASP.NET Core disponga di centinaia di servizi registrati dal Framework.</span><span class="sxs-lookup"><span data-stu-id="83493-508">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="83493-509">Nella tabella seguente è elencato un piccolo esempio di servizi registrati dal Framework.</span><span class="sxs-lookup"><span data-stu-id="83493-509">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="83493-510">Tipo di servizio</span><span class="sxs-lookup"><span data-stu-id="83493-510">Service Type</span></span> | <span data-ttu-id="83493-511">Durata</span><span class="sxs-lookup"><span data-stu-id="83493-511">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="83493-512">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="83493-512">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="83493-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="83493-514">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="83493-515">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-515">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="83493-516">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="83493-516">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="83493-517">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-517">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="83493-518">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="83493-518">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="83493-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="83493-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-520">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="83493-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-521">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="83493-522">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="83493-522">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="83493-523">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-523">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="83493-524">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-524">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="83493-525">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-525">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="83493-526">Registrare servizi aggiuntivi con i metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="83493-526">Register additional services with extension methods</span></span>

<span data-ttu-id="83493-527">Quando è disponibile un metodo di estensione della raccolta di servizi per la registrazione di un servizio (e dei servizi dipendenti, se necessario), la convenzione consiste nell'usare un singolo metodo di estensione `Add{SERVICE_NAME}` per registrare tutti i servizi richiesti da tale servizio.</span><span class="sxs-lookup"><span data-stu-id="83493-527">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="83493-528">Il codice seguente è un esempio di come aggiungere servizi aggiuntivi al contenitore usando i metodi di estensione [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="83493-528">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="83493-529">Per altre informazioni, vedere la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> nella documentazione delle API.</span><span class="sxs-lookup"><span data-stu-id="83493-529">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="83493-530">Durate del servizio</span><span class="sxs-lookup"><span data-stu-id="83493-530">Service lifetimes</span></span>

<span data-ttu-id="83493-531">Scegliere una durata appropriata per ogni servizio registrato.</span><span class="sxs-lookup"><span data-stu-id="83493-531">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="83493-532">I servizi ASP.NET Core possono essere configurati con le impostazioni di durata seguenti:</span><span class="sxs-lookup"><span data-stu-id="83493-532">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="83493-533">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="83493-533">Transient</span></span>

<span data-ttu-id="83493-534">I servizi con durata temporanea (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) vengono creati ogni volta che vengono richiesti dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="83493-534">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="83493-535">Questa impostazione di durata è consigliata per i servizi semplici senza stati.</span><span class="sxs-lookup"><span data-stu-id="83493-535">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="83493-536">Nelle app che elaborano le richieste, i servizi temporanei vengono eliminati alla fine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="83493-536">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="83493-537">Con ambito</span><span class="sxs-lookup"><span data-stu-id="83493-537">Scoped</span></span>

<span data-ttu-id="83493-538">I servizi con durata con ambito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) vengono creati una volta per ogni richiesta client (connessione).</span><span class="sxs-lookup"><span data-stu-id="83493-538">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="83493-539">Nelle app che elaborano le richieste, i servizi con ambito vengono eliminati alla fine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="83493-539">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="83493-540">Quando si usa un servizio con ambito in un middleware, inserire il servizio nel metodo `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="83493-540">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="83493-541">Non inserire tramite il [Costruttore Injection](xref:mvc/controllers/dependency-injection#constructor-injection) perché impone al servizio di comportarsi come un singleton.</span><span class="sxs-lookup"><span data-stu-id="83493-541">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="83493-542">Per altre informazioni, vedere <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="83493-542">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="83493-543">Singleton</span><span class="sxs-lookup"><span data-stu-id="83493-543">Singleton</span></span>

<span data-ttu-id="83493-544">I servizi con durata singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) vengono creati la prima volta che vengono richiesti (o quando si esegue `Startup.ConfigureServices` e viene specificata un'istanza con la registrazione del servizio).</span><span class="sxs-lookup"><span data-stu-id="83493-544">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="83493-545">Tutte le richieste successive usano la stessa istanza.</span><span class="sxs-lookup"><span data-stu-id="83493-545">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="83493-546">Se l'app richiede un comportamento singleton, è consigliabile consentire al contenitore del servizio di gestire la durata del servizio.</span><span class="sxs-lookup"><span data-stu-id="83493-546">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="83493-547">Non implementare lo schema progettuale singleton e fornire codice utente per gestire la durata dell'oggetto nella classe.</span><span class="sxs-lookup"><span data-stu-id="83493-547">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="83493-548">Nelle app che elaborano le richieste, i servizi Singleton vengono eliminati quando il <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> viene eliminato alla chiusura dell'app.</span><span class="sxs-lookup"><span data-stu-id="83493-548">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="83493-549">Può essere pericoloso risolvere un servizio con ambito da un singleton.</span><span class="sxs-lookup"><span data-stu-id="83493-549">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="83493-550">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="83493-550">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="83493-551">Metodi di registrazione del servizio</span><span class="sxs-lookup"><span data-stu-id="83493-551">Service registration methods</span></span>

<span data-ttu-id="83493-552">I metodi di estensione per la registrazione del servizio offrono overload che risultano utili in scenari specifici.</span><span class="sxs-lookup"><span data-stu-id="83493-552">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="83493-553">Metodo</span><span class="sxs-lookup"><span data-stu-id="83493-553">Method</span></span> | <span data-ttu-id="83493-554">Automatico</span><span class="sxs-lookup"><span data-stu-id="83493-554">Automatic</span></span><br><span data-ttu-id="83493-555">object</span><span class="sxs-lookup"><span data-stu-id="83493-555">object</span></span><br><span data-ttu-id="83493-556">eliminazione</span><span class="sxs-lookup"><span data-stu-id="83493-556">disposal</span></span> | <span data-ttu-id="83493-557">Più elementi</span><span class="sxs-lookup"><span data-stu-id="83493-557">Multiple</span></span><br><span data-ttu-id="83493-558">implementazioni</span><span class="sxs-lookup"><span data-stu-id="83493-558">implementations</span></span> | <span data-ttu-id="83493-559">Pass args</span><span class="sxs-lookup"><span data-stu-id="83493-559">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="83493-560">Esempio:</span><span class="sxs-lookup"><span data-stu-id="83493-560">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="83493-561">Sì</span><span class="sxs-lookup"><span data-stu-id="83493-561">Yes</span></span> | <span data-ttu-id="83493-562">Sì</span><span class="sxs-lookup"><span data-stu-id="83493-562">Yes</span></span> | <span data-ttu-id="83493-563">No</span><span class="sxs-lookup"><span data-stu-id="83493-563">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="83493-564">Esempi:</span><span class="sxs-lookup"><span data-stu-id="83493-564">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="83493-565">Sì</span><span class="sxs-lookup"><span data-stu-id="83493-565">Yes</span></span> | <span data-ttu-id="83493-566">Sì</span><span class="sxs-lookup"><span data-stu-id="83493-566">Yes</span></span> | <span data-ttu-id="83493-567">Sì</span><span class="sxs-lookup"><span data-stu-id="83493-567">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="83493-568">Esempio:</span><span class="sxs-lookup"><span data-stu-id="83493-568">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="83493-569">Sì</span><span class="sxs-lookup"><span data-stu-id="83493-569">Yes</span></span> | <span data-ttu-id="83493-570">No</span><span class="sxs-lookup"><span data-stu-id="83493-570">No</span></span> | <span data-ttu-id="83493-571">No</span><span class="sxs-lookup"><span data-stu-id="83493-571">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="83493-572">Esempi:</span><span class="sxs-lookup"><span data-stu-id="83493-572">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="83493-573">No</span><span class="sxs-lookup"><span data-stu-id="83493-573">No</span></span> | <span data-ttu-id="83493-574">Sì</span><span class="sxs-lookup"><span data-stu-id="83493-574">Yes</span></span> | <span data-ttu-id="83493-575">Sì</span><span class="sxs-lookup"><span data-stu-id="83493-575">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="83493-576">Esempi:</span><span class="sxs-lookup"><span data-stu-id="83493-576">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="83493-577">No</span><span class="sxs-lookup"><span data-stu-id="83493-577">No</span></span> | <span data-ttu-id="83493-578">No</span><span class="sxs-lookup"><span data-stu-id="83493-578">No</span></span> | <span data-ttu-id="83493-579">Sì</span><span class="sxs-lookup"><span data-stu-id="83493-579">Yes</span></span> |

<span data-ttu-id="83493-580">Per ulteriori informazioni sull'eliminazione dei tipi, vedere la sezione [Eliminazione dei servizi](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="83493-580">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="83493-581">Uno scenario comune per più implementazioni è costituito dai [tipi di simulazioni per i test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="83493-581">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="83493-582">I metodi `TryAdd{LIFETIME}` registrano il servizio solo se esiste già un'implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="83493-582">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="83493-583">Nell'esempio seguente, la prima riga registra `MyDependency` per `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="83493-583">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="83493-584">La seconda riga non ha alcun effetto perché `IMyDependency` dispone già di un'implementazione registrata:</span><span class="sxs-lookup"><span data-stu-id="83493-584">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="83493-585">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="83493-585">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="83493-586">I metodi [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrano il servizio solo se non esiste già un'implementazione *dello stesso tipo*.</span><span class="sxs-lookup"><span data-stu-id="83493-586">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="83493-587">Più servizi vengono risolti tramite `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="83493-587">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="83493-588">Durante la registrazione di servizi, lo sviluppatore desidera aggiungere un'istanza solo se non ne è già stata aggiunta una dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="83493-588">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="83493-589">In genere, questo metodo viene utilizzato dagli autori di librerie per evitare di registrare due copie di un'istanza nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="83493-589">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="83493-590">Nell'esempio seguente, la prima riga registra `MyDep` per `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="83493-590">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="83493-591">La seconda riga registra `MyDep` per `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="83493-591">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="83493-592">La seconda riga non ha alcun effetto perché `IMyDep1` dispone già di un'implementazione registrata di `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="83493-592">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="83493-593">Comportamento dell'inserimento del costruttore</span><span class="sxs-lookup"><span data-stu-id="83493-593">Constructor injection behavior</span></span>

<span data-ttu-id="83493-594">I servizi possono essere risolti con due meccanismi:</span><span class="sxs-lookup"><span data-stu-id="83493-594">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="83493-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Consente la creazione di oggetti senza registrazione del servizio nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="83493-596">`ActivatorUtilities` viene usato con astrazioni esposte all'utente, ad esempio helper tag, controller MVC e strumenti di associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="83493-596">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="83493-597">I costruttori possono accettare argomenti non forniti tramite l'inserimento di dipendenze, ma gli argomenti devono assegnare valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="83493-597">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="83493-598">Quando i servizi vengono risolti da `IServiceProvider` o `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede un costruttore *pubblico* .</span><span class="sxs-lookup"><span data-stu-id="83493-598">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="83493-599">Quando i servizi vengono risolti da `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede l'esistenza di un solo costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="83493-599">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="83493-600">Sebbene siano supportati gli overload dei costruttori, può essere presente un solo overload i cui argomenti possono essere tutti specificati tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-600">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="83493-601">Contesti di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="83493-601">Entity Framework contexts</span></span>

<span data-ttu-id="83493-602">I contesti di Entity Framework vengono in genere aggiunti al contenitore di servizi usando la [durata con ambito](#service-lifetimes) perché le operazioni di database delle app Web hanno di solito come ambito la richiesta client.</span><span class="sxs-lookup"><span data-stu-id="83493-602">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="83493-603">L'ambito della durata predefinita è se una durata non è specificata da un overload [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) durante la registrazione del contesto del database.</span><span class="sxs-lookup"><span data-stu-id="83493-603">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="83493-604">I servizi con una durata specificata non devono usare un contesto di database con una durata più breve rispetto al servizio.</span><span class="sxs-lookup"><span data-stu-id="83493-604">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="83493-605">Opzioni di durata e di registrazione</span><span class="sxs-lookup"><span data-stu-id="83493-605">Lifetime and registration options</span></span>

<span data-ttu-id="83493-606">Per illustrare la differenza tra le opzioni di durata e registrazione, si considerino le interfacce seguenti che rappresentano le attività come un'operazione con un identificatore univoco, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="83493-606">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="83493-607">A seconda del modo in cui la durata di un servizio operativo viene configurata per le interfacce seguenti, il contenitore fornisce la stessa istanza o un'istanza diversa del servizio quando richiesto da una classe:</span><span class="sxs-lookup"><span data-stu-id="83493-607">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="83493-608">Le interfacce vengono implementate nella classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="83493-608">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="83493-609">Il costruttore `Operation` genera un GUID se non ne viene fornito uno:</span><span class="sxs-lookup"><span data-stu-id="83493-609">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="83493-610">Viene registrato un `OperationService` che dipende da ognuno degli altri tipi `Operation`.</span><span class="sxs-lookup"><span data-stu-id="83493-610">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="83493-611">Quando viene richiesto `OperationService` tramite l'inserimento delle dipendenze, riceve una nuova istanza di ogni servizio o un'istanza esistente in base alla durata del servizio dipendente.</span><span class="sxs-lookup"><span data-stu-id="83493-611">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="83493-612">Se i servizi temporanei vengono creati quando vengono richiesti dal contenitore, `OperationId` del servizio `IOperationTransient` è diverso da `OperationId` di `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="83493-612">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="83493-613">`OperationService` riceve una nuova istanza della classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="83493-613">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="83493-614">La nuova istanza restituisce un `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="83493-614">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="83493-615">Se i servizi con ambito vengono creati per ogni richiesta client, `OperationId` del servizio `IOperationScoped` corrisponde a `OperationService` all'interno di una richiesta client.</span><span class="sxs-lookup"><span data-stu-id="83493-615">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="83493-616">In tutte le richieste client entrambi i servizi condividono un valore `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="83493-616">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="83493-617">Se i servizi singleton e con istanza singleton vengono creati una sola volta e usati per tutte le richieste client e tutti i servizi, `OperationId` rimane costante in tutte le richieste di servizio.</span><span class="sxs-lookup"><span data-stu-id="83493-617">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="83493-618">In `Startup.ConfigureServices`, ogni tipo viene aggiunto al contenitore in base alla durata denominata:</span><span class="sxs-lookup"><span data-stu-id="83493-618">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="83493-619">Il servizio `IOperationSingletonInstance` usa un'istanza specifica con un ID noto di `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="83493-619">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="83493-620">È chiaro quando questo tipo è in uso (il relativo GUID è composto da tutti zero).</span><span class="sxs-lookup"><span data-stu-id="83493-620">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="83493-621">L'app di esempio dimostra le durate degli oggetti all'interno e tra le singole richieste.</span><span class="sxs-lookup"><span data-stu-id="83493-621">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="83493-622">L'`IndexModel` dell'app di esempio richiede ogni genere di tipo `IOperation` e `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="83493-622">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="83493-623">La pagina visualizza quindi tutti i valori `OperationId` della classe del modello di pagina e del servizio tramite assegnazioni di proprietà:</span><span class="sxs-lookup"><span data-stu-id="83493-623">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="83493-624">L'output seguente mostra i risultati delle due richieste:</span><span class="sxs-lookup"><span data-stu-id="83493-624">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="83493-625">**Prima richiesta:**</span><span class="sxs-lookup"><span data-stu-id="83493-625">**First request:**</span></span>

<span data-ttu-id="83493-626">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="83493-626">Controller operations:</span></span>

<span data-ttu-id="83493-627">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="83493-627">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="83493-628">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="83493-628">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="83493-629">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="83493-629">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="83493-630">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="83493-630">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="83493-631">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="83493-631">`OperationService` operations:</span></span>

<span data-ttu-id="83493-632">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="83493-632">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="83493-633">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="83493-633">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="83493-634">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="83493-634">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="83493-635">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="83493-635">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="83493-636">**Seconda richiesta:**</span><span class="sxs-lookup"><span data-stu-id="83493-636">**Second request:**</span></span>

<span data-ttu-id="83493-637">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="83493-637">Controller operations:</span></span>

<span data-ttu-id="83493-638">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="83493-638">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="83493-639">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="83493-639">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="83493-640">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="83493-640">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="83493-641">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="83493-641">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="83493-642">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="83493-642">`OperationService` operations:</span></span>

<span data-ttu-id="83493-643">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="83493-643">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="83493-644">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="83493-644">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="83493-645">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="83493-645">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="83493-646">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="83493-646">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="83493-647">Osservare i valori `OperationId` che variano all'interno della richiesta e da una richiesta e l'altra:</span><span class="sxs-lookup"><span data-stu-id="83493-647">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="83493-648">Gli oggetti *temporanei* sono sempre diversi.</span><span class="sxs-lookup"><span data-stu-id="83493-648">*Transient* objects are always different.</span></span> <span data-ttu-id="83493-649">Il valore `OperationId` temporaneo per la prima e la seconda richiesta client è diverso per entrambe le operazioni `OperationService` e in tutte le richieste client.</span><span class="sxs-lookup"><span data-stu-id="83493-649">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="83493-650">Viene fornita una nuova istanza per ogni richiesta di servizio e richiesta client.</span><span class="sxs-lookup"><span data-stu-id="83493-650">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="83493-651">Gli oggetti *con ambito* sono gli stessi all'interno di una richiesta client, ma variano nelle diverse richieste client.</span><span class="sxs-lookup"><span data-stu-id="83493-651">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="83493-652">Gli oggetti *singleton* sono gli stessi per ogni oggetto e per ogni richiesta, indipendentemente dal fatto che un' `Operation` istanza venga fornita in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="83493-652">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="83493-653">Chiamare i servizi da main</span><span class="sxs-lookup"><span data-stu-id="83493-653">Call services from main</span></span>

<span data-ttu-id="83493-654">Creare un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>IServiceScope con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) per risolvere un servizio con ambito nell'ambito di applicazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="83493-654">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="83493-655">Questo approccio è utile per l'accesso a un servizio con ambito all'avvio e per l'esecuzione di attività di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="83493-655">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="83493-656">L'esempio seguente indica come ottenere un contesto per `MyScopedService` in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="83493-656">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="83493-657">Convalida dell'ambito</span><span class="sxs-lookup"><span data-stu-id="83493-657">Scope validation</span></span>

<span data-ttu-id="83493-658">Quando l'app viene eseguita nell'ambiente di sviluppo, il provider di servizi predefinito esegue controlli per verificare che:</span><span class="sxs-lookup"><span data-stu-id="83493-658">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="83493-659">I servizi con ambito non vengano risolti direttamente o indirettamente dal provider di servizi radice.</span><span class="sxs-lookup"><span data-stu-id="83493-659">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="83493-660">I servizi con ambito non vengano inseriti direttamente o indirettamente in singleton.</span><span class="sxs-lookup"><span data-stu-id="83493-660">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="83493-661">Il provider di servizi radice venga creato con la chiamata di <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="83493-661">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="83493-662">La durata del provider di servizi radice corrisponde alla durata dell'app/server quando il provider viene avviato con l'app e viene eliminato alla chiusura dell'app.</span><span class="sxs-lookup"><span data-stu-id="83493-662">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="83493-663">I servizi con ambito vengono eliminati dal contenitore che li ha creati.</span><span class="sxs-lookup"><span data-stu-id="83493-663">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="83493-664">Se un servizio con ambito viene creato nel contenitore radice, la durata del servizio viene di fatto convertita in singleton, perché il servizio viene eliminato solo dal contenitore radice alla chiusura dell'app/server.</span><span class="sxs-lookup"><span data-stu-id="83493-664">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="83493-665">La convalida degli ambiti servizio rileva queste situazioni nel contesto della chiamata di `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="83493-665">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="83493-666">Per altre informazioni, vedere <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="83493-666">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="83493-667">Servizi di richiesta</span><span class="sxs-lookup"><span data-stu-id="83493-667">Request Services</span></span>

<span data-ttu-id="83493-668">I servizi disponibili all'interno di una richiesta ASP.NET Core da `HttpContext` vengono esposti tramite la raccolta [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="83493-668">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="83493-669">I servizi di richiesta rappresentano i servizi configurati e richiesti come parte dell'app.</span><span class="sxs-lookup"><span data-stu-id="83493-669">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="83493-670">Quando gli oggetti specificano dipendenze, le dipendenze sono soddisfatte dai tipi individuati in `RequestServices`, non in `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="83493-670">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="83493-671">In generale, l'app non deve usare queste proprietà direttamente.</span><span class="sxs-lookup"><span data-stu-id="83493-671">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="83493-672">Richiedere invece i tipi richiesti dalle classi tramite i costruttori di classi e consentire al framework di inserire le dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-672">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="83493-673">Si ottengono classi più facili da testare.</span><span class="sxs-lookup"><span data-stu-id="83493-673">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="83493-674">È consigliabile richiedere le dipendenze come parametri del costruttore per l'accesso alla raccolta `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="83493-674">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="83493-675">Progettare i servizi per l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="83493-675">Design services for dependency injection</span></span>

<span data-ttu-id="83493-676">Le procedure consigliate prevedono di:</span><span class="sxs-lookup"><span data-stu-id="83493-676">Best practices are to:</span></span>

* <span data-ttu-id="83493-677">Progettare i servizi per usare l'inserimento delle dipendenze per ottenere le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-677">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="83493-678">Evitare membri e classi statiche con stato.</span><span class="sxs-lookup"><span data-stu-id="83493-678">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="83493-679">Progettare le app in modo da usare i servizi singleton, evitando così di creare uno stato globale.</span><span class="sxs-lookup"><span data-stu-id="83493-679">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="83493-680">Evitare la creazione diretta di istanze delle classi dipendenti all'interno di servizi.</span><span class="sxs-lookup"><span data-stu-id="83493-680">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="83493-681">La creazione diretta di istanze associa il codice a una determinata implementazione.</span><span class="sxs-lookup"><span data-stu-id="83493-681">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="83493-682">Fare in modo che le classi siano piccole, con factoring corretto e facili da testare.</span><span class="sxs-lookup"><span data-stu-id="83493-682">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="83493-683">Se una classe sembra avere troppe dipendenze inserite, ciò significa in genere che la classe ha un numero eccessivo di responsabilità e sta violando il [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (principio di responsabilità singola).</span><span class="sxs-lookup"><span data-stu-id="83493-683">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="83493-684">Tentare di effettuare il refactoring della classe spostando alcune delle responsabilità in una nuova classe.</span><span class="sxs-lookup"><span data-stu-id="83493-684">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="83493-685">Tenere presente che Razor le classi del modello della pagina delle pagine e le classi controller MVC devono concentrarsi sulle problematiche dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="83493-685">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="83493-686">Di conseguenza, le regole business e i dettagli di implementazione di accesso ai dati devono essere inseriti in classi appropriate per queste [problematiche separate](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="83493-686">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="83493-687">Eliminazione dei servizi</span><span class="sxs-lookup"><span data-stu-id="83493-687">Disposal of services</span></span>

<span data-ttu-id="83493-688">Il contenitore chiama <xref:System.IDisposable.Dispose*> per i tipi <xref:System.IDisposable> creati.</span><span class="sxs-lookup"><span data-stu-id="83493-688">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="83493-689">Se un'istanza viene aggiunta al contenitore dal codice utente, non viene eliminata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="83493-689">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="83493-690">Nell'esempio seguente i servizi vengono creati dal contenitore del servizio ed eliminati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="83493-690">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="83493-691">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="83493-691">In the following example:</span></span>

* <span data-ttu-id="83493-692">Le istanze del servizio non vengono create dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="83493-692">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="83493-693">Le durate di servizio previste non sono note dal Framework.</span><span class="sxs-lookup"><span data-stu-id="83493-693">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="83493-694">Il Framework non elimina automaticamente i servizi.</span><span class="sxs-lookup"><span data-stu-id="83493-694">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="83493-695">Se i servizi non vengono eliminati in modo esplicito nel codice dello sviluppatore, vengono mantenuti fino a quando l'app non viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="83493-695">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="83493-696">Linee guida IDisposable per istanze temporanee e condivise</span><span class="sxs-lookup"><span data-stu-id="83493-696">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="83493-697">Durata temporanea e limitata</span><span class="sxs-lookup"><span data-stu-id="83493-697">Transient, limited lifetime</span></span>

<span data-ttu-id="83493-698">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="83493-698">**Scenario**</span></span>

<span data-ttu-id="83493-699">L'app richiede un' <xref:System.IDisposable> istanza con una durata temporanea per uno degli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="83493-699">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="83493-700">L'istanza viene risolta nell'ambito radice.</span><span class="sxs-lookup"><span data-stu-id="83493-700">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="83493-701">L'istanza deve essere eliminata prima della fine dell'ambito.</span><span class="sxs-lookup"><span data-stu-id="83493-701">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="83493-702">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="83493-702">**Solution**</span></span>

<span data-ttu-id="83493-703">Usare il modello factory per creare un'istanza all'esterno dell'ambito padre.</span><span class="sxs-lookup"><span data-stu-id="83493-703">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="83493-704">In questa situazione, in genere l'app dispone di un `Create` metodo che chiama direttamente il costruttore del tipo finale.</span><span class="sxs-lookup"><span data-stu-id="83493-704">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="83493-705">Se il tipo finale ha altre dipendenze, la factory può:</span><span class="sxs-lookup"><span data-stu-id="83493-705">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="83493-706">Ricevere un oggetto <xref:System.IServiceProvider> nel relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="83493-706">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="83493-707">Usare <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> per creare un'istanza dell'istanza all'esterno del contenitore, usando il contenitore per le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-707">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="83493-708">Istanza condivisa, durata limitata</span><span class="sxs-lookup"><span data-stu-id="83493-708">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="83493-709">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="83493-709">**Scenario**</span></span>

<span data-ttu-id="83493-710">L'app richiede un' <xref:System.IDisposable> istanza condivisa tra più servizi, ma <xref:System.IDisposable> deve avere una durata limitata.</span><span class="sxs-lookup"><span data-stu-id="83493-710">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="83493-711">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="83493-711">**Solution**</span></span>

<span data-ttu-id="83493-712">Registrare l'istanza con una durata con ambito.</span><span class="sxs-lookup"><span data-stu-id="83493-712">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="83493-713">Usare <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> per avviare e creare un nuovo oggetto <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="83493-713">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="83493-714">Usare l'ambito <xref:System.IServiceProvider> per ottenere i servizi richiesti.</span><span class="sxs-lookup"><span data-stu-id="83493-714">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="83493-715">Eliminare l'ambito quando la durata deve terminare.</span><span class="sxs-lookup"><span data-stu-id="83493-715">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="83493-716">Linee guida generali</span><span class="sxs-lookup"><span data-stu-id="83493-716">General Guidelines</span></span>

* <span data-ttu-id="83493-717">Non registrare <xref:System.IDisposable> istanze con un ambito temporaneo.</span><span class="sxs-lookup"><span data-stu-id="83493-717">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="83493-718">Usare invece il modello Factory.</span><span class="sxs-lookup"><span data-stu-id="83493-718">Use the factory pattern instead.</span></span>
* <span data-ttu-id="83493-719">Non risolvere istanze temporanee o <xref:System.IDisposable> con ambito nell'ambito radice.</span><span class="sxs-lookup"><span data-stu-id="83493-719">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="83493-720">L'unica eccezione generale si verifica quando l'app crea/ricrea ed Elimina <xref:System.IServiceProvider> , che non è un modello ideale.</span><span class="sxs-lookup"><span data-stu-id="83493-720">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="83493-721">Per <xref:System.IDisposable> la ricezione di una dipendenza tramite di non è necessario che il ricevitore implementi <xref:System.IDisposable> se stesso.</span><span class="sxs-lookup"><span data-stu-id="83493-721">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="83493-722">Il ricevitore della <xref:System.IDisposable> dipendenza non deve chiamare <xref:System.IDisposable.Dispose%2A> su tale dipendenza.</span><span class="sxs-lookup"><span data-stu-id="83493-722">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="83493-723">Gli ambiti devono essere usati per controllare la durata dei servizi.</span><span class="sxs-lookup"><span data-stu-id="83493-723">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="83493-724">Gli ambiti non sono gerarchici e non esiste alcuna connessione speciale tra gli ambiti.</span><span class="sxs-lookup"><span data-stu-id="83493-724">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="83493-725">Sostituzione del contenitore di servizi predefinito</span><span class="sxs-lookup"><span data-stu-id="83493-725">Default service container replacement</span></span>

<span data-ttu-id="83493-726">Il contenitore dei servizi incorporato è progettato per soddisfare le esigenze del Framework e della maggior parte delle app consumer.</span><span class="sxs-lookup"><span data-stu-id="83493-726">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="83493-727">Si consiglia di usare il contenitore predefinito a meno che non sia necessaria una funzionalità specifica che il contenitore incorporato non supporta, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="83493-727">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="83493-728">Inserimento di proprietà</span><span class="sxs-lookup"><span data-stu-id="83493-728">Property injection</span></span>
* <span data-ttu-id="83493-729">Inserimento in base al nome</span><span class="sxs-lookup"><span data-stu-id="83493-729">Injection based on name</span></span>
* <span data-ttu-id="83493-730">Contenitori figlio</span><span class="sxs-lookup"><span data-stu-id="83493-730">Child containers</span></span>
* <span data-ttu-id="83493-731">Gestione della durata personalizzata</span><span class="sxs-lookup"><span data-stu-id="83493-731">Custom lifetime management</span></span>
* <span data-ttu-id="83493-732">Supporto di `Func<T>` per l'inizializzazione differita</span><span class="sxs-lookup"><span data-stu-id="83493-732">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="83493-733">Registrazione basata sulle convenzioni</span><span class="sxs-lookup"><span data-stu-id="83493-733">Convention-based registration</span></span>

<span data-ttu-id="83493-734">Con ASP.NET Core app è possibile usare i contenitori di terze parti seguenti:</span><span class="sxs-lookup"><span data-stu-id="83493-734">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="83493-735">Autofac</span><span class="sxs-lookup"><span data-stu-id="83493-735">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="83493-736">DryIoc</span><span class="sxs-lookup"><span data-stu-id="83493-736">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="83493-737">Laura</span><span class="sxs-lookup"><span data-stu-id="83493-737">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="83493-738">LightInject</span><span class="sxs-lookup"><span data-stu-id="83493-738">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="83493-739">Lamar</span><span class="sxs-lookup"><span data-stu-id="83493-739">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="83493-740">Stashbox</span><span class="sxs-lookup"><span data-stu-id="83493-740">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="83493-741">Unity</span><span class="sxs-lookup"><span data-stu-id="83493-741">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="83493-742">Thread safety</span><span class="sxs-lookup"><span data-stu-id="83493-742">Thread safety</span></span>

<span data-ttu-id="83493-743">Creare servizi singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="83493-743">Create thread-safe singleton services.</span></span> <span data-ttu-id="83493-744">Se un servizio singleton ha una dipendenza in un servizio temporaneo, è necessario che anche il servizio temporaneo sia thread-safe, a seconda di come viene usato dal singleton.</span><span class="sxs-lookup"><span data-stu-id="83493-744">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="83493-745">Il metodo factory di un singolo servizio, ad esempio il secondo argomento di [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), non deve essere thread-safe.</span><span class="sxs-lookup"><span data-stu-id="83493-745">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="83493-746">Come nel caso di un costruttore di tipo (`static`), è garantito che venga chiamato una sola volta da un singolo thread.</span><span class="sxs-lookup"><span data-stu-id="83493-746">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="83493-747">Consigli</span><span class="sxs-lookup"><span data-stu-id="83493-747">Recommendations</span></span>

* <span data-ttu-id="83493-748">La risoluzione basata sui servizi `async/await` e `Task` non è supportata.</span><span class="sxs-lookup"><span data-stu-id="83493-748">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="83493-749">Dato che C# non supporta i costruttori asincroni, il modello consigliato consiste nell'usare i metodi asincroni dopo avere risolto in modo sincrono il servizio.</span><span class="sxs-lookup"><span data-stu-id="83493-749">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="83493-750">Evitare di archiviare i dati e la configurazione direttamente nel contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="83493-750">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="83493-751">Ad esempio, il carrello acquisti di un utente non dovrebbe in genere essere aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="83493-751">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="83493-752">La configurazione deve usare il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="83493-752">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="83493-753">Analogamente, evitare gli oggetti "contenitori di dati" che hanno la sola funzione di consentire l'accesso ad altri oggetti.</span><span class="sxs-lookup"><span data-stu-id="83493-753">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="83493-754">È preferibile richiedere l'elemento effettivo tramite inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83493-754">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="83493-755">Evitare l'accesso statico ai servizi.</span><span class="sxs-lookup"><span data-stu-id="83493-755">Avoid static access to services.</span></span> <span data-ttu-id="83493-756">Ad esempio, evitare di digitare in modo statico [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) per l'uso altrove.</span><span class="sxs-lookup"><span data-stu-id="83493-756">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="83493-757">Evitare di usare il *modello del localizzatore di servizi*, che combina [inversione delle strategie di controllo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="83493-757">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="83493-758">Non richiamare <xref:System.IServiceProvider.GetService*> per ottenere un'istanza del servizio quando si può usare invece di:</span><span class="sxs-lookup"><span data-stu-id="83493-758">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="83493-759">**Non corretto:**</span><span class="sxs-lookup"><span data-stu-id="83493-759">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    <span data-ttu-id="83493-760">**Corretto**:</span><span class="sxs-lookup"><span data-stu-id="83493-760">**Correct**:</span></span>

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

* <span data-ttu-id="83493-761">Evitare di inserire una factory che risolve le dipendenze in fase di esecuzione usando <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="83493-761">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="83493-762">Evitare l'accesso statico a `HttpContext` (ad esempio [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="83493-762">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="83493-763">È tuttavia possibile che in alcuni casi queste raccomandazioni debbano essere ignorate.</span><span class="sxs-lookup"><span data-stu-id="83493-763">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="83493-764">Le eccezioni sono rari, soprattutto casi speciali all'interno del Framework stesso.</span><span class="sxs-lookup"><span data-stu-id="83493-764">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="83493-765">L'inserimento di dipendenze è un'*alternativa* ai modelli di accesso agli oggetti statici/globali.</span><span class="sxs-lookup"><span data-stu-id="83493-765">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="83493-766">Se l'inserimento di dipendenze viene usato con l'accesso agli oggetti statico i vantaggi non saranno evidenti.</span><span class="sxs-lookup"><span data-stu-id="83493-766">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="83493-767">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="83493-767">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="83493-768">Quattro modi per eliminare IDisposable in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83493-768">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="83493-769">Scrittura di codice pulito in ASP.NET Core con inserimento delle dipendenze (MSDN)</span><span class="sxs-lookup"><span data-stu-id="83493-769">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="83493-770">Explicit Dependencies Principle (Principio delle dipendenze esplicite)</span><span class="sxs-lookup"><span data-stu-id="83493-770">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="83493-771">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler) (Contenitori di inversione del controllo e modello di inserimento delle dipendenze)</span><span class="sxs-lookup"><span data-stu-id="83493-771">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="83493-772">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Come registrare un servizio con più interfacce in ASP.NET Core DI)</span><span class="sxs-lookup"><span data-stu-id="83493-772">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
