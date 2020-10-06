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
ms.openlocfilehash: 99e0109ea4c2526e9f91a8a4df23c4557e9be83a
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762308"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="10255-103">Inserimento delle dipendenze in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10255-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="10255-104">Di [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="10255-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="10255-105">ASP.NET Core supporta lo schema progettuale di software per l'inserimento di dipendenze, una tecnica per ottenere l'[IoC (Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra le classi e le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="10255-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="10255-106">Per altre informazioni specifiche per l'inserimento delle dipendenze all'interno di controller MVC, vedere <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="10255-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="10255-107">Per ulteriori informazioni sull'inserimento delle dipendenze delle opzioni, vedere <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="10255-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="10255-108">In questo argomento vengono fornite informazioni sull'inserimento delle dipendenze in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="10255-108">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="10255-109">Per informazioni sull'uso dell'inserimento di dipendenze nelle app console, vedere [inserimento delle dipendenze in .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="10255-109">For information on using dependency injection  in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="10255-110">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="10255-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="10255-111">Panoramica dell'inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="10255-111">Overview of dependency injection</span></span>

<span data-ttu-id="10255-112">Una *dipendenza* è un oggetto da cui dipende un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="10255-112">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="10255-113">Esaminare la `MyDependency` classe seguente con un `WriteMessage` metodo da cui dipendono altre classi:</span><span class="sxs-lookup"><span data-stu-id="10255-113">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="10255-114">Una classe può creare un'istanza della `MyDependency` classe per utilizzare il relativo `WriteMessage` metodo.</span><span class="sxs-lookup"><span data-stu-id="10255-114">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="10255-115">Nell'esempio seguente, la `MyDependency` classe è una dipendenza della `IndexModel` classe:</span><span class="sxs-lookup"><span data-stu-id="10255-115">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="10255-116">La classe crea e dipende direttamente dalla `MyDependency` classe.</span><span class="sxs-lookup"><span data-stu-id="10255-116">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="10255-117">Le dipendenze del codice, ad esempio nell'esempio precedente, sono problematiche e devono essere evitate per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="10255-117">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="10255-118">Per sostituire `MyDependency` con un'implementazione diversa, `IndexModel` è necessario modificare la classe.</span><span class="sxs-lookup"><span data-stu-id="10255-118">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="10255-119">Se `MyDependency` dispone di dipendenze, devono anche essere configurate dalla `IndexModel` classe.</span><span class="sxs-lookup"><span data-stu-id="10255-119">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="10255-120">In un progetto di grandi dimensioni con più classi che dipendono da `MyDependency`, il codice di configurazione diventa sparso in tutta l'app.</span><span class="sxs-lookup"><span data-stu-id="10255-120">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="10255-121">È difficile eseguire unit test di questa implementazione.</span><span class="sxs-lookup"><span data-stu-id="10255-121">This implementation is difficult to unit test.</span></span> <span data-ttu-id="10255-122">L'app dovrebbe usare una classe `MyDependency` fittizia o stub, ma ciò non è possibile con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="10255-122">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="10255-123">L'inserimento delle dipendenze consente di risolvere questi problemi tramite:</span><span class="sxs-lookup"><span data-stu-id="10255-123">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="10255-124">L'uso di un'interfaccia o di una classe di base per astrarre l'implementazione delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="10255-124">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="10255-125">La registrazione della dipendenza in un contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-125">Registration of the dependency in a service container.</span></span> <span data-ttu-id="10255-126">ASP.NET Core offre il contenitore di servizi predefinito <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="10255-126">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="10255-127">I servizi vengono in genere registrati nel metodo dell'app `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="10255-127">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="10255-128">L'*inserimento* del servizio nel costruttore della classe in cui viene usato.</span><span class="sxs-lookup"><span data-stu-id="10255-128">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="10255-129">Il framework si assume la responsabilità della creazione di un'istanza della dipendenza e della sua eliminazione quando non è più necessaria.</span><span class="sxs-lookup"><span data-stu-id="10255-129">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="10255-130">Nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l' `IMyDependency` interfaccia definisce il `WriteMessage` Metodo:</span><span class="sxs-lookup"><span data-stu-id="10255-130">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="10255-131">Questa interfaccia viene implementata da un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="10255-131">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="10255-132">L'app di esempio registra il `IMyDependency` servizio con il tipo concreto `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="10255-132">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="10255-133">Il <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> metodo registra il servizio con una durata con ambito, la durata di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="10255-133">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="10255-134">Le [durate dei servizi](#service-lifetimes) sono descritte più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="10255-134">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="10255-135">Nell'app di esempio, il `IMyDependency` servizio viene richiesto e usato per chiamare il `WriteMessage` Metodo:</span><span class="sxs-lookup"><span data-stu-id="10255-135">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="10255-136">Utilizzando il modello DI, il controller:</span><span class="sxs-lookup"><span data-stu-id="10255-136">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="10255-137">Non usa il tipo concreto `MyDependency` , bensì solo l' `IMyDependency` interfaccia che implementa.</span><span class="sxs-lookup"><span data-stu-id="10255-137">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="10255-138">Questo consente di modificare facilmente l'implementazione utilizzata dal controller senza modificare il controller.</span><span class="sxs-lookup"><span data-stu-id="10255-138">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="10255-139">Non crea un'istanza di `MyDependency` , viene creata dal contenitore di.</span><span class="sxs-lookup"><span data-stu-id="10255-139">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="10255-140">L'implementazione dell' `IMyDependency` interfaccia può essere migliorata usando l'API di registrazione incorporata:</span><span class="sxs-lookup"><span data-stu-id="10255-140">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="10255-141">Il `ConfigureServices` metodo aggiornato registra la nuova `IMyDependency` implementazione:</span><span class="sxs-lookup"><span data-stu-id="10255-141">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="10255-142">`MyDependency2` dipende da <xref:Microsoft.Extensions.Logging.ILogger%601> , che richiede nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="10255-142">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="10255-143">`ILogger<TCategoryName>` è un [servizio fornito dal Framework](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="10255-143">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="10255-144">Non è insolito usare l'inserimento delle dipendenze in modo concatenato.</span><span class="sxs-lookup"><span data-stu-id="10255-144">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="10255-145">Ogni dipendenza richiesta richiede a sua volta le proprie dipendenze.</span><span class="sxs-lookup"><span data-stu-id="10255-145">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="10255-146">Il contenitore risolve le dipendenze nel grafico e restituisce il servizio completamente risolto.</span><span class="sxs-lookup"><span data-stu-id="10255-146">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="10255-147">Il set di dipendenze che devono essere risolte viene generalmente chiamato *albero delle dipendenze* o *grafico dipendenze* o *grafico degli oggetti*.</span><span class="sxs-lookup"><span data-stu-id="10255-147">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="10255-148">Il contenitore si risolve sfruttando `ILogger<TCategoryName>` i vantaggi dei [tipi aperti (generici)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando la necessità di registrare tutti i tipi [costruiti (generici)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="10255-148">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="10255-149">Nella terminologia relativa all'inserimento delle dipendenze, un servizio:</span><span class="sxs-lookup"><span data-stu-id="10255-149">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="10255-150">È in genere un oggetto che fornisce un servizio ad altri oggetti, ad esempio il `IMyDependency` servizio.</span><span class="sxs-lookup"><span data-stu-id="10255-150">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="10255-151">Non è correlato a un servizio Web, anche se il servizio può utilizzare un servizio Web.</span><span class="sxs-lookup"><span data-stu-id="10255-151">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="10255-152">Il Framework fornisce un sistema di [registrazione](xref:fundamentals/logging/index) affidabile.</span><span class="sxs-lookup"><span data-stu-id="10255-152">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="10255-153">Le `IMyDependency` implementazioni illustrate negli esempi precedenti sono state scritte per illustrare il di base di, non per implementare la registrazione.</span><span class="sxs-lookup"><span data-stu-id="10255-153">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="10255-154">La maggior parte delle app non deve scrivere logger.</span><span class="sxs-lookup"><span data-stu-id="10255-154">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="10255-155">Il codice seguente illustra l'uso della registrazione predefinita, che non richiede la registrazione dei servizi in `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="10255-155">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="10255-156">Con il codice precedente non è necessario aggiornare `ConfigureServices` , perché la [registrazione](xref:fundamentals/logging/index) viene fornita dal Framework.</span><span class="sxs-lookup"><span data-stu-id="10255-156">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="10255-157">Servizi inseriti all'avvio</span><span class="sxs-lookup"><span data-stu-id="10255-157">Services injected into Startup</span></span>

<span data-ttu-id="10255-158">I servizi possono essere inseriti nel `Startup` costruttore e nel `Startup.Configure` metodo.</span><span class="sxs-lookup"><span data-stu-id="10255-158">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="10255-159">`Startup`Quando si utilizza l'host generico () è possibile inserire nel costruttore solo i servizi seguenti <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="10255-159">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="10255-160">Qualsiasi servizio registrato con il contenitore DI inserimento delle dipendenze può essere inserito nel `Startup.Configure` Metodo:</span><span class="sxs-lookup"><span data-stu-id="10255-160">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="10255-161">Per ulteriori informazioni, vedere <xref:fundamentals/startup> e [accedere alla configurazione all'avvio](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="10255-161">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="10255-162">Registrare gruppi di servizi con i metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="10255-162">Register groups of services with extension methods</span></span>

<span data-ttu-id="10255-163">Il framework ASP.NET Core usa una convenzione per la registrazione di un gruppo di servizi correlati.</span><span class="sxs-lookup"><span data-stu-id="10255-163">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="10255-164">La convenzione prevede l'uso di un singolo `Add{GROUP_NAME}` metodo di estensione per registrare tutti i servizi richiesti da una funzionalità del Framework.</span><span class="sxs-lookup"><span data-stu-id="10255-164">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="10255-165">Ad esempio, il metodo <Microsoft. Extensions. DependencyInjection. MvcServiceCollectionExtensions. AddControllers> Extension registra i servizi necessari per i controller MVC.</span><span class="sxs-lookup"><span data-stu-id="10255-165">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="10255-166">Il codice seguente viene generato dal Razor modello di pagine usando singoli account utente e Mostra come aggiungere altri servizi al contenitore usando i metodi di estensione <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="10255-166">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="10255-167">Durate del servizio</span><span class="sxs-lookup"><span data-stu-id="10255-167">Service lifetimes</span></span>

<span data-ttu-id="10255-168">I servizi possono essere registrati con una delle seguenti durate:</span><span class="sxs-lookup"><span data-stu-id="10255-168">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="10255-169">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="10255-169">Transient</span></span>
* <span data-ttu-id="10255-170">Con ambito</span><span class="sxs-lookup"><span data-stu-id="10255-170">Scoped</span></span>
* <span data-ttu-id="10255-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-171">Singleton</span></span>

<span data-ttu-id="10255-172">Nelle sezioni seguenti vengono descritte tutte le durate precedenti.</span><span class="sxs-lookup"><span data-stu-id="10255-172">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="10255-173">Scegliere una durata appropriata per ogni servizio registrato.</span><span class="sxs-lookup"><span data-stu-id="10255-173">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="10255-174">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="10255-174">Transient</span></span>

<span data-ttu-id="10255-175">I servizi con durata temporanea vengono creati ogni volta che vengono richiesti dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-175">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="10255-176">Questa impostazione di durata è consigliata per i servizi semplici senza stati.</span><span class="sxs-lookup"><span data-stu-id="10255-176">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="10255-177">Registrare servizi temporanei con <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> .</span><span class="sxs-lookup"><span data-stu-id="10255-177">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="10255-178">Nelle app che elaborano le richieste, i servizi temporanei vengono eliminati alla fine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="10255-178">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="10255-179">Con ambito</span><span class="sxs-lookup"><span data-stu-id="10255-179">Scoped</span></span>

<span data-ttu-id="10255-180">I servizi con durata con ambito vengono creati una volta per ogni richiesta client (connessione).</span><span class="sxs-lookup"><span data-stu-id="10255-180">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="10255-181">Registrare i servizi con ambito con <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> .</span><span class="sxs-lookup"><span data-stu-id="10255-181">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="10255-182">Nelle app che elaborano le richieste, i servizi con ambito vengono eliminati alla fine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="10255-182">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="10255-183">Quando si usa Entity Framework Core, il <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> metodo di estensione registra i `DbContext` tipi con una durata con ambito per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="10255-183">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="10255-184">***Non*** risolvere un servizio con ambito da un singleton.</span><span class="sxs-lookup"><span data-stu-id="10255-184">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="10255-185">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="10255-185">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="10255-186">È bene:</span><span class="sxs-lookup"><span data-stu-id="10255-186">It's fine to:</span></span>

* <span data-ttu-id="10255-187">Risolvere un servizio singleton da un servizio con ambito o temporaneo.</span><span class="sxs-lookup"><span data-stu-id="10255-187">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="10255-188">Risolvere un servizio con ambito da un altro servizio con ambito o temporaneo.</span><span class="sxs-lookup"><span data-stu-id="10255-188">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="10255-189">Per impostazione predefinita, nell'ambiente di sviluppo la risoluzione di un servizio da un altro servizio con una durata più lunga genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="10255-189">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="10255-190">Per ulteriori informazioni, vedere [Convalida dell'ambito](#sv).</span><span class="sxs-lookup"><span data-stu-id="10255-190">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="10255-191">Per usare i servizi con ambito nel middleware, usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="10255-191">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="10255-192">Inserire il servizio nel middleware o nel `Invoke` `InvokeAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="10255-192">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="10255-193">L'uso dell' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) genera un'eccezione in fase di esecuzione perché impone il comportamento del servizio con ambito come un singleton.</span><span class="sxs-lookup"><span data-stu-id="10255-193">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="10255-194">Nell'esempio nella sezione relativa alle [Opzioni di durata e registrazione](#lifetime-and-registration-options) viene illustrato l' `InvokeAsync` approccio.</span><span class="sxs-lookup"><span data-stu-id="10255-194">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="10255-195">Usare il [middleware basato su Factory](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="10255-195">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="10255-196">Il middleware registrato con questo approccio viene attivato per ogni richiesta client (connessione), che consente di inserire i servizi con ambito nel metodo del middleware `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="10255-196">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="10255-197">Per altre informazioni, vedere <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="10255-197">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="10255-198">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-198">Singleton</span></span>

<span data-ttu-id="10255-199">I servizi di durata singleton vengono creati:</span><span class="sxs-lookup"><span data-stu-id="10255-199">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="10255-200">La prima volta che viene richiesto.</span><span class="sxs-lookup"><span data-stu-id="10255-200">The first time they're requested.</span></span>
* <span data-ttu-id="10255-201">Dallo sviluppatore, quando si fornisce un'istanza di implementazione direttamente al contenitore.</span><span class="sxs-lookup"><span data-stu-id="10255-201">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="10255-202">Questo approccio è raramente necessario.</span><span class="sxs-lookup"><span data-stu-id="10255-202">This approach is rarely needed.</span></span>

<span data-ttu-id="10255-203">Tutte le richieste successive usano la stessa istanza.</span><span class="sxs-lookup"><span data-stu-id="10255-203">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="10255-204">Se l'app richiede il comportamento singleton, consentire al contenitore del servizio di gestire la durata del servizio.</span><span class="sxs-lookup"><span data-stu-id="10255-204">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="10255-205">Non implementare il modello di progettazione singleton e fornire codice per eliminare il singleton.</span><span class="sxs-lookup"><span data-stu-id="10255-205">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="10255-206">I servizi non devono mai essere eliminati dal codice che ha risolto il servizio dal contenitore.</span><span class="sxs-lookup"><span data-stu-id="10255-206">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="10255-207">Se un tipo o una factory viene registrata come singleton, il contenitore elimina il singleton automaticamente.</span><span class="sxs-lookup"><span data-stu-id="10255-207">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="10255-208">Registrare i servizi singleton con <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A> .</span><span class="sxs-lookup"><span data-stu-id="10255-208">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="10255-209">I servizi singleton devono essere thread-safe e spesso utilizzati nei servizi senza stato.</span><span class="sxs-lookup"><span data-stu-id="10255-209">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="10255-210">Nelle app che elaborano le richieste, i servizi Singleton vengono eliminati quando il <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> viene eliminato alla chiusura dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="10255-210">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="10255-211">Poiché la memoria non viene rilasciata fino a quando l'app non viene arrestata, prendere in considerazione l'uso della memoria con un servizio singleton.</span><span class="sxs-lookup"><span data-stu-id="10255-211">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="10255-212">***Non*** risolvere un servizio con ambito da un singleton.</span><span class="sxs-lookup"><span data-stu-id="10255-212">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="10255-213">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="10255-213">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="10255-214">È possibile risolvere un servizio singleton da un servizio con ambito o temporaneo.</span><span class="sxs-lookup"><span data-stu-id="10255-214">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="10255-215">Metodi di registrazione del servizio</span><span class="sxs-lookup"><span data-stu-id="10255-215">Service registration methods</span></span>

<span data-ttu-id="10255-216">Il Framework fornisce metodi di estensione per la registrazione del servizio utili in scenari specifici:</span><span class="sxs-lookup"><span data-stu-id="10255-216">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="10255-217">Metodo</span><span class="sxs-lookup"><span data-stu-id="10255-217">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="10255-218">Automatico</span><span class="sxs-lookup"><span data-stu-id="10255-218">Automatic</span></span><br><span data-ttu-id="10255-219">object</span><span class="sxs-lookup"><span data-stu-id="10255-219">object</span></span><br><span data-ttu-id="10255-220">eliminazione</span><span class="sxs-lookup"><span data-stu-id="10255-220">disposal</span></span> | <span data-ttu-id="10255-221">Più elementi</span><span class="sxs-lookup"><span data-stu-id="10255-221">Multiple</span></span><br><span data-ttu-id="10255-222">implementazioni</span><span class="sxs-lookup"><span data-stu-id="10255-222">implementations</span></span> | <span data-ttu-id="10255-223">Pass args</span><span class="sxs-lookup"><span data-stu-id="10255-223">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="10255-224">Esempio:</span><span class="sxs-lookup"><span data-stu-id="10255-224">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="10255-225">Sì</span><span class="sxs-lookup"><span data-stu-id="10255-225">Yes</span></span>                             | <span data-ttu-id="10255-226">Sì</span><span class="sxs-lookup"><span data-stu-id="10255-226">Yes</span></span>                         | <span data-ttu-id="10255-227">No</span><span class="sxs-lookup"><span data-stu-id="10255-227">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="10255-228">Esempi:</span><span class="sxs-lookup"><span data-stu-id="10255-228">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="10255-229">Sì</span><span class="sxs-lookup"><span data-stu-id="10255-229">Yes</span></span>                             | <span data-ttu-id="10255-230">Sì</span><span class="sxs-lookup"><span data-stu-id="10255-230">Yes</span></span>                         | <span data-ttu-id="10255-231">Sì</span><span class="sxs-lookup"><span data-stu-id="10255-231">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="10255-232">Esempio:</span><span class="sxs-lookup"><span data-stu-id="10255-232">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="10255-233">Sì</span><span class="sxs-lookup"><span data-stu-id="10255-233">Yes</span></span>                             | <span data-ttu-id="10255-234">No</span><span class="sxs-lookup"><span data-stu-id="10255-234">No</span></span>                          | <span data-ttu-id="10255-235">No</span><span class="sxs-lookup"><span data-stu-id="10255-235">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="10255-236">Esempi:</span><span class="sxs-lookup"><span data-stu-id="10255-236">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="10255-237">No</span><span class="sxs-lookup"><span data-stu-id="10255-237">No</span></span>                              | <span data-ttu-id="10255-238">Sì</span><span class="sxs-lookup"><span data-stu-id="10255-238">Yes</span></span>                         | <span data-ttu-id="10255-239">Sì</span><span class="sxs-lookup"><span data-stu-id="10255-239">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="10255-240">Esempi:</span><span class="sxs-lookup"><span data-stu-id="10255-240">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="10255-241">No</span><span class="sxs-lookup"><span data-stu-id="10255-241">No</span></span>                              | <span data-ttu-id="10255-242">No</span><span class="sxs-lookup"><span data-stu-id="10255-242">No</span></span>                          | <span data-ttu-id="10255-243">Sì</span><span class="sxs-lookup"><span data-stu-id="10255-243">Yes</span></span>       |

<span data-ttu-id="10255-244">Per ulteriori informazioni sull'eliminazione dei tipi, vedere la sezione [Eliminazione dei servizi](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="10255-244">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="10255-245">Quando si [simulano i tipi per il test](xref:test/integration-tests#inject-mock-services), è comune usare più implementazioni.</span><span class="sxs-lookup"><span data-stu-id="10255-245">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="10255-246">Il Framework fornisce anche `TryAdd{LIFETIME}` metodi di estensione, che registrano il servizio solo se non è già stata registrata un'implementazione.</span><span class="sxs-lookup"><span data-stu-id="10255-246">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="10255-247">Nell'esempio seguente, la chiamata a `AddSingleton` Registra `MyDependency` come implementazione per `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="10255-247">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="10255-248">La chiamata a `TryAddSingleton` non ha alcun effetto perché `IMyDependency` dispone già di un'implementazione registrata:</span><span class="sxs-lookup"><span data-stu-id="10255-248">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="10255-249">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="10255-249">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="10255-250">I metodi [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) registrano il servizio solo se non esiste già un'implementazione *dello stesso tipo*.</span><span class="sxs-lookup"><span data-stu-id="10255-250">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="10255-251">Più servizi vengono risolti tramite `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="10255-251">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="10255-252">Quando si registrano i servizi, lo sviluppatore deve aggiungere un'istanza se uno degli stessi tipi non è già stato aggiunto.</span><span class="sxs-lookup"><span data-stu-id="10255-252">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="10255-253">In genere, gli autori `TryAddEnumerable` di librerie utilizzano per evitare la registrazione di più copie di un'implementazione nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="10255-253">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="10255-254">Nell'esempio seguente, la prima chiamata a viene `TryAddEnumerable` registrata `MyDependency` come implementazione per `IMyDependency1` .</span><span class="sxs-lookup"><span data-stu-id="10255-254">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="10255-255">La seconda chiamata esegue la registrazione `MyDependency` per `IMyDependency2` .</span><span class="sxs-lookup"><span data-stu-id="10255-255">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="10255-256">La terza chiamata non ha alcun effetto perché `IMyDependency1` dispone già di un'implementazione registrata di `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="10255-256">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="10255-257">La registrazione del servizio è in genere indipendente dall'ordine, tranne quando si registrano più implementazioni dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="10255-257">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="10255-258">`IServiceCollection` è una raccolta di <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> oggetti.</span><span class="sxs-lookup"><span data-stu-id="10255-258">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="10255-259">Nell'esempio seguente viene illustrato come registrare un servizio creando e aggiungendo `ServiceDescriptor` :</span><span class="sxs-lookup"><span data-stu-id="10255-259">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="10255-260">I `Add{LIFETIME}` metodi predefiniti utilizzano lo stesso approccio.</span><span class="sxs-lookup"><span data-stu-id="10255-260">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="10255-261">Vedere ad esempio il [codice sorgente di AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="10255-261">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="10255-262">Comportamento dell'inserimento del costruttore</span><span class="sxs-lookup"><span data-stu-id="10255-262">Constructor injection behavior</span></span>

<span data-ttu-id="10255-263">I servizi possono essere risolti tramite:</span><span class="sxs-lookup"><span data-stu-id="10255-263">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="10255-264"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="10255-264"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="10255-265">Crea oggetti che non sono registrati nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="10255-265">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="10255-266">Usato con le funzionalità del Framework, ad esempio gli [Helper Tag](xref:mvc/views/tag-helpers/intro), i controller MVC e gli elementi di [associazione di modelli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="10255-266">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="10255-267">I costruttori possono accettare argomenti non forniti tramite l'inserimento di dipendenze, ma gli argomenti devono assegnare valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="10255-267">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="10255-268">Quando i servizi vengono risolti da `IServiceProvider` o `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede un costruttore *pubblico* .</span><span class="sxs-lookup"><span data-stu-id="10255-268">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="10255-269">Quando i servizi vengono risolti da `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede l'esistenza di un solo costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="10255-269">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="10255-270">Sebbene siano supportati gli overload dei costruttori, può essere presente un solo overload i cui argomenti possono essere tutti specificati tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="10255-270">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="10255-271">Contesti di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="10255-271">Entity Framework contexts</span></span>

<span data-ttu-id="10255-272">Per impostazione predefinita, i contesti di Entity Framework vengono aggiunti al contenitore del servizio usando la [durata con ambito](#service-lifetimes) poiché le operazioni del database dell'app Web sono in genere limitate all'ambito della richiesta client.</span><span class="sxs-lookup"><span data-stu-id="10255-272">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="10255-273">Per usare una durata diversa, specificare la durata usando un <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> Overload.</span><span class="sxs-lookup"><span data-stu-id="10255-273">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="10255-274">I servizi di una determinata durata non devono utilizzare un contesto di database con una durata minore della durata del servizio.</span><span class="sxs-lookup"><span data-stu-id="10255-274">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="10255-275">Opzioni di durata e di registrazione</span><span class="sxs-lookup"><span data-stu-id="10255-275">Lifetime and registration options</span></span>

<span data-ttu-id="10255-276">Per dimostrare la differenza tra le durate dei servizi e le relative opzioni di registrazione, prendere in considerazione le interfacce seguenti che rappresentano un'attività come operazione con un identificatore `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="10255-276">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="10255-277">A seconda del modo in cui viene configurata la durata del servizio di un'operazione per le interfacce seguenti, il contenitore fornisce le stesse istanze o diverse del servizio quando richiesto da una classe:</span><span class="sxs-lookup"><span data-stu-id="10255-277">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="10255-278">La `Operation` classe seguente implementa tutte le interfacce precedenti.</span><span class="sxs-lookup"><span data-stu-id="10255-278">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="10255-279">Il `Operation` costruttore genera un GUID e archivia gli ultimi 4 caratteri nella `OperationId` proprietà:</span><span class="sxs-lookup"><span data-stu-id="10255-279">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="10255-280">Il `Startup.ConfigureServices` metodo crea più registrazioni della `Operation` classe in base alle durate denominate:</span><span class="sxs-lookup"><span data-stu-id="10255-280">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="10255-281">L'app di esempio illustra le durate degli oggetti all'interno e tra le richieste.</span><span class="sxs-lookup"><span data-stu-id="10255-281">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="10255-282">Il `IndexModel` e il middleware richiedono ogni tipo di `IOperation` tipo e registrano `OperationId` per ogni tipo:</span><span class="sxs-lookup"><span data-stu-id="10255-282">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="10255-283">Analogamente a `IndexModel` , il middleware risolve gli stessi servizi:</span><span class="sxs-lookup"><span data-stu-id="10255-283">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="10255-284">I servizi con ambito devono essere risolti nel `InvokeAsync` Metodo:</span><span class="sxs-lookup"><span data-stu-id="10255-284">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="10255-285">L'output del logger Mostra:</span><span class="sxs-lookup"><span data-stu-id="10255-285">The logger output shows:</span></span>

* <span data-ttu-id="10255-286">Gli oggetti *temporanei* sono sempre diversi.</span><span class="sxs-lookup"><span data-stu-id="10255-286">*Transient* objects are always different.</span></span> <span data-ttu-id="10255-287">Il `OperationId` valore temporaneo è diverso in `IndexModel` e nel middleware.</span><span class="sxs-lookup"><span data-stu-id="10255-287">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="10255-288">Gli oggetti con *ambito* sono gli stessi per ogni richiesta, ma sono diversi in ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="10255-288">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="10255-289">Gli oggetti *singleton* sono gli stessi per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="10255-289">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="10255-290">Per ridurre l'output di registrazione, impostare "Logging: LogLevel: Microsoft: Error" nella *appsettings.Development.jssu* file:</span><span class="sxs-lookup"><span data-stu-id="10255-290">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="10255-291">Chiamare i servizi da main</span><span class="sxs-lookup"><span data-stu-id="10255-291">Call services from main</span></span>

<span data-ttu-id="10255-292">Creare un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>IServiceScope con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) per risolvere un servizio con ambito nell'ambito di applicazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="10255-292">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="10255-293">Questo approccio è utile per l'accesso a un servizio con ambito all'avvio e per l'esecuzione di attività di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="10255-293">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="10255-294">Nell'esempio seguente viene illustrato come accedere al servizio con ambito `IMyDependency` e come chiamare il relativo `WriteMessage` metodo in `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="10255-294">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="10255-295">Convalida dell'ambito</span><span class="sxs-lookup"><span data-stu-id="10255-295">Scope validation</span></span>

<span data-ttu-id="10255-296">Quando l'app viene eseguita nell' [ambiente di sviluppo](xref:fundamentals/environments) e chiama [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) per compilare l'host, il provider di servizi predefinito esegue i controlli per verificare che:</span><span class="sxs-lookup"><span data-stu-id="10255-296">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="10255-297">I servizi con ambito non vengono risolti dal provider di servizi radice.</span><span class="sxs-lookup"><span data-stu-id="10255-297">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="10255-298">I servizi con ambito non vengono inseriti in singleton.</span><span class="sxs-lookup"><span data-stu-id="10255-298">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="10255-299">Il provider di servizi radice venga creato con la chiamata di <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A>.</span><span class="sxs-lookup"><span data-stu-id="10255-299">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="10255-300">La durata del provider di servizi radice corrisponde alla durata dell'app quando il provider inizia con l'app e viene eliminato quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="10255-300">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="10255-301">I servizi con ambito vengono eliminati dal contenitore che li ha creati.</span><span class="sxs-lookup"><span data-stu-id="10255-301">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="10255-302">Se nel contenitore radice viene creato un servizio con ambito, la durata del servizio viene effettivamente promossa a Singleton perché viene eliminata dal contenitore radice solo quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="10255-302">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="10255-303">La convalida degli ambiti servizio rileva queste situazioni nel contesto della chiamata di `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="10255-303">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="10255-304">Per ulteriori informazioni, vedere [Convalida dell'ambito](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="10255-304">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="10255-305">Servizi di richiesta</span><span class="sxs-lookup"><span data-stu-id="10255-305">Request Services</span></span>

<span data-ttu-id="10255-306">I servizi disponibili all'interno di una richiesta di ASP.NET Core vengono esposti tramite la raccolta [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="10255-306">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="10255-307">Quando i servizi vengono richiesti dall'interno di una richiesta, i servizi e le relative dipendenze vengono risolti dalla `RequestServices` raccolta.</span><span class="sxs-lookup"><span data-stu-id="10255-307">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="10255-308">Il Framework crea un ambito per ogni richiesta ed `RequestServices` espone il provider di servizi con ambito.</span><span class="sxs-lookup"><span data-stu-id="10255-308">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="10255-309">Tutti i servizi con ambito sono validi fino a quando la richiesta è attiva.</span><span class="sxs-lookup"><span data-stu-id="10255-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="10255-310">Preferisce richiedere le dipendenze come parametri del costruttore per la risoluzione dei servizi dalla `RequestServices` raccolta.</span><span class="sxs-lookup"><span data-stu-id="10255-310">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="10255-311">In questo modo, le classi risultano più facili da testare.</span><span class="sxs-lookup"><span data-stu-id="10255-311">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="10255-312">Progettare i servizi per l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="10255-312">Design services for dependency injection</span></span>

<span data-ttu-id="10255-313">Durante la progettazione di servizi per l'inserimento di dipendenze:</span><span class="sxs-lookup"><span data-stu-id="10255-313">When designing services for dependency injection:</span></span>

* <span data-ttu-id="10255-314">Evitare membri e classi statiche con stato.</span><span class="sxs-lookup"><span data-stu-id="10255-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="10255-315">Evitare di creare lo stato globale progettando le app in modo da usare i servizi singleton.</span><span class="sxs-lookup"><span data-stu-id="10255-315">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="10255-316">Evitare la creazione diretta di istanze delle classi dipendenti all'interno di servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="10255-317">La creazione diretta di istanze associa il codice a una determinata implementazione.</span><span class="sxs-lookup"><span data-stu-id="10255-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="10255-318">Semplifica la testing dei servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-318">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="10255-319">Se una classe dispone di molte dipendenze inserite, potrebbe essere un segno che la classe ha troppe responsabilità e viola il [principio di singola responsabilità (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="10255-319">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="10255-320">Tentare di effettuare il refactoring della classe spostando alcune delle responsabilità in nuove classi.</span><span class="sxs-lookup"><span data-stu-id="10255-320">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="10255-321">Tenere presente che Razor le classi del modello della pagina delle pagine e le classi controller MVC devono concentrarsi sulle problematiche dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="10255-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="10255-322">Eliminazione dei servizi</span><span class="sxs-lookup"><span data-stu-id="10255-322">Disposal of services</span></span>

<span data-ttu-id="10255-323">Il contenitore chiama <xref:System.IDisposable.Dispose%2A> per i tipi <xref:System.IDisposable> creati.</span><span class="sxs-lookup"><span data-stu-id="10255-323">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="10255-324">I servizi risolti dal contenitore non devono mai essere eliminati dallo sviluppatore.</span><span class="sxs-lookup"><span data-stu-id="10255-324">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="10255-325">Se un tipo o una factory viene registrata come singleton, il contenitore elimina il singleton automaticamente.</span><span class="sxs-lookup"><span data-stu-id="10255-325">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="10255-326">Nell'esempio seguente i servizi vengono creati dal contenitore del servizio ed eliminati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="10255-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="10255-327">La console di debug Visualizza il seguente output dopo ogni aggiornamento della pagina di indice:</span><span class="sxs-lookup"><span data-stu-id="10255-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="10255-328">Servizi non creati dal contenitore del servizio</span><span class="sxs-lookup"><span data-stu-id="10255-328">Services not created by the service container</span></span>

<span data-ttu-id="10255-329">Osservare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="10255-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="10255-330">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="10255-330">In the preceding code:</span></span>

* <span data-ttu-id="10255-331">Le istanze del servizio non vengono create dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="10255-332">Il Framework non elimina automaticamente i servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-332">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="10255-333">Lo sviluppatore è responsabile dell'eliminazione dei servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-333">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="10255-334">Linee guida IDisposable per istanze temporanee e condivise</span><span class="sxs-lookup"><span data-stu-id="10255-334">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="10255-335">Durata temporanea e limitata</span><span class="sxs-lookup"><span data-stu-id="10255-335">Transient, limited lifetime</span></span>

<span data-ttu-id="10255-336">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="10255-336">**Scenario**</span></span>

<span data-ttu-id="10255-337">L'app richiede un' <xref:System.IDisposable> istanza con una durata temporanea per uno degli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="10255-337">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="10255-338">L'istanza viene risolta nell'ambito radice (contenitore radice).</span><span class="sxs-lookup"><span data-stu-id="10255-338">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="10255-339">L'istanza deve essere eliminata prima della fine dell'ambito.</span><span class="sxs-lookup"><span data-stu-id="10255-339">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="10255-340">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="10255-340">**Solution**</span></span>

<span data-ttu-id="10255-341">Usare il modello factory per creare un'istanza all'esterno dell'ambito padre.</span><span class="sxs-lookup"><span data-stu-id="10255-341">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="10255-342">In questa situazione, in genere l'app dispone di un `Create` metodo che chiama direttamente il costruttore del tipo finale.</span><span class="sxs-lookup"><span data-stu-id="10255-342">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="10255-343">Se il tipo finale ha altre dipendenze, la factory può:</span><span class="sxs-lookup"><span data-stu-id="10255-343">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="10255-344">Ricevere un oggetto <xref:System.IServiceProvider> nel relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="10255-344">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="10255-345">Usare <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> per creare un'istanza dell'istanza all'esterno del contenitore, usando il contenitore per le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="10255-345">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="10255-346">Istanza condivisa, durata limitata</span><span class="sxs-lookup"><span data-stu-id="10255-346">Shared instance, limited lifetime</span></span>

<span data-ttu-id="10255-347">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="10255-347">**Scenario**</span></span>

<span data-ttu-id="10255-348">L'app richiede un' <xref:System.IDisposable> istanza condivisa tra più servizi, ma l' <xref:System.IDisposable> istanza deve avere una durata limitata.</span><span class="sxs-lookup"><span data-stu-id="10255-348">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="10255-349">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="10255-349">**Solution**</span></span>

<span data-ttu-id="10255-350">Registrare l'istanza con una durata con ambito.</span><span class="sxs-lookup"><span data-stu-id="10255-350">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="10255-351">Utilizzare <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> per creare un nuovo oggetto <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="10255-351">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="10255-352">Usare l'ambito <xref:System.IServiceProvider> per ottenere i servizi richiesti.</span><span class="sxs-lookup"><span data-stu-id="10255-352">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="10255-353">Eliminare l'ambito quando non è più necessario.</span><span class="sxs-lookup"><span data-stu-id="10255-353">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="10255-354">Linee guida generali sull'IDisposable</span><span class="sxs-lookup"><span data-stu-id="10255-354">General IDisposable guidelines</span></span>

* <span data-ttu-id="10255-355">Non registrare <xref:System.IDisposable> le istanze con una durata temporanea.</span><span class="sxs-lookup"><span data-stu-id="10255-355">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="10255-356">Usare invece il modello Factory.</span><span class="sxs-lookup"><span data-stu-id="10255-356">Use the factory pattern instead.</span></span>
* <span data-ttu-id="10255-357">Non risolvere <xref:System.IDisposable> le istanze con una durata temporanea o con ambito nell'ambito radice.</span><span class="sxs-lookup"><span data-stu-id="10255-357">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="10255-358">L'unica eccezione è rappresentata dal caso in cui l'app crea/ricrea ed Elimina <xref:System.IServiceProvider> , ma questo non è un modello ideale.</span><span class="sxs-lookup"><span data-stu-id="10255-358">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="10255-359">Per <xref:System.IDisposable> la ricezione di una dipendenza tramite di non è necessario che il ricevitore implementi <xref:System.IDisposable> se stesso.</span><span class="sxs-lookup"><span data-stu-id="10255-359">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="10255-360">Il ricevitore della <xref:System.IDisposable> dipendenza non deve chiamare <xref:System.IDisposable.Dispose%2A> su tale dipendenza.</span><span class="sxs-lookup"><span data-stu-id="10255-360">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="10255-361">Utilizzare gli ambiti per controllare la durata dei servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-361">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="10255-362">Gli ambiti non sono gerarchici e non esiste alcuna connessione speciale tra gli ambiti.</span><span class="sxs-lookup"><span data-stu-id="10255-362">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="10255-363">Sostituzione del contenitore di servizi predefinito</span><span class="sxs-lookup"><span data-stu-id="10255-363">Default service container replacement</span></span>

<span data-ttu-id="10255-364">Il contenitore dei servizi incorporato è progettato per soddisfare le esigenze del Framework e della maggior parte delle app consumer.</span><span class="sxs-lookup"><span data-stu-id="10255-364">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="10255-365">È consigliabile usare il contenitore predefinito a meno che non sia necessaria una funzionalità specifica che non supporta, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="10255-365">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="10255-366">Inserimento di proprietà</span><span class="sxs-lookup"><span data-stu-id="10255-366">Property injection</span></span>
* <span data-ttu-id="10255-367">Inserimento in base al nome</span><span class="sxs-lookup"><span data-stu-id="10255-367">Injection based on name</span></span>
* <span data-ttu-id="10255-368">Contenitori figlio</span><span class="sxs-lookup"><span data-stu-id="10255-368">Child containers</span></span>
* <span data-ttu-id="10255-369">Gestione della durata personalizzata</span><span class="sxs-lookup"><span data-stu-id="10255-369">Custom lifetime management</span></span>
* <span data-ttu-id="10255-370">Supporto di `Func<T>` per l'inizializzazione differita</span><span class="sxs-lookup"><span data-stu-id="10255-370">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="10255-371">Registrazione basata sulle convenzioni</span><span class="sxs-lookup"><span data-stu-id="10255-371">Convention-based registration</span></span>

<span data-ttu-id="10255-372">Con ASP.NET Core app è possibile usare i contenitori di terze parti seguenti:</span><span class="sxs-lookup"><span data-stu-id="10255-372">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="10255-373">Autofac</span><span class="sxs-lookup"><span data-stu-id="10255-373">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="10255-374">DryIoc</span><span class="sxs-lookup"><span data-stu-id="10255-374">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="10255-375">Laura</span><span class="sxs-lookup"><span data-stu-id="10255-375">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="10255-376">LightInject</span><span class="sxs-lookup"><span data-stu-id="10255-376">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="10255-377">Lamar</span><span class="sxs-lookup"><span data-stu-id="10255-377">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="10255-378">Stashbox</span><span class="sxs-lookup"><span data-stu-id="10255-378">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="10255-379">Unity</span><span class="sxs-lookup"><span data-stu-id="10255-379">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="10255-380">Thread safety</span><span class="sxs-lookup"><span data-stu-id="10255-380">Thread safety</span></span>

<span data-ttu-id="10255-381">Creare servizi singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="10255-381">Create thread-safe singleton services.</span></span> <span data-ttu-id="10255-382">Se un servizio singleton presenta una dipendenza da un servizio temporaneo, il servizio temporaneo potrebbe richiedere anche thread safety a seconda del modo in cui viene utilizzato dal singleton.</span><span class="sxs-lookup"><span data-stu-id="10255-382">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="10255-383">Il metodo factory di un singolo servizio, ad esempio il secondo argomento di [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), non deve essere thread-safe.</span><span class="sxs-lookup"><span data-stu-id="10255-383">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="10255-384">Analogamente a un `static` Costruttore Type (), è garantita la chiamata solo una volta da un singolo thread.</span><span class="sxs-lookup"><span data-stu-id="10255-384">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="10255-385">Consigli</span><span class="sxs-lookup"><span data-stu-id="10255-385">Recommendations</span></span>

* <span data-ttu-id="10255-386">`async/await` la `Task` risoluzione del servizio basata su non è supportata.</span><span class="sxs-lookup"><span data-stu-id="10255-386">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="10255-387">Poiché C# non supporta costruttori asincroni, utilizzare i metodi asincroni dopo la risoluzione sincrona del servizio.</span><span class="sxs-lookup"><span data-stu-id="10255-387">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="10255-388">Evitare di archiviare i dati e la configurazione direttamente nel contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="10255-388">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="10255-389">Ad esempio, il carrello acquisti di un utente non dovrebbe in genere essere aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="10255-389">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="10255-390">La configurazione deve usare il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="10255-390">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="10255-391">Allo stesso modo, evitare gli oggetti "titolare dei dati" che esistono solo per consentire l'accesso a un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="10255-391">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="10255-392">È preferibile richiedere l'elemento effettivo tramite inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="10255-392">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="10255-393">Evitare l'accesso statico ai servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-393">Avoid static access to services.</span></span> <span data-ttu-id="10255-394">Ad esempio, evitare di acquisire [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) come proprietà o campo statico da usare altrove.</span><span class="sxs-lookup"><span data-stu-id="10255-394">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="10255-395">Mantieni le fabbriche veloci e sincrone.</span><span class="sxs-lookup"><span data-stu-id="10255-395">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="10255-396">Evitare di usare il *modello di localizzatore del servizio*.</span><span class="sxs-lookup"><span data-stu-id="10255-396">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="10255-397">Ad esempio, non richiamare <xref:System.IServiceProvider.GetService%2A> per ottenere un'istanza del servizio quando è invece possibile usare l'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="10255-397">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="10255-398">**Non corretto:**</span><span class="sxs-lookup"><span data-stu-id="10255-398">**Incorrect:**</span></span>

    ![Codice errato](dependency-injection/_static/bad.png)

  <span data-ttu-id="10255-400">**Corretto**:</span><span class="sxs-lookup"><span data-stu-id="10255-400">**Correct**:</span></span>

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
* <span data-ttu-id="10255-401">Un'altra variazione del localizzatore del servizio da evitare è inserire una factory che risolve le dipendenze in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="10255-401">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="10255-402">Queste procedure combinano le strategie di [inversione del controllo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="10255-402">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="10255-403">Evitare l'accesso statico a `HttpContext` (ad esempio [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="10255-403">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="10255-404">Evitare chiamate a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="10255-404">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="10255-405">`BuildServiceProvider`La chiamata in genere si verifica quando lo sviluppatore desidera risolvere un servizio in `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="10255-405">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="10255-406">Si consideri, ad esempio, il caso in cui l'oggetto `LoginPath` viene caricato dalla configurazione.</span><span class="sxs-lookup"><span data-stu-id="10255-406">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="10255-407">Evitare l'approccio seguente:</span><span class="sxs-lookup"><span data-stu-id="10255-407">Avoid the following approach:</span></span>

  ![codice errato durante la chiamata di BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="10255-409">Nell'immagine precedente, selezionando la linea ondulata verde in `services.BuildServiceProvider` viene visualizzato l'avviso ASP0000 seguente:</span><span class="sxs-lookup"><span data-stu-id="10255-409">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="10255-410">ASP0000 la chiamata a' BuildServiceProvider ' dal codice dell'applicazione comporta una copia aggiuntiva dei servizi singleton creati.</span><span class="sxs-lookup"><span data-stu-id="10255-410">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="10255-411">Prendere in considerazione alternative come la dipendenza che inserisce i servizi come parametri per "Configure".</span><span class="sxs-lookup"><span data-stu-id="10255-411">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="10255-412">`BuildServiceProvider`La chiamata di crea un secondo contenitore, che può creare singleton incompleti e causare riferimenti a oggetti grafici tra più contenitori.</span><span class="sxs-lookup"><span data-stu-id="10255-412">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="10255-413">Un modo corretto per ottenere `LoginPath` consiste nell'usare il supporto incorporato del modello di opzioni per l'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="10255-413">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="10255-414">I servizi temporanei Disposable vengono acquisiti dal contenitore per l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="10255-414">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="10255-415">Questa operazione può comportare una perdita di memoria se risolta dal contenitore di primo livello.</span><span class="sxs-lookup"><span data-stu-id="10255-415">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="10255-416">Abilitare la convalida dell'ambito per assicurarsi che l'app non disponga di singleton che acquisiscono i servizi con ambito.</span><span class="sxs-lookup"><span data-stu-id="10255-416">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="10255-417">Per ulteriori informazioni, vedere [Convalida dell'ambito](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="10255-417">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="10255-418">È tuttavia possibile che in alcuni casi queste raccomandazioni debbano essere ignorate.</span><span class="sxs-lookup"><span data-stu-id="10255-418">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="10255-419">Le eccezioni sono rari, soprattutto casi speciali all'interno del Framework stesso.</span><span class="sxs-lookup"><span data-stu-id="10255-419">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="10255-420">L'inserimento di dipendenze è un'*alternativa* ai modelli di accesso agli oggetti statici/globali.</span><span class="sxs-lookup"><span data-stu-id="10255-420">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="10255-421">Se l'inserimento di dipendenze viene usato con l'accesso agli oggetti statico i vantaggi non saranno evidenti.</span><span class="sxs-lookup"><span data-stu-id="10255-421">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="10255-422">Modelli consigliati per il multi-tenant in DI</span><span class="sxs-lookup"><span data-stu-id="10255-422">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="10255-423">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) è un Framework di applicazione per la creazione di applicazioni modulari multi-tenant in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="10255-423">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="10255-424">Per ulteriori informazioni, vedere la [documentazione di Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="10255-424">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="10255-425">Vedere gli [esempi di Orchard Core](https://github.com/OrchardCMS/OrchardCore.Samples) per esempi di come creare app modulari e multi-tenant usando solo il Framework Orchard core senza le funzionalità specifiche di CMS.</span><span class="sxs-lookup"><span data-stu-id="10255-425">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="10255-426">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="10255-426">Framework-provided services</span></span>

<span data-ttu-id="10255-427">Il `Startup.ConfigureServices` metodo registra i servizi usati dall'app, incluse le funzionalità della piattaforma, ad esempio Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="10255-427">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="10255-428">Inizialmente, l'oggetto `IServiceCollection` fornito a `ConfigureServices` dispone di servizi definiti dal Framework a seconda della [configurazione dell'host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="10255-428">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="10255-429">Per le app basate sui modelli di ASP.NET Core, il Framework registra più di 250 servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-429">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="10255-430">La tabella seguente elenca un piccolo esempio di questi servizi registrati dal Framework:</span><span class="sxs-lookup"><span data-stu-id="10255-430">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="10255-431">Tipo di servizio</span><span class="sxs-lookup"><span data-stu-id="10255-431">Service Type</span></span>                                                                                    | <span data-ttu-id="10255-432">Durata</span><span class="sxs-lookup"><span data-stu-id="10255-432">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="10255-433">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="10255-433">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="10255-434">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-434">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="10255-435">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-435">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="10255-436">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="10255-437">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="10255-437">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="10255-438">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="10255-439">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="10255-439">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="10255-440">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-440">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="10255-441">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-441">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="10255-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="10255-443">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="10255-443">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="10255-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-444">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="10255-445">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-445">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="10255-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-446">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="10255-447">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="10255-447">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="10255-448">Modelli DI conferenza NDC per lo sviluppo DI app DI</span><span class="sxs-lookup"><span data-stu-id="10255-448">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="10255-449">Quattro modi per eliminare IDisposable in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10255-449">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="10255-450">Scrittura di codice pulito in ASP.NET Core con inserimento delle dipendenze (MSDN)</span><span class="sxs-lookup"><span data-stu-id="10255-450">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="10255-451">Explicit Dependencies Principle (Principio delle dipendenze esplicite)</span><span class="sxs-lookup"><span data-stu-id="10255-451">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="10255-452">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler) (Contenitori di inversione del controllo e modello di inserimento delle dipendenze)</span><span class="sxs-lookup"><span data-stu-id="10255-452">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="10255-453">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Come registrare un servizio con più interfacce in ASP.NET Core DI)</span><span class="sxs-lookup"><span data-stu-id="10255-453">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="10255-454">Di [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="10255-454">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="10255-455">ASP.NET Core supporta lo schema progettuale di software per l'inserimento di dipendenze, una tecnica per ottenere l'[IoC (Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra le classi e le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="10255-455">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="10255-456">Per altre informazioni specifiche per l'inserimento delle dipendenze all'interno di controller MVC, vedere <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="10255-456">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="10255-457">[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="10255-457">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="10255-458">Panoramica dell'inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="10255-458">Overview of dependency injection</span></span>

<span data-ttu-id="10255-459">Una *dipendenza* è qualsiasi oggetto richiesto da un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="10255-459">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="10255-460">Esaminare la classe `MyDependency` seguente con un metodo `WriteMessage` da cui dipendono altre classi in un'app:</span><span class="sxs-lookup"><span data-stu-id="10255-460">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="10255-461">È possibile creare un'istanza della classe `MyDependency` per rendere il metodo `WriteMessage`disponibile per una classe.</span><span class="sxs-lookup"><span data-stu-id="10255-461">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="10255-462">La classe `MyDependency` è una dipendenza della classe `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="10255-462">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="10255-463">La classe crea e dipende direttamente dall'istanza `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="10255-463">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="10255-464">Le dipendenze del codice (come nell'esempio precedente) sono problematiche e devono essere evitate per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="10255-464">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="10255-465">Per sostituire `MyDependency` con un'implementazione diversa, la classe deve essere modificata.</span><span class="sxs-lookup"><span data-stu-id="10255-465">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="10255-466">Se `MyDependency` ha dipendenze, devono essere configurate dalla classe.</span><span class="sxs-lookup"><span data-stu-id="10255-466">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="10255-467">In un progetto di grandi dimensioni con più classi che dipendono da `MyDependency`, il codice di configurazione diventa sparso in tutta l'app.</span><span class="sxs-lookup"><span data-stu-id="10255-467">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="10255-468">È difficile eseguire unit test di questa implementazione.</span><span class="sxs-lookup"><span data-stu-id="10255-468">This implementation is difficult to unit test.</span></span> <span data-ttu-id="10255-469">L'app dovrebbe usare una classe `MyDependency` fittizia o stub, ma ciò non è possibile con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="10255-469">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="10255-470">L'inserimento delle dipendenze consente di risolvere questi problemi tramite:</span><span class="sxs-lookup"><span data-stu-id="10255-470">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="10255-471">L'uso di un'interfaccia o di una classe di base per astrarre l'implementazione delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="10255-471">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="10255-472">La registrazione della dipendenza in un contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-472">Registration of the dependency in a service container.</span></span> <span data-ttu-id="10255-473">ASP.NET Core offre il contenitore di servizi predefinito <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="10255-473">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="10255-474">I servizi vengono registrati nel metodo `Startup.ConfigureServices` dell'app.</span><span class="sxs-lookup"><span data-stu-id="10255-474">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="10255-475">L'*inserimento* del servizio nel costruttore della classe in cui viene usato.</span><span class="sxs-lookup"><span data-stu-id="10255-475">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="10255-476">Il framework si assume la responsabilità della creazione di un'istanza della dipendenza e della sua eliminazione quando non è più necessaria.</span><span class="sxs-lookup"><span data-stu-id="10255-476">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="10255-477">Nell'[app di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l'interfaccia `IMyDependency` definisce un metodo che il servizio fornisce all'app:</span><span class="sxs-lookup"><span data-stu-id="10255-477">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="10255-478">Questa interfaccia viene implementata da un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="10255-478">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="10255-479">`MyDependency` richiede <xref:Microsoft.Extensions.Logging.ILogger`1> nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="10255-479">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="10255-480">Non è insolito usare l'inserimento delle dipendenze in modo concatenato.</span><span class="sxs-lookup"><span data-stu-id="10255-480">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="10255-481">Ogni dipendenza richiesta richiede a sua volta le proprie dipendenze.</span><span class="sxs-lookup"><span data-stu-id="10255-481">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="10255-482">Il contenitore risolve le dipendenze nel grafico e restituisce il servizio completamente risolto.</span><span class="sxs-lookup"><span data-stu-id="10255-482">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="10255-483">Il set di dipendenze che devono essere risolte viene generalmente chiamato *albero delle dipendenze* o *grafico dipendenze* o *grafico degli oggetti*.</span><span class="sxs-lookup"><span data-stu-id="10255-483">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="10255-484">`IMyDependency` e `ILogger<TCategoryName>` devono essere registrati nel contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-484">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="10255-485">`IMyDependency` viene registrato in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="10255-485">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="10255-486">`ILogger<TCategoryName>` viene registrato dall'infrastruttura di registrazione delle astrazioni, pertanto si tratta di un [servizio fornito dal Framework](#framework-provided-services) registrato per impostazione predefinita dal Framework.</span><span class="sxs-lookup"><span data-stu-id="10255-486">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="10255-487">Il contenitore risolve `ILogger<TCategoryName>` avvalendosi di [tipi aperti (generici)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando l'esigenza di registrare ogni singolo [tipo costruito (generico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="10255-487">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="10255-488">Nell'app di esempio, il servizio `IMyDependency` viene registrato con il tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="10255-488">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="10255-489">La registrazione definisce come ambito della durata di servizio la durata di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="10255-489">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="10255-490">Le [durate dei servizi](#service-lifetimes) sono descritte più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="10255-490">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="10255-491">Ogni metodo di estensione `services.Add{SERVICE_NAME}` aggiunge (e potenzialmente configura) i servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-491">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="10255-492">Ad esempio, `services.AddMvc()` aggiunge le pagine dei servizi Razor e MVC require.</span><span class="sxs-lookup"><span data-stu-id="10255-492">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="10255-493">È consigliabile che le app seguano questa convenzione.</span><span class="sxs-lookup"><span data-stu-id="10255-493">We recommended that apps follow this convention.</span></span> <span data-ttu-id="10255-494">Inserire i metodi di estensione nello spazio dei nomi [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) per incapsulare i gruppi di registrazioni di servizio.</span><span class="sxs-lookup"><span data-stu-id="10255-494">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="10255-495">Se il costruttore del servizio richiede un [tipo incorporato](/dotnet/csharp/language-reference/keywords/built-in-types-table), ad esempio `string`, è possibile inserirlo usando la [configurazione](xref:fundamentals/configuration/index) o il [modello di opzioni](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="10255-495">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="10255-496">È richiesta un'istanza del servizio tramite il costruttore di una classe in cui il servizio viene usato e assegnato a un campo privato.</span><span class="sxs-lookup"><span data-stu-id="10255-496">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="10255-497">Il campo viene usato per accedere al servizio in base alle esigenze per l'intera classe.</span><span class="sxs-lookup"><span data-stu-id="10255-497">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="10255-498">Nell'app di esempio, viene richiesta l'istanza `IMyDependency` usata per chiamare il metodo `WriteMessage` del servizio:</span><span class="sxs-lookup"><span data-stu-id="10255-498">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="10255-499">Servizi inseriti all'avvio</span><span class="sxs-lookup"><span data-stu-id="10255-499">Services injected into Startup</span></span>

<span data-ttu-id="10255-500">`Startup`Quando si utilizza l'host generico () è possibile inserire nel costruttore solo i tipi di servizio seguenti <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="10255-500">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="10255-501">I servizi possono essere inseriti in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="10255-501">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="10255-502">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="10255-502">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="10255-503">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="10255-503">Framework-provided services</span></span>

<span data-ttu-id="10255-504">Il `Startup.ConfigureServices` metodo è responsabile della definizione dei servizi utilizzati dall'app, incluse le funzionalità della piattaforma, ad esempio Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="10255-504">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="10255-505">Inizialmente, l'oggetto `IServiceCollection` fornito a `ConfigureServices` dispone di servizi definiti dal Framework a seconda della [configurazione dell'host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="10255-505">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="10255-506">Non è insolito che un'app basata su un modello di ASP.NET Core disponga di centinaia di servizi registrati dal Framework.</span><span class="sxs-lookup"><span data-stu-id="10255-506">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="10255-507">Nella tabella seguente è elencato un piccolo esempio di servizi registrati dal Framework.</span><span class="sxs-lookup"><span data-stu-id="10255-507">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="10255-508">Tipo di servizio</span><span class="sxs-lookup"><span data-stu-id="10255-508">Service Type</span></span> | <span data-ttu-id="10255-509">Durata</span><span class="sxs-lookup"><span data-stu-id="10255-509">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="10255-510">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="10255-510">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="10255-511">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-511">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="10255-512">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-512">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="10255-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="10255-514">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="10255-514">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="10255-515">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-515">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="10255-516">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="10255-516">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="10255-517">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-517">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="10255-518">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-518">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="10255-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="10255-520">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="10255-520">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="10255-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-521">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="10255-522">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-522">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="10255-523">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-523">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="10255-524">Registrare servizi aggiuntivi con i metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="10255-524">Register additional services with extension methods</span></span>

<span data-ttu-id="10255-525">Quando è disponibile un metodo di estensione della raccolta di servizi per la registrazione di un servizio (e dei servizi dipendenti, se necessario), la convenzione consiste nell'usare un singolo metodo di estensione `Add{SERVICE_NAME}` per registrare tutti i servizi richiesti da tale servizio.</span><span class="sxs-lookup"><span data-stu-id="10255-525">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="10255-526">Il codice seguente è un esempio di come aggiungere servizi aggiuntivi al contenitore usando i metodi di estensione [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="10255-526">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="10255-527">Per altre informazioni, vedere la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> nella documentazione delle API.</span><span class="sxs-lookup"><span data-stu-id="10255-527">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="10255-528">Durate del servizio</span><span class="sxs-lookup"><span data-stu-id="10255-528">Service lifetimes</span></span>

<span data-ttu-id="10255-529">Scegliere una durata appropriata per ogni servizio registrato.</span><span class="sxs-lookup"><span data-stu-id="10255-529">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="10255-530">I servizi ASP.NET Core possono essere configurati con le impostazioni di durata seguenti:</span><span class="sxs-lookup"><span data-stu-id="10255-530">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="10255-531">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="10255-531">Transient</span></span>

<span data-ttu-id="10255-532">I servizi con durata temporanea (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) vengono creati ogni volta che vengono richiesti dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-532">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="10255-533">Questa impostazione di durata è consigliata per i servizi semplici senza stati.</span><span class="sxs-lookup"><span data-stu-id="10255-533">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="10255-534">Nelle app che elaborano le richieste, i servizi temporanei vengono eliminati alla fine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="10255-534">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="10255-535">Con ambito</span><span class="sxs-lookup"><span data-stu-id="10255-535">Scoped</span></span>

<span data-ttu-id="10255-536">I servizi con durata con ambito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) vengono creati una volta per ogni richiesta client (connessione).</span><span class="sxs-lookup"><span data-stu-id="10255-536">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="10255-537">Nelle app che elaborano le richieste, i servizi con ambito vengono eliminati alla fine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="10255-537">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="10255-538">Quando si usa un servizio con ambito in un middleware, inserire il servizio nel metodo `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="10255-538">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="10255-539">Non inserire tramite il [Costruttore Injection](xref:mvc/controllers/dependency-injection#constructor-injection) perché impone al servizio di comportarsi come un singleton.</span><span class="sxs-lookup"><span data-stu-id="10255-539">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="10255-540">Per altre informazioni, vedere <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="10255-540">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="10255-541">Singleton</span><span class="sxs-lookup"><span data-stu-id="10255-541">Singleton</span></span>

<span data-ttu-id="10255-542">I servizi con durata singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) vengono creati la prima volta che vengono richiesti (o quando si esegue `Startup.ConfigureServices` e viene specificata un'istanza con la registrazione del servizio).</span><span class="sxs-lookup"><span data-stu-id="10255-542">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="10255-543">Tutte le richieste successive usano la stessa istanza.</span><span class="sxs-lookup"><span data-stu-id="10255-543">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="10255-544">Se l'app richiede un comportamento singleton, è consigliabile consentire al contenitore del servizio di gestire la durata del servizio.</span><span class="sxs-lookup"><span data-stu-id="10255-544">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="10255-545">Non implementare lo schema progettuale singleton e fornire codice utente per gestire la durata dell'oggetto nella classe.</span><span class="sxs-lookup"><span data-stu-id="10255-545">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="10255-546">Nelle app che elaborano le richieste, i servizi Singleton vengono eliminati quando il <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> viene eliminato alla chiusura dell'app.</span><span class="sxs-lookup"><span data-stu-id="10255-546">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="10255-547">Può essere pericoloso risolvere un servizio con ambito da un singleton.</span><span class="sxs-lookup"><span data-stu-id="10255-547">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="10255-548">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="10255-548">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="10255-549">Metodi di registrazione del servizio</span><span class="sxs-lookup"><span data-stu-id="10255-549">Service registration methods</span></span>

<span data-ttu-id="10255-550">I metodi di estensione per la registrazione del servizio offrono overload che risultano utili in scenari specifici.</span><span class="sxs-lookup"><span data-stu-id="10255-550">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="10255-551">Metodo</span><span class="sxs-lookup"><span data-stu-id="10255-551">Method</span></span> | <span data-ttu-id="10255-552">Automatico</span><span class="sxs-lookup"><span data-stu-id="10255-552">Automatic</span></span><br><span data-ttu-id="10255-553">object</span><span class="sxs-lookup"><span data-stu-id="10255-553">object</span></span><br><span data-ttu-id="10255-554">eliminazione</span><span class="sxs-lookup"><span data-stu-id="10255-554">disposal</span></span> | <span data-ttu-id="10255-555">Più elementi</span><span class="sxs-lookup"><span data-stu-id="10255-555">Multiple</span></span><br><span data-ttu-id="10255-556">implementazioni</span><span class="sxs-lookup"><span data-stu-id="10255-556">implementations</span></span> | <span data-ttu-id="10255-557">Pass args</span><span class="sxs-lookup"><span data-stu-id="10255-557">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="10255-558">Esempio:</span><span class="sxs-lookup"><span data-stu-id="10255-558">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="10255-559">Sì</span><span class="sxs-lookup"><span data-stu-id="10255-559">Yes</span></span> | <span data-ttu-id="10255-560">Sì</span><span class="sxs-lookup"><span data-stu-id="10255-560">Yes</span></span> | <span data-ttu-id="10255-561">No</span><span class="sxs-lookup"><span data-stu-id="10255-561">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="10255-562">Esempi:</span><span class="sxs-lookup"><span data-stu-id="10255-562">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="10255-563">Sì</span><span class="sxs-lookup"><span data-stu-id="10255-563">Yes</span></span> | <span data-ttu-id="10255-564">Sì</span><span class="sxs-lookup"><span data-stu-id="10255-564">Yes</span></span> | <span data-ttu-id="10255-565">Sì</span><span class="sxs-lookup"><span data-stu-id="10255-565">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="10255-566">Esempio:</span><span class="sxs-lookup"><span data-stu-id="10255-566">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="10255-567">Sì</span><span class="sxs-lookup"><span data-stu-id="10255-567">Yes</span></span> | <span data-ttu-id="10255-568">No</span><span class="sxs-lookup"><span data-stu-id="10255-568">No</span></span> | <span data-ttu-id="10255-569">No</span><span class="sxs-lookup"><span data-stu-id="10255-569">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="10255-570">Esempi:</span><span class="sxs-lookup"><span data-stu-id="10255-570">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="10255-571">No</span><span class="sxs-lookup"><span data-stu-id="10255-571">No</span></span> | <span data-ttu-id="10255-572">Sì</span><span class="sxs-lookup"><span data-stu-id="10255-572">Yes</span></span> | <span data-ttu-id="10255-573">Sì</span><span class="sxs-lookup"><span data-stu-id="10255-573">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="10255-574">Esempi:</span><span class="sxs-lookup"><span data-stu-id="10255-574">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="10255-575">No</span><span class="sxs-lookup"><span data-stu-id="10255-575">No</span></span> | <span data-ttu-id="10255-576">No</span><span class="sxs-lookup"><span data-stu-id="10255-576">No</span></span> | <span data-ttu-id="10255-577">Sì</span><span class="sxs-lookup"><span data-stu-id="10255-577">Yes</span></span> |

<span data-ttu-id="10255-578">Per ulteriori informazioni sull'eliminazione dei tipi, vedere la sezione [Eliminazione dei servizi](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="10255-578">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="10255-579">Uno scenario comune per più implementazioni è costituito dai [tipi di simulazioni per i test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="10255-579">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="10255-580">I metodi `TryAdd{LIFETIME}` registrano il servizio solo se esiste già un'implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="10255-580">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="10255-581">Nell'esempio seguente, la prima riga registra `MyDependency` per `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="10255-581">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="10255-582">La seconda riga non ha alcun effetto perché `IMyDependency` dispone già di un'implementazione registrata:</span><span class="sxs-lookup"><span data-stu-id="10255-582">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="10255-583">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="10255-583">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="10255-584">I metodi [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrano il servizio solo se non esiste già un'implementazione *dello stesso tipo*.</span><span class="sxs-lookup"><span data-stu-id="10255-584">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="10255-585">Più servizi vengono risolti tramite `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="10255-585">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="10255-586">Durante la registrazione di servizi, lo sviluppatore desidera aggiungere un'istanza solo se non ne è già stata aggiunta una dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="10255-586">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="10255-587">In genere, questo metodo viene utilizzato dagli autori di librerie per evitare di registrare due copie di un'istanza nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="10255-587">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="10255-588">Nell'esempio seguente, la prima riga registra `MyDep` per `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="10255-588">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="10255-589">La seconda riga registra `MyDep` per `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="10255-589">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="10255-590">La seconda riga non ha alcun effetto perché `IMyDep1` dispone già di un'implementazione registrata di `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="10255-590">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="10255-591">Comportamento dell'inserimento del costruttore</span><span class="sxs-lookup"><span data-stu-id="10255-591">Constructor injection behavior</span></span>

<span data-ttu-id="10255-592">I servizi possono essere risolti con due meccanismi:</span><span class="sxs-lookup"><span data-stu-id="10255-592">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="10255-593"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Consente la creazione di oggetti senza registrazione del servizio nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="10255-593"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="10255-594">`ActivatorUtilities` viene usato con astrazioni esposte all'utente, ad esempio helper tag, controller MVC e strumenti di associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="10255-594">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="10255-595">I costruttori possono accettare argomenti non forniti tramite l'inserimento di dipendenze, ma gli argomenti devono assegnare valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="10255-595">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="10255-596">Quando i servizi vengono risolti da `IServiceProvider` o `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede un costruttore *pubblico* .</span><span class="sxs-lookup"><span data-stu-id="10255-596">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="10255-597">Quando i servizi vengono risolti da `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede l'esistenza di un solo costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="10255-597">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="10255-598">Sebbene siano supportati gli overload dei costruttori, può essere presente un solo overload i cui argomenti possono essere tutti specificati tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="10255-598">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="10255-599">Contesti di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="10255-599">Entity Framework contexts</span></span>

<span data-ttu-id="10255-600">I contesti di Entity Framework vengono in genere aggiunti al contenitore di servizi usando la [durata con ambito](#service-lifetimes) perché le operazioni di database delle app Web hanno di solito come ambito la richiesta client.</span><span class="sxs-lookup"><span data-stu-id="10255-600">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="10255-601">L'ambito della durata predefinita è se una durata non è specificata da un overload [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) durante la registrazione del contesto del database.</span><span class="sxs-lookup"><span data-stu-id="10255-601">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="10255-602">I servizi con una durata specificata non devono usare un contesto di database con una durata più breve rispetto al servizio.</span><span class="sxs-lookup"><span data-stu-id="10255-602">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="10255-603">Opzioni di durata e di registrazione</span><span class="sxs-lookup"><span data-stu-id="10255-603">Lifetime and registration options</span></span>

<span data-ttu-id="10255-604">Per illustrare la differenza tra le opzioni di durata e registrazione, si considerino le interfacce seguenti che rappresentano le attività come un'operazione con un identificatore univoco, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="10255-604">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="10255-605">A seconda del modo in cui la durata di un servizio operativo viene configurata per le interfacce seguenti, il contenitore fornisce la stessa istanza o un'istanza diversa del servizio quando richiesto da una classe:</span><span class="sxs-lookup"><span data-stu-id="10255-605">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="10255-606">Le interfacce vengono implementate nella classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="10255-606">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="10255-607">Il costruttore `Operation` genera un GUID se non ne viene fornito uno:</span><span class="sxs-lookup"><span data-stu-id="10255-607">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="10255-608">Viene registrato un `OperationService` che dipende da ognuno degli altri tipi `Operation`.</span><span class="sxs-lookup"><span data-stu-id="10255-608">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="10255-609">Quando viene richiesto `OperationService` tramite l'inserimento delle dipendenze, riceve una nuova istanza di ogni servizio o un'istanza esistente in base alla durata del servizio dipendente.</span><span class="sxs-lookup"><span data-stu-id="10255-609">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="10255-610">Se i servizi temporanei vengono creati quando vengono richiesti dal contenitore, `OperationId` del servizio `IOperationTransient` è diverso da `OperationId` di `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="10255-610">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="10255-611">`OperationService` riceve una nuova istanza della classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="10255-611">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="10255-612">La nuova istanza restituisce un `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="10255-612">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="10255-613">Se i servizi con ambito vengono creati per ogni richiesta client, `OperationId` del servizio `IOperationScoped` corrisponde a `OperationService` all'interno di una richiesta client.</span><span class="sxs-lookup"><span data-stu-id="10255-613">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="10255-614">In tutte le richieste client entrambi i servizi condividono un valore `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="10255-614">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="10255-615">Se i servizi singleton e con istanza singleton vengono creati una sola volta e usati per tutte le richieste client e tutti i servizi, `OperationId` rimane costante in tutte le richieste di servizio.</span><span class="sxs-lookup"><span data-stu-id="10255-615">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="10255-616">In `Startup.ConfigureServices`, ogni tipo viene aggiunto al contenitore in base alla durata denominata:</span><span class="sxs-lookup"><span data-stu-id="10255-616">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="10255-617">Il servizio `IOperationSingletonInstance` usa un'istanza specifica con un ID noto di `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="10255-617">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="10255-618">È chiaro quando questo tipo è in uso (il relativo GUID è composto da tutti zero).</span><span class="sxs-lookup"><span data-stu-id="10255-618">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="10255-619">L'app di esempio dimostra le durate degli oggetti all'interno e tra le singole richieste.</span><span class="sxs-lookup"><span data-stu-id="10255-619">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="10255-620">L'`IndexModel` dell'app di esempio richiede ogni genere di tipo `IOperation` e `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="10255-620">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="10255-621">La pagina visualizza quindi tutti i valori `OperationId` della classe del modello di pagina e del servizio tramite assegnazioni di proprietà:</span><span class="sxs-lookup"><span data-stu-id="10255-621">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="10255-622">L'output seguente mostra i risultati delle due richieste:</span><span class="sxs-lookup"><span data-stu-id="10255-622">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="10255-623">**Prima richiesta:**</span><span class="sxs-lookup"><span data-stu-id="10255-623">**First request:**</span></span>

<span data-ttu-id="10255-624">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="10255-624">Controller operations:</span></span>

<span data-ttu-id="10255-625">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="10255-625">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="10255-626">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="10255-626">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="10255-627">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="10255-627">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="10255-628">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="10255-628">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="10255-629">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="10255-629">`OperationService` operations:</span></span>

<span data-ttu-id="10255-630">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="10255-630">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="10255-631">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="10255-631">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="10255-632">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="10255-632">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="10255-633">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="10255-633">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="10255-634">**Seconda richiesta:**</span><span class="sxs-lookup"><span data-stu-id="10255-634">**Second request:**</span></span>

<span data-ttu-id="10255-635">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="10255-635">Controller operations:</span></span>

<span data-ttu-id="10255-636">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="10255-636">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="10255-637">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="10255-637">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="10255-638">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="10255-638">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="10255-639">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="10255-639">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="10255-640">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="10255-640">`OperationService` operations:</span></span>

<span data-ttu-id="10255-641">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="10255-641">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="10255-642">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="10255-642">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="10255-643">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="10255-643">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="10255-644">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="10255-644">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="10255-645">Osservare i valori `OperationId` che variano all'interno della richiesta e da una richiesta e l'altra:</span><span class="sxs-lookup"><span data-stu-id="10255-645">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="10255-646">Gli oggetti *temporanei* sono sempre diversi.</span><span class="sxs-lookup"><span data-stu-id="10255-646">*Transient* objects are always different.</span></span> <span data-ttu-id="10255-647">Il valore `OperationId` temporaneo per la prima e la seconda richiesta client è diverso per entrambe le operazioni `OperationService` e in tutte le richieste client.</span><span class="sxs-lookup"><span data-stu-id="10255-647">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="10255-648">Viene fornita una nuova istanza per ogni richiesta di servizio e richiesta client.</span><span class="sxs-lookup"><span data-stu-id="10255-648">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="10255-649">Gli oggetti *con ambito* sono gli stessi all'interno di una richiesta client, ma variano nelle diverse richieste client.</span><span class="sxs-lookup"><span data-stu-id="10255-649">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="10255-650">Gli oggetti *singleton* sono gli stessi per ogni oggetto e per ogni richiesta, indipendentemente dal fatto che un' `Operation` istanza venga fornita in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="10255-650">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="10255-651">Chiamare i servizi da main</span><span class="sxs-lookup"><span data-stu-id="10255-651">Call services from main</span></span>

<span data-ttu-id="10255-652">Creare un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>IServiceScope con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) per risolvere un servizio con ambito nell'ambito di applicazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="10255-652">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="10255-653">Questo approccio è utile per l'accesso a un servizio con ambito all'avvio e per l'esecuzione di attività di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="10255-653">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="10255-654">L'esempio seguente indica come ottenere un contesto per `MyScopedService` in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="10255-654">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="10255-655">Convalida dell'ambito</span><span class="sxs-lookup"><span data-stu-id="10255-655">Scope validation</span></span>

<span data-ttu-id="10255-656">Quando l'app viene eseguita nell'ambiente di sviluppo, il provider di servizi predefinito esegue controlli per verificare che:</span><span class="sxs-lookup"><span data-stu-id="10255-656">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="10255-657">I servizi con ambito non vengano risolti direttamente o indirettamente dal provider di servizi radice.</span><span class="sxs-lookup"><span data-stu-id="10255-657">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="10255-658">I servizi con ambito non vengano inseriti direttamente o indirettamente in singleton.</span><span class="sxs-lookup"><span data-stu-id="10255-658">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="10255-659">Il provider di servizi radice venga creato con la chiamata di <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="10255-659">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="10255-660">La durata del provider di servizi radice corrisponde alla durata dell'app/server quando il provider viene avviato con l'app e viene eliminato alla chiusura dell'app.</span><span class="sxs-lookup"><span data-stu-id="10255-660">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="10255-661">I servizi con ambito vengono eliminati dal contenitore che li ha creati.</span><span class="sxs-lookup"><span data-stu-id="10255-661">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="10255-662">Se un servizio con ambito viene creato nel contenitore radice, la durata del servizio viene di fatto convertita in singleton, perché il servizio viene eliminato solo dal contenitore radice alla chiusura dell'app/server.</span><span class="sxs-lookup"><span data-stu-id="10255-662">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="10255-663">La convalida degli ambiti servizio rileva queste situazioni nel contesto della chiamata di `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="10255-663">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="10255-664">Per altre informazioni, vedere <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="10255-664">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="10255-665">Servizi di richiesta</span><span class="sxs-lookup"><span data-stu-id="10255-665">Request Services</span></span>

<span data-ttu-id="10255-666">I servizi disponibili all'interno di una richiesta ASP.NET Core da `HttpContext` vengono esposti tramite la raccolta [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="10255-666">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="10255-667">I servizi di richiesta rappresentano i servizi configurati e richiesti come parte dell'app.</span><span class="sxs-lookup"><span data-stu-id="10255-667">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="10255-668">Quando gli oggetti specificano dipendenze, le dipendenze sono soddisfatte dai tipi individuati in `RequestServices`, non in `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="10255-668">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="10255-669">In generale, l'app non deve usare queste proprietà direttamente.</span><span class="sxs-lookup"><span data-stu-id="10255-669">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="10255-670">Richiedere invece i tipi richiesti dalle classi tramite i costruttori di classi e consentire al framework di inserire le dipendenze.</span><span class="sxs-lookup"><span data-stu-id="10255-670">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="10255-671">Si ottengono classi più facili da testare.</span><span class="sxs-lookup"><span data-stu-id="10255-671">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="10255-672">È consigliabile richiedere le dipendenze come parametri del costruttore per l'accesso alla raccolta `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="10255-672">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="10255-673">Progettare i servizi per l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="10255-673">Design services for dependency injection</span></span>

<span data-ttu-id="10255-674">Le procedure consigliate prevedono di:</span><span class="sxs-lookup"><span data-stu-id="10255-674">Best practices are to:</span></span>

* <span data-ttu-id="10255-675">Progettare i servizi per usare l'inserimento delle dipendenze per ottenere le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="10255-675">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="10255-676">Evitare membri e classi statiche con stato.</span><span class="sxs-lookup"><span data-stu-id="10255-676">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="10255-677">Progettare le app in modo da usare i servizi singleton, evitando così di creare uno stato globale.</span><span class="sxs-lookup"><span data-stu-id="10255-677">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="10255-678">Evitare la creazione diretta di istanze delle classi dipendenti all'interno di servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-678">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="10255-679">La creazione diretta di istanze associa il codice a una determinata implementazione.</span><span class="sxs-lookup"><span data-stu-id="10255-679">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="10255-680">Fare in modo che le classi siano piccole, con factoring corretto e facili da testare.</span><span class="sxs-lookup"><span data-stu-id="10255-680">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="10255-681">Se una classe sembra avere troppe dipendenze inserite, ciò significa in genere che la classe ha un numero eccessivo di responsabilità e sta violando il [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (principio di responsabilità singola).</span><span class="sxs-lookup"><span data-stu-id="10255-681">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="10255-682">Tentare di effettuare il refactoring della classe spostando alcune delle responsabilità in una nuova classe.</span><span class="sxs-lookup"><span data-stu-id="10255-682">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="10255-683">Tenere presente che Razor le classi del modello della pagina delle pagine e le classi controller MVC devono concentrarsi sulle problematiche dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="10255-683">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="10255-684">Di conseguenza, le regole business e i dettagli di implementazione di accesso ai dati devono essere inseriti in classi appropriate per queste [problematiche separate](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="10255-684">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="10255-685">Eliminazione dei servizi</span><span class="sxs-lookup"><span data-stu-id="10255-685">Disposal of services</span></span>

<span data-ttu-id="10255-686">Il contenitore chiama <xref:System.IDisposable.Dispose*> per i tipi <xref:System.IDisposable> creati.</span><span class="sxs-lookup"><span data-stu-id="10255-686">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="10255-687">Se un'istanza viene aggiunta al contenitore dal codice utente, non viene eliminata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="10255-687">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="10255-688">Nell'esempio seguente i servizi vengono creati dal contenitore del servizio ed eliminati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="10255-688">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="10255-689">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="10255-689">In the following example:</span></span>

* <span data-ttu-id="10255-690">Le istanze del servizio non vengono create dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-690">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="10255-691">Le durate di servizio previste non sono note dal Framework.</span><span class="sxs-lookup"><span data-stu-id="10255-691">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="10255-692">Il Framework non elimina automaticamente i servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-692">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="10255-693">Se i servizi non vengono eliminati in modo esplicito nel codice dello sviluppatore, vengono mantenuti fino a quando l'app non viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="10255-693">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="10255-694">Linee guida IDisposable per istanze temporanee e condivise</span><span class="sxs-lookup"><span data-stu-id="10255-694">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="10255-695">Durata temporanea e limitata</span><span class="sxs-lookup"><span data-stu-id="10255-695">Transient, limited lifetime</span></span>

<span data-ttu-id="10255-696">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="10255-696">**Scenario**</span></span>

<span data-ttu-id="10255-697">L'app richiede un' <xref:System.IDisposable> istanza con una durata temporanea per uno degli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="10255-697">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="10255-698">L'istanza viene risolta nell'ambito radice.</span><span class="sxs-lookup"><span data-stu-id="10255-698">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="10255-699">L'istanza deve essere eliminata prima della fine dell'ambito.</span><span class="sxs-lookup"><span data-stu-id="10255-699">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="10255-700">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="10255-700">**Solution**</span></span>

<span data-ttu-id="10255-701">Usare il modello factory per creare un'istanza all'esterno dell'ambito padre.</span><span class="sxs-lookup"><span data-stu-id="10255-701">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="10255-702">In questa situazione, in genere l'app dispone di un `Create` metodo che chiama direttamente il costruttore del tipo finale.</span><span class="sxs-lookup"><span data-stu-id="10255-702">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="10255-703">Se il tipo finale ha altre dipendenze, la factory può:</span><span class="sxs-lookup"><span data-stu-id="10255-703">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="10255-704">Ricevere un oggetto <xref:System.IServiceProvider> nel relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="10255-704">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="10255-705">Usare <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> per creare un'istanza dell'istanza all'esterno del contenitore, usando il contenitore per le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="10255-705">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="10255-706">Istanza condivisa, durata limitata</span><span class="sxs-lookup"><span data-stu-id="10255-706">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="10255-707">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="10255-707">**Scenario**</span></span>

<span data-ttu-id="10255-708">L'app richiede un' <xref:System.IDisposable> istanza condivisa tra più servizi, ma <xref:System.IDisposable> deve avere una durata limitata.</span><span class="sxs-lookup"><span data-stu-id="10255-708">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="10255-709">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="10255-709">**Solution**</span></span>

<span data-ttu-id="10255-710">Registrare l'istanza con una durata con ambito.</span><span class="sxs-lookup"><span data-stu-id="10255-710">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="10255-711">Usare <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> per avviare e creare un nuovo oggetto <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="10255-711">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="10255-712">Usare l'ambito <xref:System.IServiceProvider> per ottenere i servizi richiesti.</span><span class="sxs-lookup"><span data-stu-id="10255-712">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="10255-713">Eliminare l'ambito quando la durata deve terminare.</span><span class="sxs-lookup"><span data-stu-id="10255-713">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="10255-714">Linee guida generali</span><span class="sxs-lookup"><span data-stu-id="10255-714">General Guidelines</span></span>

* <span data-ttu-id="10255-715">Non registrare <xref:System.IDisposable> istanze con un ambito temporaneo.</span><span class="sxs-lookup"><span data-stu-id="10255-715">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="10255-716">Usare invece il modello Factory.</span><span class="sxs-lookup"><span data-stu-id="10255-716">Use the factory pattern instead.</span></span>
* <span data-ttu-id="10255-717">Non risolvere istanze temporanee o <xref:System.IDisposable> con ambito nell'ambito radice.</span><span class="sxs-lookup"><span data-stu-id="10255-717">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="10255-718">L'unica eccezione generale si verifica quando l'app crea/ricrea ed Elimina <xref:System.IServiceProvider> , che non è un modello ideale.</span><span class="sxs-lookup"><span data-stu-id="10255-718">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="10255-719">Per <xref:System.IDisposable> la ricezione di una dipendenza tramite di non è necessario che il ricevitore implementi <xref:System.IDisposable> se stesso.</span><span class="sxs-lookup"><span data-stu-id="10255-719">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="10255-720">Il ricevitore della <xref:System.IDisposable> dipendenza non deve chiamare <xref:System.IDisposable.Dispose%2A> su tale dipendenza.</span><span class="sxs-lookup"><span data-stu-id="10255-720">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="10255-721">Gli ambiti devono essere usati per controllare la durata dei servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-721">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="10255-722">Gli ambiti non sono gerarchici e non esiste alcuna connessione speciale tra gli ambiti.</span><span class="sxs-lookup"><span data-stu-id="10255-722">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="10255-723">Sostituzione del contenitore di servizi predefinito</span><span class="sxs-lookup"><span data-stu-id="10255-723">Default service container replacement</span></span>

<span data-ttu-id="10255-724">Il contenitore dei servizi incorporato è progettato per soddisfare le esigenze del Framework e della maggior parte delle app consumer.</span><span class="sxs-lookup"><span data-stu-id="10255-724">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="10255-725">Si consiglia di usare il contenitore predefinito a meno che non sia necessaria una funzionalità specifica che il contenitore incorporato non supporta, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="10255-725">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="10255-726">Inserimento di proprietà</span><span class="sxs-lookup"><span data-stu-id="10255-726">Property injection</span></span>
* <span data-ttu-id="10255-727">Inserimento in base al nome</span><span class="sxs-lookup"><span data-stu-id="10255-727">Injection based on name</span></span>
* <span data-ttu-id="10255-728">Contenitori figlio</span><span class="sxs-lookup"><span data-stu-id="10255-728">Child containers</span></span>
* <span data-ttu-id="10255-729">Gestione della durata personalizzata</span><span class="sxs-lookup"><span data-stu-id="10255-729">Custom lifetime management</span></span>
* <span data-ttu-id="10255-730">Supporto di `Func<T>` per l'inizializzazione differita</span><span class="sxs-lookup"><span data-stu-id="10255-730">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="10255-731">Registrazione basata sulle convenzioni</span><span class="sxs-lookup"><span data-stu-id="10255-731">Convention-based registration</span></span>

<span data-ttu-id="10255-732">Con ASP.NET Core app è possibile usare i contenitori di terze parti seguenti:</span><span class="sxs-lookup"><span data-stu-id="10255-732">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="10255-733">Autofac</span><span class="sxs-lookup"><span data-stu-id="10255-733">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="10255-734">DryIoc</span><span class="sxs-lookup"><span data-stu-id="10255-734">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="10255-735">Laura</span><span class="sxs-lookup"><span data-stu-id="10255-735">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="10255-736">LightInject</span><span class="sxs-lookup"><span data-stu-id="10255-736">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="10255-737">Lamar</span><span class="sxs-lookup"><span data-stu-id="10255-737">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="10255-738">Stashbox</span><span class="sxs-lookup"><span data-stu-id="10255-738">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="10255-739">Unity</span><span class="sxs-lookup"><span data-stu-id="10255-739">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="10255-740">Thread safety</span><span class="sxs-lookup"><span data-stu-id="10255-740">Thread safety</span></span>

<span data-ttu-id="10255-741">Creare servizi singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="10255-741">Create thread-safe singleton services.</span></span> <span data-ttu-id="10255-742">Se un servizio singleton ha una dipendenza in un servizio temporaneo, è necessario che anche il servizio temporaneo sia thread-safe, a seconda di come viene usato dal singleton.</span><span class="sxs-lookup"><span data-stu-id="10255-742">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="10255-743">Il metodo factory di un singolo servizio, ad esempio il secondo argomento di [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), non deve essere thread-safe.</span><span class="sxs-lookup"><span data-stu-id="10255-743">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="10255-744">Come nel caso di un costruttore di tipo (`static`), è garantito che venga chiamato una sola volta da un singolo thread.</span><span class="sxs-lookup"><span data-stu-id="10255-744">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="10255-745">Consigli</span><span class="sxs-lookup"><span data-stu-id="10255-745">Recommendations</span></span>

* <span data-ttu-id="10255-746">La risoluzione basata sui servizi `async/await` e `Task` non è supportata.</span><span class="sxs-lookup"><span data-stu-id="10255-746">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="10255-747">Dato che C# non supporta i costruttori asincroni, il modello consigliato consiste nell'usare i metodi asincroni dopo avere risolto in modo sincrono il servizio.</span><span class="sxs-lookup"><span data-stu-id="10255-747">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="10255-748">Evitare di archiviare i dati e la configurazione direttamente nel contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="10255-748">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="10255-749">Ad esempio, il carrello acquisti di un utente non dovrebbe in genere essere aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="10255-749">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="10255-750">La configurazione deve usare il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="10255-750">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="10255-751">Analogamente, evitare gli oggetti "contenitori di dati" che hanno la sola funzione di consentire l'accesso ad altri oggetti.</span><span class="sxs-lookup"><span data-stu-id="10255-751">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="10255-752">È preferibile richiedere l'elemento effettivo tramite inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="10255-752">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="10255-753">Evitare l'accesso statico ai servizi.</span><span class="sxs-lookup"><span data-stu-id="10255-753">Avoid static access to services.</span></span> <span data-ttu-id="10255-754">Ad esempio, evitare di digitare in modo statico [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) per l'uso altrove.</span><span class="sxs-lookup"><span data-stu-id="10255-754">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="10255-755">Evitare di usare il *modello del localizzatore di servizi*, che combina [inversione delle strategie di controllo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="10255-755">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="10255-756">Non richiamare <xref:System.IServiceProvider.GetService*> per ottenere un'istanza del servizio quando si può usare invece di:</span><span class="sxs-lookup"><span data-stu-id="10255-756">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="10255-757">**Non corretto:**</span><span class="sxs-lookup"><span data-stu-id="10255-757">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="10255-758">**Corretto**:</span><span class="sxs-lookup"><span data-stu-id="10255-758">**Correct**:</span></span>

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

* <span data-ttu-id="10255-759">Evitare di inserire una factory che risolve le dipendenze in fase di esecuzione usando <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="10255-759">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="10255-760">Evitare l'accesso statico a `HttpContext` (ad esempio [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="10255-760">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="10255-761">È tuttavia possibile che in alcuni casi queste raccomandazioni debbano essere ignorate.</span><span class="sxs-lookup"><span data-stu-id="10255-761">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="10255-762">Le eccezioni sono rari, soprattutto casi speciali all'interno del Framework stesso.</span><span class="sxs-lookup"><span data-stu-id="10255-762">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="10255-763">L'inserimento di dipendenze è un'*alternativa* ai modelli di accesso agli oggetti statici/globali.</span><span class="sxs-lookup"><span data-stu-id="10255-763">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="10255-764">Se l'inserimento di dipendenze viene usato con l'accesso agli oggetti statico i vantaggi non saranno evidenti.</span><span class="sxs-lookup"><span data-stu-id="10255-764">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10255-765">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="10255-765">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="10255-766">Quattro modi per eliminare IDisposable in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10255-766">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="10255-767">Scrittura di codice pulito in ASP.NET Core con inserimento delle dipendenze (MSDN)</span><span class="sxs-lookup"><span data-stu-id="10255-767">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="10255-768">Explicit Dependencies Principle (Principio delle dipendenze esplicite)</span><span class="sxs-lookup"><span data-stu-id="10255-768">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="10255-769">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler) (Contenitori di inversione del controllo e modello di inserimento delle dipendenze)</span><span class="sxs-lookup"><span data-stu-id="10255-769">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="10255-770">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Come registrare un servizio con più interfacce in ASP.NET Core DI)</span><span class="sxs-lookup"><span data-stu-id="10255-770">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
