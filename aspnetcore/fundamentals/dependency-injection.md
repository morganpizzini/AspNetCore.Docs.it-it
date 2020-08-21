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
ms.openlocfilehash: 0a51647463362d6cfac335688d42d4be013f8b9c
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712513"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="f22ce-103">Inserimento delle dipendenze in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f22ce-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f22ce-104">Di [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="f22ce-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="f22ce-105">ASP.NET Core supporta lo schema progettuale di software per l'inserimento di dipendenze, una tecnica per ottenere l'[IoC (Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra le classi e le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="f22ce-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="f22ce-106">Per altre informazioni specifiche per l'inserimento delle dipendenze all'interno di controller MVC, vedere <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="f22ce-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="f22ce-107">Per ulteriori informazioni sull'inserimento delle dipendenze delle opzioni, vedere <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="f22ce-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="f22ce-108">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f22ce-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="f22ce-109">Panoramica dell'inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="f22ce-109">Overview of dependency injection</span></span>

<span data-ttu-id="f22ce-110">Una *dipendenza* è un oggetto da cui dipende un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="f22ce-110">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="f22ce-111">Esaminare la `MyDependency` classe seguente con un `WriteMessage` metodo da cui dipendono altre classi:</span><span class="sxs-lookup"><span data-stu-id="f22ce-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="f22ce-112">Una classe può creare un'istanza della `MyDependency` classe per utilizzare il relativo `WriteMessage` metodo.</span><span class="sxs-lookup"><span data-stu-id="f22ce-112">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="f22ce-113">Nell'esempio seguente, la `MyDependency` classe è una dipendenza della `IndexModel` classe:</span><span class="sxs-lookup"><span data-stu-id="f22ce-113">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage("IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="f22ce-114">La classe crea e dipende direttamente dalla `MyDependency` classe.</span><span class="sxs-lookup"><span data-stu-id="f22ce-114">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="f22ce-115">Le dipendenze del codice, ad esempio nell'esempio precedente, sono problematiche e devono essere evitate per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="f22ce-115">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="f22ce-116">Per sostituire `MyDependency` con un'implementazione diversa, `IndexModel` è necessario modificare la classe.</span><span class="sxs-lookup"><span data-stu-id="f22ce-116">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="f22ce-117">Se `MyDependency` dispone di dipendenze, devono anche essere configurate dalla `IndexModel` classe.</span><span class="sxs-lookup"><span data-stu-id="f22ce-117">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="f22ce-118">In un progetto di grandi dimensioni con più classi che dipendono da `MyDependency`, il codice di configurazione diventa sparso in tutta l'app.</span><span class="sxs-lookup"><span data-stu-id="f22ce-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="f22ce-119">È difficile eseguire unit test di questa implementazione.</span><span class="sxs-lookup"><span data-stu-id="f22ce-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="f22ce-120">L'app dovrebbe usare una classe `MyDependency` fittizia o stub, ma ciò non è possibile con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="f22ce-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="f22ce-121">L'inserimento delle dipendenze consente di risolvere questi problemi tramite:</span><span class="sxs-lookup"><span data-stu-id="f22ce-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="f22ce-122">L'uso di un'interfaccia o di una classe di base per astrarre l'implementazione delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="f22ce-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="f22ce-123">La registrazione della dipendenza in un contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="f22ce-124">ASP.NET Core offre il contenitore di servizi predefinito <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="f22ce-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="f22ce-125">I servizi vengono in genere registrati nel metodo dell'app `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f22ce-125">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="f22ce-126">L'*inserimento* del servizio nel costruttore della classe in cui viene usato.</span><span class="sxs-lookup"><span data-stu-id="f22ce-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="f22ce-127">Il framework si assume la responsabilità della creazione di un'istanza della dipendenza e della sua eliminazione quando non è più necessaria.</span><span class="sxs-lookup"><span data-stu-id="f22ce-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="f22ce-128">Nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l' `IMyDependency` interfaccia definisce il `WriteMessage` Metodo:</span><span class="sxs-lookup"><span data-stu-id="f22ce-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="f22ce-129">Questa interfaccia viene implementata da un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="f22ce-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="f22ce-130">L'app di esempio registra il `IMyDependency` servizio con il tipo concreto `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="f22ce-130">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="f22ce-131">Il <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> metodo registra il servizio con una durata con ambito, la durata di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="f22ce-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="f22ce-132">Le [durate dei servizi](#service-lifetimes) sono descritte più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="f22ce-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="f22ce-133">Nell'app di esempio, il `IMyDependency` servizio viene richiesto e usato per chiamare il `WriteMessage` Metodo:</span><span class="sxs-lookup"><span data-stu-id="f22ce-133">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="f22ce-134">Utilizzando il modello DI, il controller:</span><span class="sxs-lookup"><span data-stu-id="f22ce-134">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="f22ce-135">Non usa il tipo concreto `MyDependency` , bensì solo l' `IMyDependency` interfaccia che implementa.</span><span class="sxs-lookup"><span data-stu-id="f22ce-135">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="f22ce-136">Questo consente di modificare facilmente l'implementazione utilizzata dal controller senza modificare il controller.</span><span class="sxs-lookup"><span data-stu-id="f22ce-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="f22ce-137">Non crea un'istanza di `MyDependency` , viene creata dal contenitore di.</span><span class="sxs-lookup"><span data-stu-id="f22ce-137">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="f22ce-138">L'implementazione dell' `IMyDependency` interfaccia può essere migliorata usando l'API di registrazione incorporata:</span><span class="sxs-lookup"><span data-stu-id="f22ce-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="f22ce-139">Il `ConfigureServices` metodo aggiornato registra la nuova `IMyDependency` implementazione:</span><span class="sxs-lookup"><span data-stu-id="f22ce-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="f22ce-140">`MyDependency2` dipende da <xref:Microsoft.Extensions.Logging.ILogger%601> , che richiede nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="f22ce-140">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="f22ce-141">`ILogger<TCategoryName>` è un [servizio fornito dal Framework](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="f22ce-141">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="f22ce-142">Non è insolito usare l'inserimento delle dipendenze in modo concatenato.</span><span class="sxs-lookup"><span data-stu-id="f22ce-142">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="f22ce-143">Ogni dipendenza richiesta richiede a sua volta le proprie dipendenze.</span><span class="sxs-lookup"><span data-stu-id="f22ce-143">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="f22ce-144">Il contenitore risolve le dipendenze nel grafico e restituisce il servizio completamente risolto.</span><span class="sxs-lookup"><span data-stu-id="f22ce-144">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="f22ce-145">Il set di dipendenze che devono essere risolte viene generalmente chiamato *albero delle dipendenze* o *grafico dipendenze* o *grafico degli oggetti*.</span><span class="sxs-lookup"><span data-stu-id="f22ce-145">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="f22ce-146">Il contenitore si risolve sfruttando `ILogger<TCategoryName>` i vantaggi dei [tipi aperti (generici)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando la necessità di registrare tutti i tipi [costruiti (generici)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="f22ce-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="f22ce-147">Nella terminologia relativa all'inserimento delle dipendenze, un servizio:</span><span class="sxs-lookup"><span data-stu-id="f22ce-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="f22ce-148">È in genere un oggetto che fornisce un servizio ad altri oggetti, ad esempio il `IMyDependency` servizio.</span><span class="sxs-lookup"><span data-stu-id="f22ce-148">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="f22ce-149">Non è correlato a un servizio Web, anche se il servizio può utilizzare un servizio Web.</span><span class="sxs-lookup"><span data-stu-id="f22ce-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="f22ce-150">Il Framework fornisce un sistema di [registrazione](xref:fundamentals/logging/index) affidabile.</span><span class="sxs-lookup"><span data-stu-id="f22ce-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="f22ce-151">Le `IMyDependency` implementazioni illustrate negli esempi precedenti sono state scritte per illustrare il di base di, non per implementare la registrazione.</span><span class="sxs-lookup"><span data-stu-id="f22ce-151">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="f22ce-152">La maggior parte delle app non deve scrivere logger.</span><span class="sxs-lookup"><span data-stu-id="f22ce-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="f22ce-153">Il codice seguente illustra l'uso della registrazione predefinita, che non richiede la registrazione dei servizi in `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f22ce-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="f22ce-154">Con il codice precedente non è necessario aggiornare `ConfigureServices` , perché la [registrazione](xref:fundamentals/logging/index) viene fornita dal Framework.</span><span class="sxs-lookup"><span data-stu-id="f22ce-154">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="f22ce-155">Servizi inseriti all'avvio</span><span class="sxs-lookup"><span data-stu-id="f22ce-155">Services injected into Startup</span></span>

<span data-ttu-id="f22ce-156">I servizi possono essere inseriti nel `Startup` costruttore e nel `Startup.Configure` metodo.</span><span class="sxs-lookup"><span data-stu-id="f22ce-156">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="f22ce-157">`Startup`Quando si utilizza l'host generico () è possibile inserire nel costruttore solo i servizi seguenti <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="f22ce-157">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="f22ce-158">Qualsiasi servizio registrato con il contenitore DI inserimento delle dipendenze può essere inserito nel `Startup.Configure` Metodo:</span><span class="sxs-lookup"><span data-stu-id="f22ce-158">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="f22ce-159">Per ulteriori informazioni, vedere <xref:fundamentals/startup> e [accedere alla configurazione all'avvio](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="f22ce-159">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="f22ce-160">Registrare gruppi di servizi con i metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="f22ce-160">Register groups of services with extension methods</span></span>

<span data-ttu-id="f22ce-161">Il framework ASP.NET Core usa una convenzione per la registrazione di un gruppo di servizi correlati.</span><span class="sxs-lookup"><span data-stu-id="f22ce-161">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="f22ce-162">La convenzione prevede l'uso di un singolo `Add{GROUP_NAME}` metodo di estensione per registrare tutti i servizi richiesti da una funzionalità del Framework.</span><span class="sxs-lookup"><span data-stu-id="f22ce-162">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="f22ce-163">Ad esempio, il metodo <Microsoft. Extensions. DependencyInjection. MvcServiceCollectionExtensions. AddControllers> Extension registra i servizi necessari per i controller MVC.</span><span class="sxs-lookup"><span data-stu-id="f22ce-163">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="f22ce-164">Il codice seguente viene generato dal Razor modello di pagine usando singoli account utente e Mostra come aggiungere altri servizi al contenitore usando i metodi di estensione <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="f22ce-164">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="f22ce-165">Durate del servizio</span><span class="sxs-lookup"><span data-stu-id="f22ce-165">Service lifetimes</span></span>

<span data-ttu-id="f22ce-166">I servizi possono essere registrati con una delle seguenti durate:</span><span class="sxs-lookup"><span data-stu-id="f22ce-166">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="f22ce-167">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="f22ce-167">Transient</span></span>
* <span data-ttu-id="f22ce-168">Con ambito</span><span class="sxs-lookup"><span data-stu-id="f22ce-168">Scoped</span></span>
* <span data-ttu-id="f22ce-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-169">Singleton</span></span>

<span data-ttu-id="f22ce-170">Nelle sezioni seguenti vengono descritte tutte le durate precedenti.</span><span class="sxs-lookup"><span data-stu-id="f22ce-170">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="f22ce-171">Scegliere una durata appropriata per ogni servizio registrato.</span><span class="sxs-lookup"><span data-stu-id="f22ce-171">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="f22ce-172">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="f22ce-172">Transient</span></span>

<span data-ttu-id="f22ce-173">I servizi con durata temporanea vengono creati ogni volta che vengono richiesti dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-173">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="f22ce-174">Questa impostazione di durata è consigliata per i servizi semplici senza stati.</span><span class="sxs-lookup"><span data-stu-id="f22ce-174">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="f22ce-175">Registrare servizi temporanei con <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> .</span><span class="sxs-lookup"><span data-stu-id="f22ce-175">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="f22ce-176">Nelle app che elaborano le richieste, i servizi temporanei vengono eliminati alla fine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="f22ce-176">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="f22ce-177">Con ambito</span><span class="sxs-lookup"><span data-stu-id="f22ce-177">Scoped</span></span>

<span data-ttu-id="f22ce-178">I servizi con durata con ambito vengono creati una volta per ogni richiesta client (connessione).</span><span class="sxs-lookup"><span data-stu-id="f22ce-178">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="f22ce-179">Registrare i servizi con ambito con <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> .</span><span class="sxs-lookup"><span data-stu-id="f22ce-179">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="f22ce-180">Nelle app che elaborano le richieste, i servizi con ambito vengono eliminati alla fine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="f22ce-180">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="f22ce-181">Quando si usa Entity Framework Core, il <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> metodo di estensione registra i `DbContext` tipi con una durata con ambito per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="f22ce-181">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="f22ce-182">***Non*** risolvere un servizio con ambito da un singleton.</span><span class="sxs-lookup"><span data-stu-id="f22ce-182">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="f22ce-183">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="f22ce-183">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="f22ce-184">È bene:</span><span class="sxs-lookup"><span data-stu-id="f22ce-184">It's fine to:</span></span>

* <span data-ttu-id="f22ce-185">Risolvere un servizio singleton da un servizio con ambito o temporaneo.</span><span class="sxs-lookup"><span data-stu-id="f22ce-185">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="f22ce-186">Risolvere un servizio con ambito da un altro servizio con ambito o temporaneo.</span><span class="sxs-lookup"><span data-stu-id="f22ce-186">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="f22ce-187">Per impostazione predefinita, nell'ambiente di sviluppo la risoluzione di un servizio da un altro servizio con una durata più lunga genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="f22ce-187">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="f22ce-188">Per ulteriori informazioni, vedere [Convalida dell'ambito](#sv).</span><span class="sxs-lookup"><span data-stu-id="f22ce-188">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="f22ce-189">Per usare i servizi con ambito nel middleware, usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="f22ce-189">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="f22ce-190">Inserire il servizio nel middleware o nel `Invoke` `InvokeAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="f22ce-190">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="f22ce-191">L'uso dell' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) genera un'eccezione in fase di esecuzione perché impone il comportamento del servizio con ambito come un singleton.</span><span class="sxs-lookup"><span data-stu-id="f22ce-191">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="f22ce-192">Nell'esempio nella sezione relativa alle [Opzioni di durata e registrazione](#lifetime-and-registration-options) viene illustrato l' `InvokeAsync` approccio.</span><span class="sxs-lookup"><span data-stu-id="f22ce-192">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="f22ce-193">Usare il [middleware basato su Factory](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="f22ce-193">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="f22ce-194">Il middleware registrato con questo approccio viene attivato per ogni richiesta client (connessione), che consente di inserire i servizi con ambito nel metodo del middleware `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="f22ce-194">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="f22ce-195">Per altre informazioni, vedere <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="f22ce-195">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="f22ce-196">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-196">Singleton</span></span>

<span data-ttu-id="f22ce-197">I servizi di durata singleton vengono creati:</span><span class="sxs-lookup"><span data-stu-id="f22ce-197">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="f22ce-198">La prima volta che viene richiesto.</span><span class="sxs-lookup"><span data-stu-id="f22ce-198">The first time they're requested.</span></span>
* <span data-ttu-id="f22ce-199">Dallo sviluppatore, quando si fornisce un'istanza di implementazione direttamente al contenitore.</span><span class="sxs-lookup"><span data-stu-id="f22ce-199">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="f22ce-200">Questo approccio è raramente necessario.</span><span class="sxs-lookup"><span data-stu-id="f22ce-200">This approach is rarely needed.</span></span>

<span data-ttu-id="f22ce-201">Tutte le richieste successive usano la stessa istanza.</span><span class="sxs-lookup"><span data-stu-id="f22ce-201">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="f22ce-202">Se l'app richiede il comportamento singleton, consentire al contenitore del servizio di gestire la durata del servizio.</span><span class="sxs-lookup"><span data-stu-id="f22ce-202">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="f22ce-203">Non implementare il modello di progettazione singleton e fornire codice per eliminare il singleton.</span><span class="sxs-lookup"><span data-stu-id="f22ce-203">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="f22ce-204">I servizi non devono mai essere eliminati dal codice che ha risolto il servizio dal contenitore.</span><span class="sxs-lookup"><span data-stu-id="f22ce-204">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="f22ce-205">Se un tipo o una factory viene registrata come singleton, il contenitore elimina il singleton automaticamente.</span><span class="sxs-lookup"><span data-stu-id="f22ce-205">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="f22ce-206">Registrare i servizi singleton con <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A> .</span><span class="sxs-lookup"><span data-stu-id="f22ce-206">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="f22ce-207">I servizi singleton devono essere thread-safe e spesso utilizzati nei servizi senza stato.</span><span class="sxs-lookup"><span data-stu-id="f22ce-207">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="f22ce-208">Nelle app che elaborano le richieste, i servizi Singleton vengono eliminati quando il <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> viene eliminato alla chiusura dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="f22ce-208">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="f22ce-209">Poiché la memoria non viene rilasciata fino a quando l'app non viene arrestata, prendere in considerazione l'uso della memoria con un servizio singleton.</span><span class="sxs-lookup"><span data-stu-id="f22ce-209">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="f22ce-210">***Non*** risolvere un servizio con ambito da un singleton.</span><span class="sxs-lookup"><span data-stu-id="f22ce-210">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="f22ce-211">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="f22ce-211">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="f22ce-212">È possibile risolvere un servizio singleton da un servizio con ambito o temporaneo.</span><span class="sxs-lookup"><span data-stu-id="f22ce-212">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="f22ce-213">Metodi di registrazione del servizio</span><span class="sxs-lookup"><span data-stu-id="f22ce-213">Service registration methods</span></span>

<span data-ttu-id="f22ce-214">Il Framework fornisce metodi di estensione per la registrazione del servizio utili in scenari specifici:</span><span class="sxs-lookup"><span data-stu-id="f22ce-214">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="f22ce-215">Metodo</span><span class="sxs-lookup"><span data-stu-id="f22ce-215">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="f22ce-216">Automatico</span><span class="sxs-lookup"><span data-stu-id="f22ce-216">Automatic</span></span><br><span data-ttu-id="f22ce-217">object</span><span class="sxs-lookup"><span data-stu-id="f22ce-217">object</span></span><br><span data-ttu-id="f22ce-218">eliminazione</span><span class="sxs-lookup"><span data-stu-id="f22ce-218">disposal</span></span> | <span data-ttu-id="f22ce-219">Più elementi</span><span class="sxs-lookup"><span data-stu-id="f22ce-219">Multiple</span></span><br><span data-ttu-id="f22ce-220">implementazioni</span><span class="sxs-lookup"><span data-stu-id="f22ce-220">implementations</span></span> | <span data-ttu-id="f22ce-221">Pass args</span><span class="sxs-lookup"><span data-stu-id="f22ce-221">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="f22ce-222">Esempio:</span><span class="sxs-lookup"><span data-stu-id="f22ce-222">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="f22ce-223">Sì</span><span class="sxs-lookup"><span data-stu-id="f22ce-223">Yes</span></span>                             | <span data-ttu-id="f22ce-224">Sì</span><span class="sxs-lookup"><span data-stu-id="f22ce-224">Yes</span></span>                         | <span data-ttu-id="f22ce-225">No</span><span class="sxs-lookup"><span data-stu-id="f22ce-225">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="f22ce-226">Esempi:</span><span class="sxs-lookup"><span data-stu-id="f22ce-226">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="f22ce-227">Sì</span><span class="sxs-lookup"><span data-stu-id="f22ce-227">Yes</span></span>                             | <span data-ttu-id="f22ce-228">Sì</span><span class="sxs-lookup"><span data-stu-id="f22ce-228">Yes</span></span>                         | <span data-ttu-id="f22ce-229">Sì</span><span class="sxs-lookup"><span data-stu-id="f22ce-229">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="f22ce-230">Esempio:</span><span class="sxs-lookup"><span data-stu-id="f22ce-230">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="f22ce-231">Sì</span><span class="sxs-lookup"><span data-stu-id="f22ce-231">Yes</span></span>                             | <span data-ttu-id="f22ce-232">No</span><span class="sxs-lookup"><span data-stu-id="f22ce-232">No</span></span>                          | <span data-ttu-id="f22ce-233">No</span><span class="sxs-lookup"><span data-stu-id="f22ce-233">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="f22ce-234">Esempi:</span><span class="sxs-lookup"><span data-stu-id="f22ce-234">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="f22ce-235">No</span><span class="sxs-lookup"><span data-stu-id="f22ce-235">No</span></span>                              | <span data-ttu-id="f22ce-236">Sì</span><span class="sxs-lookup"><span data-stu-id="f22ce-236">Yes</span></span>                         | <span data-ttu-id="f22ce-237">Sì</span><span class="sxs-lookup"><span data-stu-id="f22ce-237">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="f22ce-238">Esempi:</span><span class="sxs-lookup"><span data-stu-id="f22ce-238">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="f22ce-239">No</span><span class="sxs-lookup"><span data-stu-id="f22ce-239">No</span></span>                              | <span data-ttu-id="f22ce-240">No</span><span class="sxs-lookup"><span data-stu-id="f22ce-240">No</span></span>                          | <span data-ttu-id="f22ce-241">Sì</span><span class="sxs-lookup"><span data-stu-id="f22ce-241">Yes</span></span>       |

<span data-ttu-id="f22ce-242">Per ulteriori informazioni sull'eliminazione dei tipi, vedere la sezione [Eliminazione dei servizi](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="f22ce-242">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="f22ce-243">Quando si [simulano i tipi per il test](xref:test/integration-tests#inject-mock-services), è comune usare più implementazioni.</span><span class="sxs-lookup"><span data-stu-id="f22ce-243">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="f22ce-244">Il Framework fornisce anche `TryAdd{LIFETIME}` metodi di estensione, che registrano il servizio solo se non è già stata registrata un'implementazione.</span><span class="sxs-lookup"><span data-stu-id="f22ce-244">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="f22ce-245">Nell'esempio seguente, la chiamata a `AddSingleton` Registra `MyDependency` come implementazione per `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="f22ce-245">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="f22ce-246">La chiamata a `TryAddSingleton` non ha alcun effetto perché `IMyDependency` dispone già di un'implementazione registrata:</span><span class="sxs-lookup"><span data-stu-id="f22ce-246">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="f22ce-247">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="f22ce-247">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="f22ce-248">I metodi [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) registrano il servizio solo se non esiste già un'implementazione *dello stesso tipo*.</span><span class="sxs-lookup"><span data-stu-id="f22ce-248">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="f22ce-249">Più servizi vengono risolti tramite `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-249">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="f22ce-250">Quando si registrano i servizi, lo sviluppatore deve aggiungere un'istanza se uno degli stessi tipi non è già stato aggiunto.</span><span class="sxs-lookup"><span data-stu-id="f22ce-250">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="f22ce-251">In genere, gli autori `TryAddEnumerable` di librerie utilizzano per evitare la registrazione di più copie di un'implementazione nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="f22ce-251">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="f22ce-252">Nell'esempio seguente, la prima chiamata a viene `TryAddEnumerable` registrata `MyDependency` come implementazione per `IMyDependency1` .</span><span class="sxs-lookup"><span data-stu-id="f22ce-252">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="f22ce-253">La seconda chiamata esegue la registrazione `MyDependency` per `IMyDependency2` .</span><span class="sxs-lookup"><span data-stu-id="f22ce-253">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="f22ce-254">La terza chiamata non ha alcun effetto perché `IMyDependency1` dispone già di un'implementazione registrata di `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="f22ce-254">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="f22ce-255">La registrazione del servizio è in genere indipendente dall'ordine, tranne quando si registrano più implementazioni dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="f22ce-255">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="f22ce-256">`IServiceCollection` è una raccolta di <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> oggetti.</span><span class="sxs-lookup"><span data-stu-id="f22ce-256">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="f22ce-257">Nell'esempio seguente viene illustrato come registrare un servizio creando e aggiungendo `ServiceDescriptor` :</span><span class="sxs-lookup"><span data-stu-id="f22ce-257">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="f22ce-258">I `Add{LIFETIME}` metodi predefiniti utilizzano lo stesso approccio.</span><span class="sxs-lookup"><span data-stu-id="f22ce-258">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="f22ce-259">Vedere ad esempio il [codice sorgente di AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="f22ce-259">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="f22ce-260">Comportamento dell'inserimento del costruttore</span><span class="sxs-lookup"><span data-stu-id="f22ce-260">Constructor injection behavior</span></span>

<span data-ttu-id="f22ce-261">I servizi possono essere risolti tramite:</span><span class="sxs-lookup"><span data-stu-id="f22ce-261">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="f22ce-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="f22ce-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="f22ce-263">Crea oggetti che non sono registrati nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="f22ce-263">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="f22ce-264">Usato con le funzionalità del Framework, ad esempio gli [Helper Tag](xref:mvc/views/tag-helpers/intro), i controller MVC e gli elementi di [associazione di modelli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="f22ce-264">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="f22ce-265">I costruttori possono accettare argomenti non forniti tramite l'inserimento di dipendenze, ma gli argomenti devono assegnare valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="f22ce-265">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="f22ce-266">Quando i servizi vengono risolti da `IServiceProvider` o `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede un costruttore *pubblico* .</span><span class="sxs-lookup"><span data-stu-id="f22ce-266">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="f22ce-267">Quando i servizi vengono risolti da `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede l'esistenza di un solo costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="f22ce-267">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="f22ce-268">Sebbene siano supportati gli overload dei costruttori, può essere presente un solo overload i cui argomenti possono essere tutti specificati tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="f22ce-268">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="f22ce-269">Contesti di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="f22ce-269">Entity Framework contexts</span></span>

<span data-ttu-id="f22ce-270">Per impostazione predefinita, i contesti di Entity Framework vengono aggiunti al contenitore del servizio usando la [durata con ambito](#service-lifetimes) poiché le operazioni del database dell'app Web sono in genere limitate all'ambito della richiesta client.</span><span class="sxs-lookup"><span data-stu-id="f22ce-270">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="f22ce-271">Per usare una durata diversa, specificare la durata usando un <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> Overload.</span><span class="sxs-lookup"><span data-stu-id="f22ce-271">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="f22ce-272">I servizi di una determinata durata non devono utilizzare un contesto di database con una durata minore della durata del servizio.</span><span class="sxs-lookup"><span data-stu-id="f22ce-272">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="f22ce-273">Opzioni di durata e di registrazione</span><span class="sxs-lookup"><span data-stu-id="f22ce-273">Lifetime and registration options</span></span>

<span data-ttu-id="f22ce-274">Per dimostrare la differenza tra le durate dei servizi e le relative opzioni di registrazione, prendere in considerazione le interfacce seguenti che rappresentano un'attività come operazione con un identificatore `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="f22ce-274">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="f22ce-275">A seconda del modo in cui viene configurata la durata del servizio di un'operazione per le interfacce seguenti, il contenitore fornisce le stesse istanze o diverse del servizio quando richiesto da una classe:</span><span class="sxs-lookup"><span data-stu-id="f22ce-275">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="f22ce-276">La `Operation` classe seguente implementa tutte le interfacce precedenti.</span><span class="sxs-lookup"><span data-stu-id="f22ce-276">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="f22ce-277">Il `Operation` costruttore genera un GUID e archivia gli ultimi 4 caratteri nella `OperationId` proprietà:</span><span class="sxs-lookup"><span data-stu-id="f22ce-277">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="f22ce-278">Il `Startup.ConfigureServices` metodo crea più registrazioni della `Operation` classe in base alle durate denominate:</span><span class="sxs-lookup"><span data-stu-id="f22ce-278">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="f22ce-279">L'app di esempio illustra le durate degli oggetti all'interno e tra le richieste.</span><span class="sxs-lookup"><span data-stu-id="f22ce-279">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="f22ce-280">Il `IndexModel` e il middleware richiedono ogni tipo di `IOperation` tipo e registrano `OperationId` per ogni tipo:</span><span class="sxs-lookup"><span data-stu-id="f22ce-280">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="f22ce-281">Analogamente a `IndexModel` , il middleware risolve gli stessi servizi:</span><span class="sxs-lookup"><span data-stu-id="f22ce-281">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="f22ce-282">I servizi con ambito devono essere risolti nel `InvokeAsync` Metodo:</span><span class="sxs-lookup"><span data-stu-id="f22ce-282">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="f22ce-283">L'output del logger Mostra:</span><span class="sxs-lookup"><span data-stu-id="f22ce-283">The logger output shows:</span></span>

* <span data-ttu-id="f22ce-284">Gli oggetti *temporanei* sono sempre diversi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-284">*Transient* objects are always different.</span></span> <span data-ttu-id="f22ce-285">Il `OperationId` valore temporaneo è diverso in `IndexModel` e nel middleware.</span><span class="sxs-lookup"><span data-stu-id="f22ce-285">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="f22ce-286">Gli oggetti con *ambito* sono gli stessi per ogni richiesta, ma sono diversi in ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="f22ce-286">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="f22ce-287">Gli oggetti *singleton* sono gli stessi per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="f22ce-287">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="f22ce-288">Per ridurre l'output di registrazione, impostare "Logging: LogLevel: Microsoft: Error" nella *appsettings.Development.jssu* file:</span><span class="sxs-lookup"><span data-stu-id="f22ce-288">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="f22ce-289">Chiamare i servizi da main</span><span class="sxs-lookup"><span data-stu-id="f22ce-289">Call services from main</span></span>

<span data-ttu-id="f22ce-290">Creare un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>IServiceScope con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) per risolvere un servizio con ambito nell'ambito di applicazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="f22ce-290">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="f22ce-291">Questo approccio è utile per l'accesso a un servizio con ambito all'avvio e per l'esecuzione di attività di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="f22ce-291">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="f22ce-292">Nell'esempio seguente viene illustrato come accedere al servizio con ambito `IMyDependency` e come chiamare il relativo `WriteMessage` metodo in `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="f22ce-292">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="f22ce-293">Convalida dell'ambito</span><span class="sxs-lookup"><span data-stu-id="f22ce-293">Scope validation</span></span>

<span data-ttu-id="f22ce-294">Quando l'app viene eseguita nell' [ambiente di sviluppo](xref:fundamentals/environments) e chiama [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) per compilare l'host, il provider di servizi predefinito esegue i controlli per verificare che:</span><span class="sxs-lookup"><span data-stu-id="f22ce-294">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="f22ce-295">I servizi con ambito non vengono risolti dal provider di servizi radice.</span><span class="sxs-lookup"><span data-stu-id="f22ce-295">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="f22ce-296">I servizi con ambito non vengono inseriti in singleton.</span><span class="sxs-lookup"><span data-stu-id="f22ce-296">Scoped services aren't injected into singletons.</span></span>
* <span data-ttu-id="f22ce-297">I servizi temporanei non vengono inseriti in Singleton o servizi con ambito.</span><span class="sxs-lookup"><span data-stu-id="f22ce-297">Transient services aren't injected into singletons or scoped services.</span></span>

<span data-ttu-id="f22ce-298">Il provider di servizi radice venga creato con la chiamata di <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A>.</span><span class="sxs-lookup"><span data-stu-id="f22ce-298">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="f22ce-299">La durata del provider di servizi radice corrisponde alla durata dell'app quando il provider inizia con l'app e viene eliminato quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="f22ce-299">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="f22ce-300">I servizi con ambito vengono eliminati dal contenitore che li ha creati.</span><span class="sxs-lookup"><span data-stu-id="f22ce-300">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="f22ce-301">Se nel contenitore radice viene creato un servizio con ambito, la durata del servizio viene effettivamente promossa a Singleton perché viene eliminata dal contenitore radice solo quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="f22ce-301">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="f22ce-302">La convalida degli ambiti servizio rileva queste situazioni nel contesto della chiamata di `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-302">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="f22ce-303">Per ulteriori informazioni, vedere [Convalida dell'ambito](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="f22ce-303">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="f22ce-304">Servizi di richiesta</span><span class="sxs-lookup"><span data-stu-id="f22ce-304">Request Services</span></span>

<span data-ttu-id="f22ce-305">I servizi disponibili all'interno di una richiesta di ASP.NET Core vengono esposti tramite la raccolta [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="f22ce-305">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="f22ce-306">Quando i servizi vengono richiesti dall'interno di una richiesta, i servizi e le relative dipendenze vengono risolti dalla `RequestServices` raccolta.</span><span class="sxs-lookup"><span data-stu-id="f22ce-306">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="f22ce-307">Il Framework crea un ambito per ogni richiesta ed `RequestServices` espone il provider di servizi con ambito.</span><span class="sxs-lookup"><span data-stu-id="f22ce-307">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="f22ce-308">Tutti i servizi con ambito sono validi fino a quando la richiesta è attiva.</span><span class="sxs-lookup"><span data-stu-id="f22ce-308">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="f22ce-309">Preferisce richiedere le dipendenze come parametri del costruttore per la risoluzione dei servizi dalla `RequestServices` raccolta.</span><span class="sxs-lookup"><span data-stu-id="f22ce-309">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="f22ce-310">In questo modo, le classi risultano più facili da testare.</span><span class="sxs-lookup"><span data-stu-id="f22ce-310">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="f22ce-311">Progettare i servizi per l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="f22ce-311">Design services for dependency injection</span></span>

<span data-ttu-id="f22ce-312">Durante la progettazione di servizi per l'inserimento di dipendenze:</span><span class="sxs-lookup"><span data-stu-id="f22ce-312">When designing services for dependency injection:</span></span>

* <span data-ttu-id="f22ce-313">Evitare membri e classi statiche con stato.</span><span class="sxs-lookup"><span data-stu-id="f22ce-313">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="f22ce-314">Evitare di creare lo stato globale progettando le app in modo da usare i servizi singleton.</span><span class="sxs-lookup"><span data-stu-id="f22ce-314">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="f22ce-315">Evitare la creazione diretta di istanze delle classi dipendenti all'interno di servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-315">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="f22ce-316">La creazione diretta di istanze associa il codice a una determinata implementazione.</span><span class="sxs-lookup"><span data-stu-id="f22ce-316">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="f22ce-317">Semplifica la testing dei servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-317">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="f22ce-318">Se una classe dispone di molte dipendenze inserite, potrebbe essere un segno che la classe ha troppe responsabilità e viola il [principio di singola responsabilità (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="f22ce-318">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="f22ce-319">Tentare di effettuare il refactoring della classe spostando alcune delle responsabilità in nuove classi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-319">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="f22ce-320">Tenere presente che Razor le classi del modello della pagina delle pagine e le classi controller MVC devono concentrarsi sulle problematiche dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="f22ce-320">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="f22ce-321">Eliminazione dei servizi</span><span class="sxs-lookup"><span data-stu-id="f22ce-321">Disposal of services</span></span>

<span data-ttu-id="f22ce-322">Il contenitore chiama <xref:System.IDisposable.Dispose%2A> per i tipi <xref:System.IDisposable> creati.</span><span class="sxs-lookup"><span data-stu-id="f22ce-322">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="f22ce-323">I servizi risolti dal contenitore non devono mai essere eliminati dallo sviluppatore.</span><span class="sxs-lookup"><span data-stu-id="f22ce-323">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="f22ce-324">Se un tipo o una factory viene registrata come singleton, il contenitore elimina il singleton automaticamente.</span><span class="sxs-lookup"><span data-stu-id="f22ce-324">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="f22ce-325">Nell'esempio seguente i servizi vengono creati dal contenitore del servizio ed eliminati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="f22ce-325">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="f22ce-326">La console di debug Visualizza il seguente output dopo ogni aggiornamento della pagina di indice:</span><span class="sxs-lookup"><span data-stu-id="f22ce-326">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="f22ce-327">Servizi non creati dal contenitore del servizio</span><span class="sxs-lookup"><span data-stu-id="f22ce-327">Services not created by the service container</span></span>

<span data-ttu-id="f22ce-328">Osservare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="f22ce-328">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="f22ce-329">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="f22ce-329">In the preceding code:</span></span>

* <span data-ttu-id="f22ce-330">Le istanze del servizio non vengono create dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-330">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="f22ce-331">Il Framework non elimina automaticamente i servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-331">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="f22ce-332">Lo sviluppatore è responsabile dell'eliminazione dei servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-332">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="f22ce-333">Linee guida IDisposable per istanze temporanee e condivise</span><span class="sxs-lookup"><span data-stu-id="f22ce-333">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="f22ce-334">Durata temporanea e limitata</span><span class="sxs-lookup"><span data-stu-id="f22ce-334">Transient, limited lifetime</span></span>

<span data-ttu-id="f22ce-335">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="f22ce-335">**Scenario**</span></span>

<span data-ttu-id="f22ce-336">L'app richiede un' <xref:System.IDisposable> istanza con una durata temporanea per uno degli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="f22ce-336">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="f22ce-337">L'istanza viene risolta nell'ambito radice (contenitore radice).</span><span class="sxs-lookup"><span data-stu-id="f22ce-337">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="f22ce-338">L'istanza deve essere eliminata prima della fine dell'ambito.</span><span class="sxs-lookup"><span data-stu-id="f22ce-338">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="f22ce-339">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="f22ce-339">**Solution**</span></span>

<span data-ttu-id="f22ce-340">Usare il modello factory per creare un'istanza all'esterno dell'ambito padre.</span><span class="sxs-lookup"><span data-stu-id="f22ce-340">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="f22ce-341">In questa situazione, in genere l'app dispone di un `Create` metodo che chiama direttamente il costruttore del tipo finale.</span><span class="sxs-lookup"><span data-stu-id="f22ce-341">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="f22ce-342">Se il tipo finale ha altre dipendenze, la factory può:</span><span class="sxs-lookup"><span data-stu-id="f22ce-342">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="f22ce-343">Ricevere un oggetto <xref:System.IServiceProvider> nel relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="f22ce-343">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="f22ce-344">Usare <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> per creare un'istanza dell'istanza all'esterno del contenitore, usando il contenitore per le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="f22ce-344">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="f22ce-345">Istanza condivisa, durata limitata</span><span class="sxs-lookup"><span data-stu-id="f22ce-345">Shared instance, limited lifetime</span></span>

<span data-ttu-id="f22ce-346">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="f22ce-346">**Scenario**</span></span>

<span data-ttu-id="f22ce-347">L'app richiede un' <xref:System.IDisposable> istanza condivisa tra più servizi, ma l' <xref:System.IDisposable> istanza deve avere una durata limitata.</span><span class="sxs-lookup"><span data-stu-id="f22ce-347">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="f22ce-348">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="f22ce-348">**Solution**</span></span>

<span data-ttu-id="f22ce-349">Registrare l'istanza con una durata con ambito.</span><span class="sxs-lookup"><span data-stu-id="f22ce-349">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="f22ce-350">Utilizzare <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> per creare un nuovo oggetto <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="f22ce-350">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="f22ce-351">Usare l'ambito <xref:System.IServiceProvider> per ottenere i servizi richiesti.</span><span class="sxs-lookup"><span data-stu-id="f22ce-351">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="f22ce-352">Eliminare l'ambito quando non è più necessario.</span><span class="sxs-lookup"><span data-stu-id="f22ce-352">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="f22ce-353">Linee guida generali sull'IDisposable</span><span class="sxs-lookup"><span data-stu-id="f22ce-353">General IDisposable guidelines</span></span>

* <span data-ttu-id="f22ce-354">Non registrare <xref:System.IDisposable> le istanze con una durata temporanea.</span><span class="sxs-lookup"><span data-stu-id="f22ce-354">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="f22ce-355">Usare invece il modello Factory.</span><span class="sxs-lookup"><span data-stu-id="f22ce-355">Use the factory pattern instead.</span></span>
* <span data-ttu-id="f22ce-356">Non risolvere <xref:System.IDisposable> le istanze con una durata temporanea o con ambito nell'ambito radice.</span><span class="sxs-lookup"><span data-stu-id="f22ce-356">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="f22ce-357">L'unica eccezione è rappresentata dal caso in cui l'app crea/ricrea ed Elimina <xref:System.IServiceProvider> , ma questo non è un modello ideale.</span><span class="sxs-lookup"><span data-stu-id="f22ce-357">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="f22ce-358">Per <xref:System.IDisposable> la ricezione di una dipendenza tramite di non è necessario che il ricevitore implementi <xref:System.IDisposable> se stesso.</span><span class="sxs-lookup"><span data-stu-id="f22ce-358">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="f22ce-359">Il ricevitore della <xref:System.IDisposable> dipendenza non deve chiamare <xref:System.IDisposable.Dispose%2A> su tale dipendenza.</span><span class="sxs-lookup"><span data-stu-id="f22ce-359">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="f22ce-360">Utilizzare gli ambiti per controllare la durata dei servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-360">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="f22ce-361">Gli ambiti non sono gerarchici e non esiste alcuna connessione speciale tra gli ambiti.</span><span class="sxs-lookup"><span data-stu-id="f22ce-361">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="f22ce-362">Sostituzione del contenitore di servizi predefinito</span><span class="sxs-lookup"><span data-stu-id="f22ce-362">Default service container replacement</span></span>

<span data-ttu-id="f22ce-363">Il contenitore dei servizi incorporato è progettato per soddisfare le esigenze del Framework e della maggior parte delle app consumer.</span><span class="sxs-lookup"><span data-stu-id="f22ce-363">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="f22ce-364">È consigliabile usare il contenitore predefinito a meno che non sia necessaria una funzionalità specifica che non supporta, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f22ce-364">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="f22ce-365">Inserimento di proprietà</span><span class="sxs-lookup"><span data-stu-id="f22ce-365">Property injection</span></span>
* <span data-ttu-id="f22ce-366">Inserimento in base al nome</span><span class="sxs-lookup"><span data-stu-id="f22ce-366">Injection based on name</span></span>
* <span data-ttu-id="f22ce-367">Contenitori figlio</span><span class="sxs-lookup"><span data-stu-id="f22ce-367">Child containers</span></span>
* <span data-ttu-id="f22ce-368">Gestione della durata personalizzata</span><span class="sxs-lookup"><span data-stu-id="f22ce-368">Custom lifetime management</span></span>
* <span data-ttu-id="f22ce-369">Supporto di `Func<T>` per l'inizializzazione differita</span><span class="sxs-lookup"><span data-stu-id="f22ce-369">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="f22ce-370">Registrazione basata sulle convenzioni</span><span class="sxs-lookup"><span data-stu-id="f22ce-370">Convention-based registration</span></span>

<span data-ttu-id="f22ce-371">Con ASP.NET Core app è possibile usare i contenitori di terze parti seguenti:</span><span class="sxs-lookup"><span data-stu-id="f22ce-371">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="f22ce-372">Autofac</span><span class="sxs-lookup"><span data-stu-id="f22ce-372">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="f22ce-373">DryIoc</span><span class="sxs-lookup"><span data-stu-id="f22ce-373">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="f22ce-374">Laura</span><span class="sxs-lookup"><span data-stu-id="f22ce-374">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="f22ce-375">LightInject</span><span class="sxs-lookup"><span data-stu-id="f22ce-375">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="f22ce-376">Lamar</span><span class="sxs-lookup"><span data-stu-id="f22ce-376">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="f22ce-377">Stashbox</span><span class="sxs-lookup"><span data-stu-id="f22ce-377">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="f22ce-378">Unity</span><span class="sxs-lookup"><span data-stu-id="f22ce-378">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="f22ce-379">Thread safety</span><span class="sxs-lookup"><span data-stu-id="f22ce-379">Thread safety</span></span>

<span data-ttu-id="f22ce-380">Creare servizi singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="f22ce-380">Create thread-safe singleton services.</span></span> <span data-ttu-id="f22ce-381">Se un servizio singleton presenta una dipendenza da un servizio temporaneo, il servizio temporaneo potrebbe richiedere anche thread safety a seconda del modo in cui viene utilizzato dal singleton.</span><span class="sxs-lookup"><span data-stu-id="f22ce-381">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="f22ce-382">Il metodo factory di un singolo servizio, ad esempio il secondo argomento di [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), non deve essere thread-safe.</span><span class="sxs-lookup"><span data-stu-id="f22ce-382">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="f22ce-383">Analogamente a un `static` Costruttore Type (), è garantita la chiamata solo una volta da un singolo thread.</span><span class="sxs-lookup"><span data-stu-id="f22ce-383">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="f22ce-384">Consigli</span><span class="sxs-lookup"><span data-stu-id="f22ce-384">Recommendations</span></span>

* <span data-ttu-id="f22ce-385">`async/await` la `Task` risoluzione del servizio basata su non è supportata.</span><span class="sxs-lookup"><span data-stu-id="f22ce-385">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="f22ce-386">Poiché C# non supporta costruttori asincroni, utilizzare i metodi asincroni dopo la risoluzione sincrona del servizio.</span><span class="sxs-lookup"><span data-stu-id="f22ce-386">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="f22ce-387">Evitare di archiviare i dati e la configurazione direttamente nel contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="f22ce-387">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="f22ce-388">Ad esempio, il carrello acquisti di un utente non dovrebbe in genere essere aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="f22ce-388">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="f22ce-389">La configurazione deve usare il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="f22ce-389">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="f22ce-390">Allo stesso modo, evitare gli oggetti "titolare dei dati" che esistono solo per consentire l'accesso a un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="f22ce-390">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="f22ce-391">È preferibile richiedere l'elemento effettivo tramite inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="f22ce-391">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="f22ce-392">Evitare l'accesso statico ai servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-392">Avoid static access to services.</span></span> <span data-ttu-id="f22ce-393">Ad esempio, evitare di acquisire [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) come proprietà o campo statico da usare altrove.</span><span class="sxs-lookup"><span data-stu-id="f22ce-393">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="f22ce-394">Mantieni le fabbriche veloci e sincrone.</span><span class="sxs-lookup"><span data-stu-id="f22ce-394">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="f22ce-395">Evitare di usare il *modello di localizzatore del servizio*.</span><span class="sxs-lookup"><span data-stu-id="f22ce-395">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="f22ce-396">Ad esempio, non richiamare <xref:System.IServiceProvider.GetService%2A> per ottenere un'istanza del servizio quando è invece possibile usare l'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="f22ce-396">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="f22ce-397">**Non corretto:**</span><span class="sxs-lookup"><span data-stu-id="f22ce-397">**Incorrect:**</span></span>

    ![Codice errato](dependency-injection/_static/bad.png)

  <span data-ttu-id="f22ce-399">**Corretto**:</span><span class="sxs-lookup"><span data-stu-id="f22ce-399">**Correct**:</span></span>

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
* <span data-ttu-id="f22ce-400">Un'altra variazione del localizzatore del servizio da evitare è inserire una factory che risolve le dipendenze in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="f22ce-400">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="f22ce-401">Queste procedure combinano le strategie di [inversione del controllo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="f22ce-401">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="f22ce-402">Evitare l'accesso statico a `HttpContext` (ad esempio [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="f22ce-402">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="f22ce-403">Evitare chiamate a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f22ce-403">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="f22ce-404">`BuildServiceProvider`La chiamata in genere si verifica quando lo sviluppatore desidera risolvere un servizio in `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f22ce-404">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="f22ce-405">Si consideri, ad esempio, il caso in cui l'oggetto `LoginPath` viene caricato dalla configurazione.</span><span class="sxs-lookup"><span data-stu-id="f22ce-405">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="f22ce-406">Evitare l'approccio seguente:</span><span class="sxs-lookup"><span data-stu-id="f22ce-406">Avoid the following approach:</span></span>

  ![codice errato durante la chiamata di BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="f22ce-408">Nell'immagine precedente, selezionando la linea ondulata verde in `services.BuildServiceProvider` viene visualizzato l'avviso ASP0000 seguente:</span><span class="sxs-lookup"><span data-stu-id="f22ce-408">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="f22ce-409">ASP0000 la chiamata a' BuildServiceProvider ' dal codice dell'applicazione comporta una copia aggiuntiva dei servizi singleton creati.</span><span class="sxs-lookup"><span data-stu-id="f22ce-409">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="f22ce-410">Prendere in considerazione alternative come la dipendenza che inserisce i servizi come parametri per "Configure".</span><span class="sxs-lookup"><span data-stu-id="f22ce-410">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="f22ce-411">`BuildServiceProvider`La chiamata di crea un secondo contenitore, che può creare singleton incompleti e causare riferimenti a oggetti grafici tra più contenitori.</span><span class="sxs-lookup"><span data-stu-id="f22ce-411">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="f22ce-412">Un modo corretto per ottenere `LoginPath` consiste nell'usare il supporto incorporato del modello di opzioni per l'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="f22ce-412">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="f22ce-413">I servizi temporanei Disposable vengono acquisiti dal contenitore per l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="f22ce-413">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="f22ce-414">Questa operazione può comportare una perdita di memoria se risolta dal contenitore di primo livello.</span><span class="sxs-lookup"><span data-stu-id="f22ce-414">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="f22ce-415">Abilitare la convalida dell'ambito per assicurarsi che l'app non disponga di servizi con ambito che acquisiscano i singleton.</span><span class="sxs-lookup"><span data-stu-id="f22ce-415">Enable scope validation to make sure the app doesn't have scoped services that capture singletons.</span></span> <span data-ttu-id="f22ce-416">Per ulteriori informazioni, vedere [Convalida dell'ambito](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="f22ce-416">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="f22ce-417">È tuttavia possibile che in alcuni casi queste raccomandazioni debbano essere ignorate.</span><span class="sxs-lookup"><span data-stu-id="f22ce-417">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="f22ce-418">Le eccezioni sono rari, soprattutto casi speciali all'interno del Framework stesso.</span><span class="sxs-lookup"><span data-stu-id="f22ce-418">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="f22ce-419">L'inserimento di dipendenze è un'*alternativa* ai modelli di accesso agli oggetti statici/globali.</span><span class="sxs-lookup"><span data-stu-id="f22ce-419">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="f22ce-420">Se l'inserimento di dipendenze viene usato con l'accesso agli oggetti statico i vantaggi non saranno evidenti.</span><span class="sxs-lookup"><span data-stu-id="f22ce-420">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="f22ce-421">Modelli consigliati per il multi-tenant in DI</span><span class="sxs-lookup"><span data-stu-id="f22ce-421">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="f22ce-422">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) è un Framework di applicazione per la creazione di applicazioni modulari multi-tenant in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f22ce-422">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="f22ce-423">Per ulteriori informazioni, vedere la [documentazione di Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="f22ce-423">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="f22ce-424">Vedere gli [esempi di Orchard Core](https://github.com/OrchardCMS/OrchardCore.Samples) per esempi di come creare app modulari e multi-tenant usando solo il Framework Orchard core senza le funzionalità specifiche di CMS.</span><span class="sxs-lookup"><span data-stu-id="f22ce-424">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="f22ce-425">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="f22ce-425">Framework-provided services</span></span>

<span data-ttu-id="f22ce-426">Il `Startup.ConfigureServices` metodo registra i servizi usati dall'app, incluse le funzionalità della piattaforma, ad esempio Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="f22ce-426">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="f22ce-427">Inizialmente, l'oggetto `IServiceCollection` fornito a `ConfigureServices` dispone di servizi definiti dal Framework a seconda della [configurazione dell'host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="f22ce-427">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="f22ce-428">Per le app basate sui modelli di ASP.NET Core, il Framework registra più di 250 servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-428">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="f22ce-429">La tabella seguente elenca un piccolo esempio di questi servizi registrati dal Framework:</span><span class="sxs-lookup"><span data-stu-id="f22ce-429">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="f22ce-430">Tipo di servizio</span><span class="sxs-lookup"><span data-stu-id="f22ce-430">Service Type</span></span>                                                                                    | <span data-ttu-id="f22ce-431">Durata</span><span class="sxs-lookup"><span data-stu-id="f22ce-431">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="f22ce-432">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="f22ce-432">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="f22ce-433">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-433">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="f22ce-434">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-434">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="f22ce-435">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-435">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="f22ce-436">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="f22ce-436">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="f22ce-437">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="f22ce-438">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="f22ce-438">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="f22ce-439">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-439">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="f22ce-440">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-440">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="f22ce-441">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-441">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="f22ce-442">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="f22ce-442">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="f22ce-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-443">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="f22ce-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-444">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="f22ce-445">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-445">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="f22ce-446">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f22ce-446">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="f22ce-447">Modelli DI conferenza NDC per lo sviluppo DI app DI</span><span class="sxs-lookup"><span data-stu-id="f22ce-447">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="f22ce-448">Quattro modi per eliminare IDisposable in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f22ce-448">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="f22ce-449">Scrittura di codice pulito in ASP.NET Core con inserimento delle dipendenze (MSDN)</span><span class="sxs-lookup"><span data-stu-id="f22ce-449">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="f22ce-450">Explicit Dependencies Principle (Principio delle dipendenze esplicite)</span><span class="sxs-lookup"><span data-stu-id="f22ce-450">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="f22ce-451">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler) (Contenitori di inversione del controllo e modello di inserimento delle dipendenze)</span><span class="sxs-lookup"><span data-stu-id="f22ce-451">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="f22ce-452">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Come registrare un servizio con più interfacce in ASP.NET Core DI)</span><span class="sxs-lookup"><span data-stu-id="f22ce-452">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f22ce-453">Di [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="f22ce-453">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="f22ce-454">ASP.NET Core supporta lo schema progettuale di software per l'inserimento di dipendenze, una tecnica per ottenere l'[IoC (Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra le classi e le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="f22ce-454">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="f22ce-455">Per altre informazioni specifiche per l'inserimento delle dipendenze all'interno di controller MVC, vedere <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="f22ce-455">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="f22ce-456">[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f22ce-456">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="f22ce-457">Panoramica dell'inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="f22ce-457">Overview of dependency injection</span></span>

<span data-ttu-id="f22ce-458">Una *dipendenza* è qualsiasi oggetto richiesto da un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="f22ce-458">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="f22ce-459">Esaminare la classe `MyDependency` seguente con un metodo `WriteMessage` da cui dipendono altre classi in un'app:</span><span class="sxs-lookup"><span data-stu-id="f22ce-459">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="f22ce-460">È possibile creare un'istanza della classe `MyDependency` per rendere il metodo `WriteMessage`disponibile per una classe.</span><span class="sxs-lookup"><span data-stu-id="f22ce-460">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="f22ce-461">La classe `MyDependency` è una dipendenza della classe `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="f22ce-461">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="f22ce-462">La classe crea e dipende direttamente dall'istanza `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-462">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="f22ce-463">Le dipendenze del codice (come nell'esempio precedente) sono problematiche e devono essere evitate per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="f22ce-463">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="f22ce-464">Per sostituire `MyDependency` con un'implementazione diversa, la classe deve essere modificata.</span><span class="sxs-lookup"><span data-stu-id="f22ce-464">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="f22ce-465">Se `MyDependency` ha dipendenze, devono essere configurate dalla classe.</span><span class="sxs-lookup"><span data-stu-id="f22ce-465">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="f22ce-466">In un progetto di grandi dimensioni con più classi che dipendono da `MyDependency`, il codice di configurazione diventa sparso in tutta l'app.</span><span class="sxs-lookup"><span data-stu-id="f22ce-466">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="f22ce-467">È difficile eseguire unit test di questa implementazione.</span><span class="sxs-lookup"><span data-stu-id="f22ce-467">This implementation is difficult to unit test.</span></span> <span data-ttu-id="f22ce-468">L'app dovrebbe usare una classe `MyDependency` fittizia o stub, ma ciò non è possibile con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="f22ce-468">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="f22ce-469">L'inserimento delle dipendenze consente di risolvere questi problemi tramite:</span><span class="sxs-lookup"><span data-stu-id="f22ce-469">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="f22ce-470">L'uso di un'interfaccia o di una classe di base per astrarre l'implementazione delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="f22ce-470">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="f22ce-471">La registrazione della dipendenza in un contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-471">Registration of the dependency in a service container.</span></span> <span data-ttu-id="f22ce-472">ASP.NET Core offre il contenitore di servizi predefinito <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="f22ce-472">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="f22ce-473">I servizi vengono registrati nel metodo `Startup.ConfigureServices` dell'app.</span><span class="sxs-lookup"><span data-stu-id="f22ce-473">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="f22ce-474">L'*inserimento* del servizio nel costruttore della classe in cui viene usato.</span><span class="sxs-lookup"><span data-stu-id="f22ce-474">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="f22ce-475">Il framework si assume la responsabilità della creazione di un'istanza della dipendenza e della sua eliminazione quando non è più necessaria.</span><span class="sxs-lookup"><span data-stu-id="f22ce-475">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="f22ce-476">Nell'[app di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l'interfaccia `IMyDependency` definisce un metodo che il servizio fornisce all'app:</span><span class="sxs-lookup"><span data-stu-id="f22ce-476">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="f22ce-477">Questa interfaccia viene implementata da un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="f22ce-477">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="f22ce-478">`MyDependency` richiede <xref:Microsoft.Extensions.Logging.ILogger`1> nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="f22ce-478">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="f22ce-479">Non è insolito usare l'inserimento delle dipendenze in modo concatenato.</span><span class="sxs-lookup"><span data-stu-id="f22ce-479">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="f22ce-480">Ogni dipendenza richiesta richiede a sua volta le proprie dipendenze.</span><span class="sxs-lookup"><span data-stu-id="f22ce-480">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="f22ce-481">Il contenitore risolve le dipendenze nel grafico e restituisce il servizio completamente risolto.</span><span class="sxs-lookup"><span data-stu-id="f22ce-481">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="f22ce-482">Il set di dipendenze che devono essere risolte viene generalmente chiamato *albero delle dipendenze* o *grafico dipendenze* o *grafico degli oggetti*.</span><span class="sxs-lookup"><span data-stu-id="f22ce-482">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="f22ce-483">`IMyDependency` e `ILogger<TCategoryName>` devono essere registrati nel contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-483">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="f22ce-484">`IMyDependency` viene registrato in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-484">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f22ce-485">`ILogger<TCategoryName>` viene registrato dall'infrastruttura di registrazione delle astrazioni, pertanto si tratta di un [servizio fornito dal Framework](#framework-provided-services) registrato per impostazione predefinita dal Framework.</span><span class="sxs-lookup"><span data-stu-id="f22ce-485">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="f22ce-486">Il contenitore risolve `ILogger<TCategoryName>` avvalendosi di [tipi aperti (generici)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando l'esigenza di registrare ogni singolo [tipo costruito (generico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="f22ce-486">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="f22ce-487">Nell'app di esempio, il servizio `IMyDependency` viene registrato con il tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-487">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="f22ce-488">La registrazione definisce come ambito della durata di servizio la durata di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="f22ce-488">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="f22ce-489">Le [durate dei servizi](#service-lifetimes) sono descritte più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="f22ce-489">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="f22ce-490">Ogni metodo di estensione `services.Add{SERVICE_NAME}` aggiunge (e potenzialmente configura) i servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-490">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="f22ce-491">Ad esempio, `services.AddMvc()` aggiunge le pagine dei servizi Razor e MVC require.</span><span class="sxs-lookup"><span data-stu-id="f22ce-491">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="f22ce-492">È consigliabile che le app seguano questa convenzione.</span><span class="sxs-lookup"><span data-stu-id="f22ce-492">We recommended that apps follow this convention.</span></span> <span data-ttu-id="f22ce-493">Inserire i metodi di estensione nello spazio dei nomi [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) per incapsulare i gruppi di registrazioni di servizio.</span><span class="sxs-lookup"><span data-stu-id="f22ce-493">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="f22ce-494">Se il costruttore del servizio richiede un [tipo incorporato](/dotnet/csharp/language-reference/keywords/built-in-types-table), ad esempio `string`, è possibile inserirlo usando la [configurazione](xref:fundamentals/configuration/index) o il [modello di opzioni](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="f22ce-494">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="f22ce-495">È richiesta un'istanza del servizio tramite il costruttore di una classe in cui il servizio viene usato e assegnato a un campo privato.</span><span class="sxs-lookup"><span data-stu-id="f22ce-495">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="f22ce-496">Il campo viene usato per accedere al servizio in base alle esigenze per l'intera classe.</span><span class="sxs-lookup"><span data-stu-id="f22ce-496">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="f22ce-497">Nell'app di esempio, viene richiesta l'istanza `IMyDependency` usata per chiamare il metodo `WriteMessage` del servizio:</span><span class="sxs-lookup"><span data-stu-id="f22ce-497">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="f22ce-498">Servizi inseriti all'avvio</span><span class="sxs-lookup"><span data-stu-id="f22ce-498">Services injected into Startup</span></span>

<span data-ttu-id="f22ce-499">`Startup`Quando si utilizza l'host generico () è possibile inserire nel costruttore solo i tipi di servizio seguenti <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="f22ce-499">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="f22ce-500">I servizi possono essere inseriti in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f22ce-500">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="f22ce-501">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="f22ce-501">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="f22ce-502">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="f22ce-502">Framework-provided services</span></span>

<span data-ttu-id="f22ce-503">Il `Startup.ConfigureServices` metodo è responsabile della definizione dei servizi utilizzati dall'app, incluse le funzionalità della piattaforma, ad esempio Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="f22ce-503">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="f22ce-504">Inizialmente, l'oggetto `IServiceCollection` fornito a `ConfigureServices` dispone di servizi definiti dal Framework a seconda della [configurazione dell'host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="f22ce-504">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="f22ce-505">Non è insolito che un'app basata su un modello di ASP.NET Core disponga di centinaia di servizi registrati dal Framework.</span><span class="sxs-lookup"><span data-stu-id="f22ce-505">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="f22ce-506">Nella tabella seguente è elencato un piccolo esempio di servizi registrati dal Framework.</span><span class="sxs-lookup"><span data-stu-id="f22ce-506">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="f22ce-507">Tipo di servizio</span><span class="sxs-lookup"><span data-stu-id="f22ce-507">Service Type</span></span> | <span data-ttu-id="f22ce-508">Durata</span><span class="sxs-lookup"><span data-stu-id="f22ce-508">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="f22ce-509">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="f22ce-509">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="f22ce-510">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-510">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="f22ce-511">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-511">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="f22ce-512">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-512">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="f22ce-513">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="f22ce-513">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="f22ce-514">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="f22ce-515">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="f22ce-515">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="f22ce-516">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-516">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="f22ce-517">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-517">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="f22ce-518">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-518">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="f22ce-519">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="f22ce-519">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="f22ce-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-520">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="f22ce-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-521">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="f22ce-522">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-522">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="f22ce-523">Registrare servizi aggiuntivi con i metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="f22ce-523">Register additional services with extension methods</span></span>

<span data-ttu-id="f22ce-524">Quando è disponibile un metodo di estensione della raccolta di servizi per la registrazione di un servizio (e dei servizi dipendenti, se necessario), la convenzione consiste nell'usare un singolo metodo di estensione `Add{SERVICE_NAME}` per registrare tutti i servizi richiesti da tale servizio.</span><span class="sxs-lookup"><span data-stu-id="f22ce-524">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="f22ce-525">Il codice seguente è un esempio di come aggiungere servizi aggiuntivi al contenitore usando i metodi di estensione [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="f22ce-525">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="f22ce-526">Per altre informazioni, vedere la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> nella documentazione delle API.</span><span class="sxs-lookup"><span data-stu-id="f22ce-526">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="f22ce-527">Durate del servizio</span><span class="sxs-lookup"><span data-stu-id="f22ce-527">Service lifetimes</span></span>

<span data-ttu-id="f22ce-528">Scegliere una durata appropriata per ogni servizio registrato.</span><span class="sxs-lookup"><span data-stu-id="f22ce-528">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="f22ce-529">I servizi ASP.NET Core possono essere configurati con le impostazioni di durata seguenti:</span><span class="sxs-lookup"><span data-stu-id="f22ce-529">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="f22ce-530">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="f22ce-530">Transient</span></span>

<span data-ttu-id="f22ce-531">I servizi con durata temporanea (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) vengono creati ogni volta che vengono richiesti dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-531">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="f22ce-532">Questa impostazione di durata è consigliata per i servizi semplici senza stati.</span><span class="sxs-lookup"><span data-stu-id="f22ce-532">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="f22ce-533">Nelle app che elaborano le richieste, i servizi temporanei vengono eliminati alla fine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="f22ce-533">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="f22ce-534">Con ambito</span><span class="sxs-lookup"><span data-stu-id="f22ce-534">Scoped</span></span>

<span data-ttu-id="f22ce-535">I servizi con durata con ambito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) vengono creati una volta per ogni richiesta client (connessione).</span><span class="sxs-lookup"><span data-stu-id="f22ce-535">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="f22ce-536">Nelle app che elaborano le richieste, i servizi con ambito vengono eliminati alla fine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="f22ce-536">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="f22ce-537">Quando si usa un servizio con ambito in un middleware, inserire il servizio nel metodo `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-537">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="f22ce-538">Non inserire tramite il [Costruttore Injection](xref:mvc/controllers/dependency-injection#constructor-injection) perché impone al servizio di comportarsi come un singleton.</span><span class="sxs-lookup"><span data-stu-id="f22ce-538">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="f22ce-539">Per altre informazioni, vedere <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="f22ce-539">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="f22ce-540">Singleton</span><span class="sxs-lookup"><span data-stu-id="f22ce-540">Singleton</span></span>

<span data-ttu-id="f22ce-541">I servizi con durata singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) vengono creati la prima volta che vengono richiesti (o quando si esegue `Startup.ConfigureServices` e viene specificata un'istanza con la registrazione del servizio).</span><span class="sxs-lookup"><span data-stu-id="f22ce-541">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="f22ce-542">Tutte le richieste successive usano la stessa istanza.</span><span class="sxs-lookup"><span data-stu-id="f22ce-542">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="f22ce-543">Se l'app richiede un comportamento singleton, è consigliabile consentire al contenitore del servizio di gestire la durata del servizio.</span><span class="sxs-lookup"><span data-stu-id="f22ce-543">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="f22ce-544">Non implementare lo schema progettuale singleton e fornire codice utente per gestire la durata dell'oggetto nella classe.</span><span class="sxs-lookup"><span data-stu-id="f22ce-544">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="f22ce-545">Nelle app che elaborano le richieste, i servizi Singleton vengono eliminati quando il <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> viene eliminato alla chiusura dell'app.</span><span class="sxs-lookup"><span data-stu-id="f22ce-545">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="f22ce-546">Può essere pericoloso risolvere un servizio con ambito da un singleton.</span><span class="sxs-lookup"><span data-stu-id="f22ce-546">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="f22ce-547">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="f22ce-547">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="f22ce-548">Metodi di registrazione del servizio</span><span class="sxs-lookup"><span data-stu-id="f22ce-548">Service registration methods</span></span>

<span data-ttu-id="f22ce-549">I metodi di estensione per la registrazione del servizio offrono overload che risultano utili in scenari specifici.</span><span class="sxs-lookup"><span data-stu-id="f22ce-549">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="f22ce-550">Metodo</span><span class="sxs-lookup"><span data-stu-id="f22ce-550">Method</span></span> | <span data-ttu-id="f22ce-551">Automatico</span><span class="sxs-lookup"><span data-stu-id="f22ce-551">Automatic</span></span><br><span data-ttu-id="f22ce-552">object</span><span class="sxs-lookup"><span data-stu-id="f22ce-552">object</span></span><br><span data-ttu-id="f22ce-553">eliminazione</span><span class="sxs-lookup"><span data-stu-id="f22ce-553">disposal</span></span> | <span data-ttu-id="f22ce-554">Più elementi</span><span class="sxs-lookup"><span data-stu-id="f22ce-554">Multiple</span></span><br><span data-ttu-id="f22ce-555">implementazioni</span><span class="sxs-lookup"><span data-stu-id="f22ce-555">implementations</span></span> | <span data-ttu-id="f22ce-556">Pass args</span><span class="sxs-lookup"><span data-stu-id="f22ce-556">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="f22ce-557">Esempio:</span><span class="sxs-lookup"><span data-stu-id="f22ce-557">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="f22ce-558">Sì</span><span class="sxs-lookup"><span data-stu-id="f22ce-558">Yes</span></span> | <span data-ttu-id="f22ce-559">Sì</span><span class="sxs-lookup"><span data-stu-id="f22ce-559">Yes</span></span> | <span data-ttu-id="f22ce-560">No</span><span class="sxs-lookup"><span data-stu-id="f22ce-560">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="f22ce-561">Esempi:</span><span class="sxs-lookup"><span data-stu-id="f22ce-561">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="f22ce-562">Sì</span><span class="sxs-lookup"><span data-stu-id="f22ce-562">Yes</span></span> | <span data-ttu-id="f22ce-563">Sì</span><span class="sxs-lookup"><span data-stu-id="f22ce-563">Yes</span></span> | <span data-ttu-id="f22ce-564">Sì</span><span class="sxs-lookup"><span data-stu-id="f22ce-564">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="f22ce-565">Esempio:</span><span class="sxs-lookup"><span data-stu-id="f22ce-565">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="f22ce-566">Sì</span><span class="sxs-lookup"><span data-stu-id="f22ce-566">Yes</span></span> | <span data-ttu-id="f22ce-567">No</span><span class="sxs-lookup"><span data-stu-id="f22ce-567">No</span></span> | <span data-ttu-id="f22ce-568">No</span><span class="sxs-lookup"><span data-stu-id="f22ce-568">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="f22ce-569">Esempi:</span><span class="sxs-lookup"><span data-stu-id="f22ce-569">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="f22ce-570">No</span><span class="sxs-lookup"><span data-stu-id="f22ce-570">No</span></span> | <span data-ttu-id="f22ce-571">Sì</span><span class="sxs-lookup"><span data-stu-id="f22ce-571">Yes</span></span> | <span data-ttu-id="f22ce-572">Sì</span><span class="sxs-lookup"><span data-stu-id="f22ce-572">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="f22ce-573">Esempi:</span><span class="sxs-lookup"><span data-stu-id="f22ce-573">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="f22ce-574">No</span><span class="sxs-lookup"><span data-stu-id="f22ce-574">No</span></span> | <span data-ttu-id="f22ce-575">No</span><span class="sxs-lookup"><span data-stu-id="f22ce-575">No</span></span> | <span data-ttu-id="f22ce-576">Sì</span><span class="sxs-lookup"><span data-stu-id="f22ce-576">Yes</span></span> |

<span data-ttu-id="f22ce-577">Per ulteriori informazioni sull'eliminazione dei tipi, vedere la sezione [Eliminazione dei servizi](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="f22ce-577">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="f22ce-578">Uno scenario comune per più implementazioni è costituito dai [tipi di simulazioni per i test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="f22ce-578">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="f22ce-579">I metodi `TryAdd{LIFETIME}` registrano il servizio solo se esiste già un'implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="f22ce-579">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="f22ce-580">Nell'esempio seguente, la prima riga registra `MyDependency` per `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-580">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="f22ce-581">La seconda riga non ha alcun effetto perché `IMyDependency` dispone già di un'implementazione registrata:</span><span class="sxs-lookup"><span data-stu-id="f22ce-581">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="f22ce-582">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="f22ce-582">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="f22ce-583">I metodi [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrano il servizio solo se non esiste già un'implementazione *dello stesso tipo*.</span><span class="sxs-lookup"><span data-stu-id="f22ce-583">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="f22ce-584">Più servizi vengono risolti tramite `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-584">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="f22ce-585">Durante la registrazione di servizi, lo sviluppatore desidera aggiungere un'istanza solo se non ne è già stata aggiunta una dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="f22ce-585">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="f22ce-586">In genere, questo metodo viene utilizzato dagli autori di librerie per evitare di registrare due copie di un'istanza nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="f22ce-586">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="f22ce-587">Nell'esempio seguente, la prima riga registra `MyDep` per `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-587">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="f22ce-588">La seconda riga registra `MyDep` per `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-588">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="f22ce-589">La seconda riga non ha alcun effetto perché `IMyDep1` dispone già di un'implementazione registrata di `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="f22ce-589">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="f22ce-590">Comportamento dell'inserimento del costruttore</span><span class="sxs-lookup"><span data-stu-id="f22ce-590">Constructor injection behavior</span></span>

<span data-ttu-id="f22ce-591">I servizi possono essere risolti con due meccanismi:</span><span class="sxs-lookup"><span data-stu-id="f22ce-591">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="f22ce-592"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Consente la creazione di oggetti senza registrazione del servizio nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="f22ce-592"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="f22ce-593">`ActivatorUtilities` viene usato con astrazioni esposte all'utente, ad esempio helper tag, controller MVC e strumenti di associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="f22ce-593">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="f22ce-594">I costruttori possono accettare argomenti non forniti tramite l'inserimento di dipendenze, ma gli argomenti devono assegnare valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="f22ce-594">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="f22ce-595">Quando i servizi vengono risolti da `IServiceProvider` o `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede un costruttore *pubblico* .</span><span class="sxs-lookup"><span data-stu-id="f22ce-595">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="f22ce-596">Quando i servizi vengono risolti da `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede l'esistenza di un solo costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="f22ce-596">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="f22ce-597">Sebbene siano supportati gli overload dei costruttori, può essere presente un solo overload i cui argomenti possono essere tutti specificati tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="f22ce-597">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="f22ce-598">Contesti di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="f22ce-598">Entity Framework contexts</span></span>

<span data-ttu-id="f22ce-599">I contesti di Entity Framework vengono in genere aggiunti al contenitore di servizi usando la [durata con ambito](#service-lifetimes) perché le operazioni di database delle app Web hanno di solito come ambito la richiesta client.</span><span class="sxs-lookup"><span data-stu-id="f22ce-599">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="f22ce-600">L'ambito della durata predefinita è se una durata non è specificata da un overload [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) durante la registrazione del contesto del database.</span><span class="sxs-lookup"><span data-stu-id="f22ce-600">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="f22ce-601">I servizi con una durata specificata non devono usare un contesto di database con una durata più breve rispetto al servizio.</span><span class="sxs-lookup"><span data-stu-id="f22ce-601">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="f22ce-602">Opzioni di durata e di registrazione</span><span class="sxs-lookup"><span data-stu-id="f22ce-602">Lifetime and registration options</span></span>

<span data-ttu-id="f22ce-603">Per illustrare la differenza tra le opzioni di durata e registrazione, si considerino le interfacce seguenti che rappresentano le attività come un'operazione con un identificatore univoco, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-603">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="f22ce-604">A seconda del modo in cui la durata di un servizio operativo viene configurata per le interfacce seguenti, il contenitore fornisce la stessa istanza o un'istanza diversa del servizio quando richiesto da una classe:</span><span class="sxs-lookup"><span data-stu-id="f22ce-604">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="f22ce-605">Le interfacce vengono implementate nella classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-605">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="f22ce-606">Il costruttore `Operation` genera un GUID se non ne viene fornito uno:</span><span class="sxs-lookup"><span data-stu-id="f22ce-606">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="f22ce-607">Viene registrato un `OperationService` che dipende da ognuno degli altri tipi `Operation`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-607">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="f22ce-608">Quando viene richiesto `OperationService` tramite l'inserimento delle dipendenze, riceve una nuova istanza di ogni servizio o un'istanza esistente in base alla durata del servizio dipendente.</span><span class="sxs-lookup"><span data-stu-id="f22ce-608">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="f22ce-609">Se i servizi temporanei vengono creati quando vengono richiesti dal contenitore, `OperationId` del servizio `IOperationTransient` è diverso da `OperationId` di `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-609">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="f22ce-610">`OperationService` riceve una nuova istanza della classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-610">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="f22ce-611">La nuova istanza restituisce un `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="f22ce-611">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="f22ce-612">Se i servizi con ambito vengono creati per ogni richiesta client, `OperationId` del servizio `IOperationScoped` corrisponde a `OperationService` all'interno di una richiesta client.</span><span class="sxs-lookup"><span data-stu-id="f22ce-612">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="f22ce-613">In tutte le richieste client entrambi i servizi condividono un valore `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="f22ce-613">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="f22ce-614">Se i servizi singleton e con istanza singleton vengono creati una sola volta e usati per tutte le richieste client e tutti i servizi, `OperationId` rimane costante in tutte le richieste di servizio.</span><span class="sxs-lookup"><span data-stu-id="f22ce-614">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="f22ce-615">In `Startup.ConfigureServices`, ogni tipo viene aggiunto al contenitore in base alla durata denominata:</span><span class="sxs-lookup"><span data-stu-id="f22ce-615">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="f22ce-616">Il servizio `IOperationSingletonInstance` usa un'istanza specifica con un ID noto di `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-616">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="f22ce-617">È chiaro quando questo tipo è in uso (il relativo GUID è composto da tutti zero).</span><span class="sxs-lookup"><span data-stu-id="f22ce-617">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="f22ce-618">L'app di esempio dimostra le durate degli oggetti all'interno e tra le singole richieste.</span><span class="sxs-lookup"><span data-stu-id="f22ce-618">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="f22ce-619">L'`IndexModel` dell'app di esempio richiede ogni genere di tipo `IOperation` e `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-619">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="f22ce-620">La pagina visualizza quindi tutti i valori `OperationId` della classe del modello di pagina e del servizio tramite assegnazioni di proprietà:</span><span class="sxs-lookup"><span data-stu-id="f22ce-620">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="f22ce-621">L'output seguente mostra i risultati delle due richieste:</span><span class="sxs-lookup"><span data-stu-id="f22ce-621">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="f22ce-622">**Prima richiesta:**</span><span class="sxs-lookup"><span data-stu-id="f22ce-622">**First request:**</span></span>

<span data-ttu-id="f22ce-623">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="f22ce-623">Controller operations:</span></span>

<span data-ttu-id="f22ce-624">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="f22ce-624">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="f22ce-625">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="f22ce-625">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="f22ce-626">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f22ce-626">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f22ce-627">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f22ce-627">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f22ce-628">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="f22ce-628">`OperationService` operations:</span></span>

<span data-ttu-id="f22ce-629">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="f22ce-629">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="f22ce-630">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="f22ce-630">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="f22ce-631">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f22ce-631">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f22ce-632">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f22ce-632">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f22ce-633">**Seconda richiesta:**</span><span class="sxs-lookup"><span data-stu-id="f22ce-633">**Second request:**</span></span>

<span data-ttu-id="f22ce-634">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="f22ce-634">Controller operations:</span></span>

<span data-ttu-id="f22ce-635">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="f22ce-635">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="f22ce-636">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="f22ce-636">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="f22ce-637">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f22ce-637">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f22ce-638">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f22ce-638">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f22ce-639">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="f22ce-639">`OperationService` operations:</span></span>

<span data-ttu-id="f22ce-640">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="f22ce-640">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="f22ce-641">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="f22ce-641">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="f22ce-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f22ce-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f22ce-643">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f22ce-643">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f22ce-644">Osservare i valori `OperationId` che variano all'interno della richiesta e da una richiesta e l'altra:</span><span class="sxs-lookup"><span data-stu-id="f22ce-644">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="f22ce-645">Gli oggetti *temporanei* sono sempre diversi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-645">*Transient* objects are always different.</span></span> <span data-ttu-id="f22ce-646">Il valore `OperationId` temporaneo per la prima e la seconda richiesta client è diverso per entrambe le operazioni `OperationService` e in tutte le richieste client.</span><span class="sxs-lookup"><span data-stu-id="f22ce-646">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="f22ce-647">Viene fornita una nuova istanza per ogni richiesta di servizio e richiesta client.</span><span class="sxs-lookup"><span data-stu-id="f22ce-647">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="f22ce-648">Gli oggetti *con ambito* sono gli stessi all'interno di una richiesta client, ma variano nelle diverse richieste client.</span><span class="sxs-lookup"><span data-stu-id="f22ce-648">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="f22ce-649">Gli oggetti *singleton* sono gli stessi per ogni oggetto e per ogni richiesta, indipendentemente dal fatto che un' `Operation` istanza venga fornita in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f22ce-649">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="f22ce-650">Chiamare i servizi da main</span><span class="sxs-lookup"><span data-stu-id="f22ce-650">Call services from main</span></span>

<span data-ttu-id="f22ce-651">Creare un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>IServiceScope con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) per risolvere un servizio con ambito nell'ambito di applicazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="f22ce-651">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="f22ce-652">Questo approccio è utile per l'accesso a un servizio con ambito all'avvio e per l'esecuzione di attività di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="f22ce-652">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="f22ce-653">L'esempio seguente indica come ottenere un contesto per `MyScopedService` in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f22ce-653">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="f22ce-654">Convalida dell'ambito</span><span class="sxs-lookup"><span data-stu-id="f22ce-654">Scope validation</span></span>

<span data-ttu-id="f22ce-655">Quando l'app viene eseguita nell'ambiente di sviluppo, il provider di servizi predefinito esegue controlli per verificare che:</span><span class="sxs-lookup"><span data-stu-id="f22ce-655">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="f22ce-656">I servizi con ambito non vengano risolti direttamente o indirettamente dal provider di servizi radice.</span><span class="sxs-lookup"><span data-stu-id="f22ce-656">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="f22ce-657">I servizi con ambito non vengano inseriti direttamente o indirettamente in singleton.</span><span class="sxs-lookup"><span data-stu-id="f22ce-657">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="f22ce-658">Il provider di servizi radice venga creato con la chiamata di <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="f22ce-658">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="f22ce-659">La durata del provider di servizi radice corrisponde alla durata dell'app/server quando il provider viene avviato con l'app e viene eliminato alla chiusura dell'app.</span><span class="sxs-lookup"><span data-stu-id="f22ce-659">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="f22ce-660">I servizi con ambito vengono eliminati dal contenitore che li ha creati.</span><span class="sxs-lookup"><span data-stu-id="f22ce-660">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="f22ce-661">Se un servizio con ambito viene creato nel contenitore radice, la durata del servizio viene di fatto convertita in singleton, perché il servizio viene eliminato solo dal contenitore radice alla chiusura dell'app/server.</span><span class="sxs-lookup"><span data-stu-id="f22ce-661">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="f22ce-662">La convalida degli ambiti servizio rileva queste situazioni nel contesto della chiamata di `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-662">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="f22ce-663">Per altre informazioni, vedere <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="f22ce-663">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="f22ce-664">Servizi di richiesta</span><span class="sxs-lookup"><span data-stu-id="f22ce-664">Request Services</span></span>

<span data-ttu-id="f22ce-665">I servizi disponibili all'interno di una richiesta ASP.NET Core da `HttpContext` vengono esposti tramite la raccolta [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="f22ce-665">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="f22ce-666">I servizi di richiesta rappresentano i servizi configurati e richiesti come parte dell'app.</span><span class="sxs-lookup"><span data-stu-id="f22ce-666">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="f22ce-667">Quando gli oggetti specificano dipendenze, le dipendenze sono soddisfatte dai tipi individuati in `RequestServices`, non in `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-667">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="f22ce-668">In generale, l'app non deve usare queste proprietà direttamente.</span><span class="sxs-lookup"><span data-stu-id="f22ce-668">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="f22ce-669">Richiedere invece i tipi richiesti dalle classi tramite i costruttori di classi e consentire al framework di inserire le dipendenze.</span><span class="sxs-lookup"><span data-stu-id="f22ce-669">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="f22ce-670">Si ottengono classi più facili da testare.</span><span class="sxs-lookup"><span data-stu-id="f22ce-670">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="f22ce-671">È consigliabile richiedere le dipendenze come parametri del costruttore per l'accesso alla raccolta `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="f22ce-671">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="f22ce-672">Progettare i servizi per l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="f22ce-672">Design services for dependency injection</span></span>

<span data-ttu-id="f22ce-673">Le procedure consigliate prevedono di:</span><span class="sxs-lookup"><span data-stu-id="f22ce-673">Best practices are to:</span></span>

* <span data-ttu-id="f22ce-674">Progettare i servizi per usare l'inserimento delle dipendenze per ottenere le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="f22ce-674">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="f22ce-675">Evitare membri e classi statiche con stato.</span><span class="sxs-lookup"><span data-stu-id="f22ce-675">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="f22ce-676">Progettare le app in modo da usare i servizi singleton, evitando così di creare uno stato globale.</span><span class="sxs-lookup"><span data-stu-id="f22ce-676">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="f22ce-677">Evitare la creazione diretta di istanze delle classi dipendenti all'interno di servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-677">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="f22ce-678">La creazione diretta di istanze associa il codice a una determinata implementazione.</span><span class="sxs-lookup"><span data-stu-id="f22ce-678">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="f22ce-679">Fare in modo che le classi siano piccole, con factoring corretto e facili da testare.</span><span class="sxs-lookup"><span data-stu-id="f22ce-679">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="f22ce-680">Se una classe sembra avere troppe dipendenze inserite, ciò significa in genere che la classe ha un numero eccessivo di responsabilità e sta violando il [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (principio di responsabilità singola).</span><span class="sxs-lookup"><span data-stu-id="f22ce-680">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="f22ce-681">Tentare di effettuare il refactoring della classe spostando alcune delle responsabilità in una nuova classe.</span><span class="sxs-lookup"><span data-stu-id="f22ce-681">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="f22ce-682">Tenere presente che Razor le classi del modello della pagina delle pagine e le classi controller MVC devono concentrarsi sulle problematiche dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="f22ce-682">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="f22ce-683">Di conseguenza, le regole business e i dettagli di implementazione di accesso ai dati devono essere inseriti in classi appropriate per queste [problematiche separate](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="f22ce-683">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="f22ce-684">Eliminazione dei servizi</span><span class="sxs-lookup"><span data-stu-id="f22ce-684">Disposal of services</span></span>

<span data-ttu-id="f22ce-685">Il contenitore chiama <xref:System.IDisposable.Dispose*> per i tipi <xref:System.IDisposable> creati.</span><span class="sxs-lookup"><span data-stu-id="f22ce-685">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="f22ce-686">Se un'istanza viene aggiunta al contenitore dal codice utente, non viene eliminata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="f22ce-686">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="f22ce-687">Nell'esempio seguente i servizi vengono creati dal contenitore del servizio ed eliminati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="f22ce-687">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="f22ce-688">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="f22ce-688">In the following example:</span></span>

* <span data-ttu-id="f22ce-689">Le istanze del servizio non vengono create dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-689">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="f22ce-690">Le durate di servizio previste non sono note dal Framework.</span><span class="sxs-lookup"><span data-stu-id="f22ce-690">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="f22ce-691">Il Framework non elimina automaticamente i servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-691">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="f22ce-692">Se i servizi non vengono eliminati in modo esplicito nel codice dello sviluppatore, vengono mantenuti fino a quando l'app non viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="f22ce-692">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="f22ce-693">Linee guida IDisposable per istanze temporanee e condivise</span><span class="sxs-lookup"><span data-stu-id="f22ce-693">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="f22ce-694">Durata temporanea e limitata</span><span class="sxs-lookup"><span data-stu-id="f22ce-694">Transient, limited lifetime</span></span>

<span data-ttu-id="f22ce-695">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="f22ce-695">**Scenario**</span></span>

<span data-ttu-id="f22ce-696">L'app richiede un' <xref:System.IDisposable> istanza con una durata temporanea per uno degli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="f22ce-696">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="f22ce-697">L'istanza viene risolta nell'ambito radice.</span><span class="sxs-lookup"><span data-stu-id="f22ce-697">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="f22ce-698">L'istanza deve essere eliminata prima della fine dell'ambito.</span><span class="sxs-lookup"><span data-stu-id="f22ce-698">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="f22ce-699">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="f22ce-699">**Solution**</span></span>

<span data-ttu-id="f22ce-700">Usare il modello factory per creare un'istanza all'esterno dell'ambito padre.</span><span class="sxs-lookup"><span data-stu-id="f22ce-700">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="f22ce-701">In questa situazione, in genere l'app dispone di un `Create` metodo che chiama direttamente il costruttore del tipo finale.</span><span class="sxs-lookup"><span data-stu-id="f22ce-701">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="f22ce-702">Se il tipo finale ha altre dipendenze, la factory può:</span><span class="sxs-lookup"><span data-stu-id="f22ce-702">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="f22ce-703">Ricevere un oggetto <xref:System.IServiceProvider> nel relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="f22ce-703">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="f22ce-704">Usare <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> per creare un'istanza dell'istanza all'esterno del contenitore, usando il contenitore per le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="f22ce-704">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="f22ce-705">Istanza condivisa, durata limitata</span><span class="sxs-lookup"><span data-stu-id="f22ce-705">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="f22ce-706">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="f22ce-706">**Scenario**</span></span>

<span data-ttu-id="f22ce-707">L'app richiede un' <xref:System.IDisposable> istanza condivisa tra più servizi, ma <xref:System.IDisposable> deve avere una durata limitata.</span><span class="sxs-lookup"><span data-stu-id="f22ce-707">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="f22ce-708">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="f22ce-708">**Solution**</span></span>

<span data-ttu-id="f22ce-709">Registrare l'istanza con una durata con ambito.</span><span class="sxs-lookup"><span data-stu-id="f22ce-709">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="f22ce-710">Usare <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> per avviare e creare un nuovo oggetto <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="f22ce-710">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="f22ce-711">Usare l'ambito <xref:System.IServiceProvider> per ottenere i servizi richiesti.</span><span class="sxs-lookup"><span data-stu-id="f22ce-711">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="f22ce-712">Eliminare l'ambito quando la durata deve terminare.</span><span class="sxs-lookup"><span data-stu-id="f22ce-712">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="f22ce-713">Linee guida generali</span><span class="sxs-lookup"><span data-stu-id="f22ce-713">General Guidelines</span></span>

* <span data-ttu-id="f22ce-714">Non registrare <xref:System.IDisposable> istanze con un ambito temporaneo.</span><span class="sxs-lookup"><span data-stu-id="f22ce-714">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="f22ce-715">Usare invece il modello Factory.</span><span class="sxs-lookup"><span data-stu-id="f22ce-715">Use the factory pattern instead.</span></span>
* <span data-ttu-id="f22ce-716">Non risolvere istanze temporanee o <xref:System.IDisposable> con ambito nell'ambito radice.</span><span class="sxs-lookup"><span data-stu-id="f22ce-716">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="f22ce-717">L'unica eccezione generale si verifica quando l'app crea/ricrea ed Elimina <xref:System.IServiceProvider> , che non è un modello ideale.</span><span class="sxs-lookup"><span data-stu-id="f22ce-717">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="f22ce-718">Per <xref:System.IDisposable> la ricezione di una dipendenza tramite di non è necessario che il ricevitore implementi <xref:System.IDisposable> se stesso.</span><span class="sxs-lookup"><span data-stu-id="f22ce-718">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="f22ce-719">Il ricevitore della <xref:System.IDisposable> dipendenza non deve chiamare <xref:System.IDisposable.Dispose%2A> su tale dipendenza.</span><span class="sxs-lookup"><span data-stu-id="f22ce-719">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="f22ce-720">Gli ambiti devono essere usati per controllare la durata dei servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-720">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="f22ce-721">Gli ambiti non sono gerarchici e non esiste alcuna connessione speciale tra gli ambiti.</span><span class="sxs-lookup"><span data-stu-id="f22ce-721">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="f22ce-722">Sostituzione del contenitore di servizi predefinito</span><span class="sxs-lookup"><span data-stu-id="f22ce-722">Default service container replacement</span></span>

<span data-ttu-id="f22ce-723">Il contenitore dei servizi incorporato è progettato per soddisfare le esigenze del Framework e della maggior parte delle app consumer.</span><span class="sxs-lookup"><span data-stu-id="f22ce-723">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="f22ce-724">Si consiglia di usare il contenitore predefinito a meno che non sia necessaria una funzionalità specifica che il contenitore incorporato non supporta, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f22ce-724">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="f22ce-725">Inserimento di proprietà</span><span class="sxs-lookup"><span data-stu-id="f22ce-725">Property injection</span></span>
* <span data-ttu-id="f22ce-726">Inserimento in base al nome</span><span class="sxs-lookup"><span data-stu-id="f22ce-726">Injection based on name</span></span>
* <span data-ttu-id="f22ce-727">Contenitori figlio</span><span class="sxs-lookup"><span data-stu-id="f22ce-727">Child containers</span></span>
* <span data-ttu-id="f22ce-728">Gestione della durata personalizzata</span><span class="sxs-lookup"><span data-stu-id="f22ce-728">Custom lifetime management</span></span>
* <span data-ttu-id="f22ce-729">Supporto di `Func<T>` per l'inizializzazione differita</span><span class="sxs-lookup"><span data-stu-id="f22ce-729">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="f22ce-730">Registrazione basata sulle convenzioni</span><span class="sxs-lookup"><span data-stu-id="f22ce-730">Convention-based registration</span></span>

<span data-ttu-id="f22ce-731">Con ASP.NET Core app è possibile usare i contenitori di terze parti seguenti:</span><span class="sxs-lookup"><span data-stu-id="f22ce-731">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="f22ce-732">Autofac</span><span class="sxs-lookup"><span data-stu-id="f22ce-732">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="f22ce-733">DryIoc</span><span class="sxs-lookup"><span data-stu-id="f22ce-733">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="f22ce-734">Laura</span><span class="sxs-lookup"><span data-stu-id="f22ce-734">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="f22ce-735">LightInject</span><span class="sxs-lookup"><span data-stu-id="f22ce-735">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="f22ce-736">Lamar</span><span class="sxs-lookup"><span data-stu-id="f22ce-736">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="f22ce-737">Stashbox</span><span class="sxs-lookup"><span data-stu-id="f22ce-737">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="f22ce-738">Unity</span><span class="sxs-lookup"><span data-stu-id="f22ce-738">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="f22ce-739">Thread safety</span><span class="sxs-lookup"><span data-stu-id="f22ce-739">Thread safety</span></span>

<span data-ttu-id="f22ce-740">Creare servizi singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="f22ce-740">Create thread-safe singleton services.</span></span> <span data-ttu-id="f22ce-741">Se un servizio singleton ha una dipendenza in un servizio temporaneo, è necessario che anche il servizio temporaneo sia thread-safe, a seconda di come viene usato dal singleton.</span><span class="sxs-lookup"><span data-stu-id="f22ce-741">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="f22ce-742">Il metodo factory di un singolo servizio, ad esempio il secondo argomento di [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), non deve essere thread-safe.</span><span class="sxs-lookup"><span data-stu-id="f22ce-742">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="f22ce-743">Come nel caso di un costruttore di tipo (`static`), è garantito che venga chiamato una sola volta da un singolo thread.</span><span class="sxs-lookup"><span data-stu-id="f22ce-743">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="f22ce-744">Consigli</span><span class="sxs-lookup"><span data-stu-id="f22ce-744">Recommendations</span></span>

* <span data-ttu-id="f22ce-745">La risoluzione basata sui servizi `async/await` e `Task` non è supportata.</span><span class="sxs-lookup"><span data-stu-id="f22ce-745">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="f22ce-746">Dato che C# non supporta i costruttori asincroni, il modello consigliato consiste nell'usare i metodi asincroni dopo avere risolto in modo sincrono il servizio.</span><span class="sxs-lookup"><span data-stu-id="f22ce-746">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="f22ce-747">Evitare di archiviare i dati e la configurazione direttamente nel contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="f22ce-747">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="f22ce-748">Ad esempio, il carrello acquisti di un utente non dovrebbe in genere essere aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="f22ce-748">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="f22ce-749">La configurazione deve usare il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="f22ce-749">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="f22ce-750">Analogamente, evitare gli oggetti "contenitori di dati" che hanno la sola funzione di consentire l'accesso ad altri oggetti.</span><span class="sxs-lookup"><span data-stu-id="f22ce-750">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="f22ce-751">È preferibile richiedere l'elemento effettivo tramite inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="f22ce-751">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="f22ce-752">Evitare l'accesso statico ai servizi.</span><span class="sxs-lookup"><span data-stu-id="f22ce-752">Avoid static access to services.</span></span> <span data-ttu-id="f22ce-753">Ad esempio, evitare di digitare in modo statico [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) per l'uso altrove.</span><span class="sxs-lookup"><span data-stu-id="f22ce-753">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="f22ce-754">Evitare di usare il *modello del localizzatore di servizi*, che combina [inversione delle strategie di controllo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="f22ce-754">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="f22ce-755">Non richiamare <xref:System.IServiceProvider.GetService*> per ottenere un'istanza del servizio quando si può usare invece di:</span><span class="sxs-lookup"><span data-stu-id="f22ce-755">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="f22ce-756">**Non corretto:**</span><span class="sxs-lookup"><span data-stu-id="f22ce-756">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="f22ce-757">**Corretto**:</span><span class="sxs-lookup"><span data-stu-id="f22ce-757">**Correct**:</span></span>

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

* <span data-ttu-id="f22ce-758">Evitare di inserire una factory che risolve le dipendenze in fase di esecuzione usando <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="f22ce-758">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="f22ce-759">Evitare l'accesso statico a `HttpContext` (ad esempio [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="f22ce-759">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="f22ce-760">È tuttavia possibile che in alcuni casi queste raccomandazioni debbano essere ignorate.</span><span class="sxs-lookup"><span data-stu-id="f22ce-760">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="f22ce-761">Le eccezioni sono rari, soprattutto casi speciali all'interno del Framework stesso.</span><span class="sxs-lookup"><span data-stu-id="f22ce-761">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="f22ce-762">L'inserimento di dipendenze è un'*alternativa* ai modelli di accesso agli oggetti statici/globali.</span><span class="sxs-lookup"><span data-stu-id="f22ce-762">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="f22ce-763">Se l'inserimento di dipendenze viene usato con l'accesso agli oggetti statico i vantaggi non saranno evidenti.</span><span class="sxs-lookup"><span data-stu-id="f22ce-763">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f22ce-764">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f22ce-764">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="f22ce-765">Quattro modi per eliminare IDisposable in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f22ce-765">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="f22ce-766">Scrittura di codice pulito in ASP.NET Core con inserimento delle dipendenze (MSDN)</span><span class="sxs-lookup"><span data-stu-id="f22ce-766">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="f22ce-767">Explicit Dependencies Principle (Principio delle dipendenze esplicite)</span><span class="sxs-lookup"><span data-stu-id="f22ce-767">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="f22ce-768">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler) (Contenitori di inversione del controllo e modello di inserimento delle dipendenze)</span><span class="sxs-lookup"><span data-stu-id="f22ce-768">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="f22ce-769">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Come registrare un servizio con più interfacce in ASP.NET Core DI)</span><span class="sxs-lookup"><span data-stu-id="f22ce-769">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
