---
title: Inserimento delle dipendenze in ASP.NET Core
author: rick-anderson
description: Informazioni su come ASP.NET Core implementa l'inserimento delle dipendenze e su come usare questa funzionalità.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/dependency-injection
ms.openlocfilehash: 6f677cc4fc26eb9d50ab6e149b7363079ae756a9
ms.sourcegitcommit: c06a5bf419541d17595af30e4cf6f2787c21855e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678569"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="bc4b9-103">Inserimento delle dipendenze in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bc4b9-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bc4b9-104">Di [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="bc4b9-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="bc4b9-105">ASP.NET Core supporta lo schema progettuale di software per l'inserimento di dipendenze, una tecnica per ottenere l'[IoC (Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra le classi e le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="bc4b9-106">Per altre informazioni specifiche per l'inserimento delle dipendenze all'interno di controller MVC, vedere <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="bc4b9-107">Per informazioni sull'uso dell'inserimento di dipendenze nelle app console, vedere [inserimento delle dipendenze in .NET](/dotnet/core/extensions/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="bc4b9-107">For information on using dependency injection in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="bc4b9-108">Per ulteriori informazioni sull'inserimento delle dipendenze delle opzioni, vedere <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-108">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="bc4b9-109">In questo argomento vengono fornite informazioni sull'inserimento delle dipendenze in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-109">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="bc4b9-110">Per informazioni sull'uso dell'inserimento di dipendenze nelle app console, vedere [inserimento delle dipendenze in .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-110">For information on using dependency injection  in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="bc4b9-111">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bc4b9-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="bc4b9-112">Panoramica dell'inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="bc4b9-112">Overview of dependency injection</span></span>

<span data-ttu-id="bc4b9-113">Una *dipendenza* è un oggetto da cui dipende un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-113">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="bc4b9-114">Esaminare la `MyDependency` classe seguente con un `WriteMessage` metodo da cui dipendono altre classi:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="bc4b9-115">Una classe può creare un'istanza della `MyDependency` classe per utilizzare il relativo `WriteMessage` metodo.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-115">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="bc4b9-116">Nell'esempio seguente, la `MyDependency` classe è una dipendenza della `IndexModel` classe:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-116">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="bc4b9-117">La classe crea e dipende direttamente dalla `MyDependency` classe.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-117">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="bc4b9-118">Le dipendenze del codice, ad esempio nell'esempio precedente, sono problematiche e devono essere evitate per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-118">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="bc4b9-119">Per sostituire `MyDependency` con un'implementazione diversa, `IndexModel` è necessario modificare la classe.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-119">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="bc4b9-120">Se `MyDependency` dispone di dipendenze, devono anche essere configurate dalla `IndexModel` classe.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-120">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="bc4b9-121">In un progetto di grandi dimensioni con più classi che dipendono da `MyDependency`, il codice di configurazione diventa sparso in tutta l'app.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="bc4b9-122">È difficile eseguire unit test di questa implementazione.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="bc4b9-123">L'app dovrebbe usare una classe `MyDependency` fittizia o stub, ma ciò non è possibile con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="bc4b9-124">L'inserimento delle dipendenze consente di risolvere questi problemi tramite:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="bc4b9-125">L'uso di un'interfaccia o di una classe di base per astrarre l'implementazione delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="bc4b9-126">La registrazione della dipendenza in un contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="bc4b9-127">ASP.NET Core offre il contenitore di servizi predefinito <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="bc4b9-128">I servizi vengono in genere registrati nel metodo dell'app `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-128">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="bc4b9-129">L' *inserimento* del servizio nel costruttore della classe in cui viene usato.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="bc4b9-130">Il framework si assume la responsabilità della creazione di un'istanza della dipendenza e della sua eliminazione quando non è più necessaria.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="bc4b9-131">Nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l' `IMyDependency` interfaccia definisce il `WriteMessage` Metodo:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="bc4b9-132">Questa interfaccia viene implementata da un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="bc4b9-133">L'app di esempio registra il `IMyDependency` servizio con il tipo concreto `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-133">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="bc4b9-134">Il <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> metodo registra il servizio con una durata con ambito, la durata di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-134">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="bc4b9-135">Le [durate dei servizi](#service-lifetimes) sono descritte più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-135">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="bc4b9-136">Nell'app di esempio, il `IMyDependency` servizio viene richiesto e usato per chiamare il `WriteMessage` Metodo:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-136">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="bc4b9-137">Utilizzando il modello DI, il controller:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-137">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="bc4b9-138">Non usa il tipo concreto `MyDependency` , bensì solo l' `IMyDependency` interfaccia che implementa.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-138">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="bc4b9-139">Questo consente di modificare facilmente l'implementazione utilizzata dal controller senza modificare il controller.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-139">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="bc4b9-140">Non crea un'istanza di `MyDependency` , viene creata dal contenitore di.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-140">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="bc4b9-141">L'implementazione dell' `IMyDependency` interfaccia può essere migliorata usando l'API di registrazione incorporata:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-141">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="bc4b9-142">Il `ConfigureServices` metodo aggiornato registra la nuova `IMyDependency` implementazione:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-142">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="bc4b9-143">`MyDependency2` dipende da <xref:Microsoft.Extensions.Logging.ILogger%601> , che richiede nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-143">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="bc4b9-144">`ILogger<TCategoryName>` è un [servizio fornito dal Framework](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-144">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="bc4b9-145">Non è insolito usare l'inserimento delle dipendenze in modo concatenato.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-145">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="bc4b9-146">Ogni dipendenza richiesta richiede a sua volta le proprie dipendenze.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-146">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="bc4b9-147">Il contenitore risolve le dipendenze nel grafico e restituisce il servizio completamente risolto.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-147">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="bc4b9-148">Il set di dipendenze che devono essere risolte viene generalmente chiamato *albero delle dipendenze* o *grafico dipendenze* o *grafico degli oggetti* .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-148">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph* .</span></span>

<span data-ttu-id="bc4b9-149">Il contenitore si risolve sfruttando `ILogger<TCategoryName>` i vantaggi dei [tipi aperti (generici)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando la necessità di registrare tutti i tipi [costruiti (generici)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-149">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="bc4b9-150">Nella terminologia relativa all'inserimento delle dipendenze, un servizio:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-150">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="bc4b9-151">È in genere un oggetto che fornisce un servizio ad altri oggetti, ad esempio il `IMyDependency` servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-151">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="bc4b9-152">Non è correlato a un servizio Web, anche se il servizio può utilizzare un servizio Web.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-152">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="bc4b9-153">Il Framework fornisce un sistema di [registrazione](xref:fundamentals/logging/index) affidabile.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-153">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="bc4b9-154">Le `IMyDependency` implementazioni illustrate negli esempi precedenti sono state scritte per illustrare il di base di, non per implementare la registrazione.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-154">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="bc4b9-155">La maggior parte delle app non deve scrivere logger.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-155">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="bc4b9-156">Il codice seguente illustra l'uso della registrazione predefinita, che non richiede la registrazione dei servizi in `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="bc4b9-156">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="bc4b9-157">Con il codice precedente non è necessario aggiornare `ConfigureServices` , perché la [registrazione](xref:fundamentals/logging/index) viene fornita dal Framework.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-157">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="bc4b9-158">Servizi inseriti all'avvio</span><span class="sxs-lookup"><span data-stu-id="bc4b9-158">Services injected into Startup</span></span>

<span data-ttu-id="bc4b9-159">I servizi possono essere inseriti nel `Startup` costruttore e nel `Startup.Configure` metodo.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-159">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="bc4b9-160">`Startup`Quando si utilizza l'host generico () è possibile inserire nel costruttore solo i servizi seguenti <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="bc4b9-160">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="bc4b9-161">Qualsiasi servizio registrato con il contenitore DI inserimento delle dipendenze può essere inserito nel `Startup.Configure` Metodo:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-161">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="bc4b9-162">Per ulteriori informazioni, vedere <xref:fundamentals/startup> e [accedere alla configurazione all'avvio](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-162">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="bc4b9-163">Registrare gruppi di servizi con i metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="bc4b9-163">Register groups of services with extension methods</span></span>

<span data-ttu-id="bc4b9-164">Il framework ASP.NET Core usa una convenzione per la registrazione di un gruppo di servizi correlati.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-164">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="bc4b9-165">La convenzione prevede l'uso di un singolo `Add{GROUP_NAME}` metodo di estensione per registrare tutti i servizi richiesti da una funzionalità del Framework.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-165">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="bc4b9-166">Ad esempio, il metodo <Microsoft. Extensions. DependencyInjection. MvcServiceCollectionExtensions. AddControllers> Extension registra i servizi necessari per i controller MVC.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-166">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="bc4b9-167">Il codice seguente viene generato dal :::no-loc(Razor)::: modello di pagine usando singoli account utente e Mostra come aggiungere altri servizi al contenitore usando i metodi di estensione <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> e <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A> :</span><span class="sxs-lookup"><span data-stu-id="bc4b9-167">The following code is generated by the :::no-loc(Razor)::: Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="bc4b9-168">Durate del servizio</span><span class="sxs-lookup"><span data-stu-id="bc4b9-168">Service lifetimes</span></span>

<span data-ttu-id="bc4b9-169">I servizi possono essere registrati con una delle seguenti durate:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-169">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="bc4b9-170">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="bc4b9-170">Transient</span></span>
* <span data-ttu-id="bc4b9-171">Con ambito</span><span class="sxs-lookup"><span data-stu-id="bc4b9-171">Scoped</span></span>
* <span data-ttu-id="bc4b9-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-172">Singleton</span></span>

<span data-ttu-id="bc4b9-173">Nelle sezioni seguenti vengono descritte tutte le durate precedenti.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-173">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="bc4b9-174">Scegliere una durata appropriata per ogni servizio registrato.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-174">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="bc4b9-175">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="bc4b9-175">Transient</span></span>

<span data-ttu-id="bc4b9-176">I servizi con durata temporanea vengono creati ogni volta che vengono richiesti dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-176">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="bc4b9-177">Questa impostazione di durata è consigliata per i servizi semplici senza stati.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-177">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="bc4b9-178">Registrare servizi temporanei con <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-178">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="bc4b9-179">Nelle app che elaborano le richieste, i servizi temporanei vengono eliminati alla fine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-179">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="bc4b9-180">Con ambito</span><span class="sxs-lookup"><span data-stu-id="bc4b9-180">Scoped</span></span>

<span data-ttu-id="bc4b9-181">I servizi con durata con ambito vengono creati una volta per ogni richiesta client (connessione).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-181">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="bc4b9-182">Registrare i servizi con ambito con <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-182">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="bc4b9-183">Nelle app che elaborano le richieste, i servizi con ambito vengono eliminati alla fine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-183">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="bc4b9-184">Quando si usa Entity Framework Core, il <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> metodo di estensione registra i `DbContext` tipi con una durata con ambito per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-184">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="bc4b9-185">Do \* **not** _ risolve un servizio con ambito da un singleton e prestare attenzione a non eseguire questa operazione indirettamente, ad esempio tramite un servizio temporaneo.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-185">Do \* **not** _ resolve a scoped service from a singleton and be careful not to do so indirectly, for example, through a transient service.</span></span> <span data-ttu-id="bc4b9-186">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-186">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="bc4b9-187">È bene:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-187">It's fine to:</span></span>

<span data-ttu-id="bc4b9-188">_ Risolvere un servizio singleton da un servizio con ambito o temporaneo.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-188">_ Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="bc4b9-189">Risolvere un servizio con ambito da un altro servizio con ambito o temporaneo.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-189">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="bc4b9-190">Per impostazione predefinita, nell'ambiente di sviluppo la risoluzione di un servizio da un altro servizio con una durata più lunga genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-190">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="bc4b9-191">Per ulteriori informazioni, vedere [Convalida dell'ambito](#sv).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-191">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="bc4b9-192">Per usare i servizi con ambito nel middleware, usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-192">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="bc4b9-193">Inserire il servizio nel middleware o nel `Invoke` `InvokeAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-193">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="bc4b9-194">L'uso dell' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) genera un'eccezione in fase di esecuzione perché impone il comportamento del servizio con ambito come un singleton.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-194">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="bc4b9-195">Nell'esempio nella sezione relativa alle [Opzioni di durata e registrazione](#lifetime-and-registration-options) viene illustrato l' `InvokeAsync` approccio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-195">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="bc4b9-196">Usare il [middleware basato su Factory](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-196">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="bc4b9-197">Il middleware registrato con questo approccio viene attivato per ogni richiesta client (connessione), che consente di inserire i servizi con ambito nel metodo del middleware `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-197">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="bc4b9-198">Per altre informazioni, vedere <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-198">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="bc4b9-199">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-199">Singleton</span></span>

<span data-ttu-id="bc4b9-200">I servizi di durata singleton vengono creati:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-200">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="bc4b9-201">La prima volta che viene richiesto.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-201">The first time they're requested.</span></span>
* <span data-ttu-id="bc4b9-202">Dallo sviluppatore, quando si fornisce un'istanza di implementazione direttamente al contenitore.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-202">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="bc4b9-203">Questo approccio è raramente necessario.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-203">This approach is rarely needed.</span></span>

<span data-ttu-id="bc4b9-204">Tutte le richieste successive usano la stessa istanza.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-204">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="bc4b9-205">Se l'app richiede il comportamento singleton, consentire al contenitore del servizio di gestire la durata del servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-205">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="bc4b9-206">Non implementare il modello di progettazione singleton e fornire codice per eliminare il singleton.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-206">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="bc4b9-207">I servizi non devono mai essere eliminati dal codice che ha risolto il servizio dal contenitore.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-207">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="bc4b9-208">Se un tipo o una factory viene registrata come singleton, il contenitore elimina il singleton automaticamente.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-208">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="bc4b9-209">Registrare i servizi singleton con <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A> .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-209">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="bc4b9-210">I servizi singleton devono essere thread-safe e spesso utilizzati nei servizi senza stato.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-210">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="bc4b9-211">Nelle app che elaborano le richieste, i servizi Singleton vengono eliminati quando il <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> viene eliminato alla chiusura dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-211">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="bc4b9-212">Poiché la memoria non viene rilasciata fino a quando l'app non viene arrestata, prendere in considerazione l'uso della memoria con un servizio singleton.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-212">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="bc4b9-213">Do \* **not** _ risolve un servizio con ambito da un singleton.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-213">Do \* **not** _ resolve a scoped service from a singleton.</span></span> <span data-ttu-id="bc4b9-214">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-214">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="bc4b9-215">È possibile risolvere un servizio singleton da un servizio con ambito o temporaneo.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-215">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="bc4b9-216">Metodi di registrazione del servizio</span><span class="sxs-lookup"><span data-stu-id="bc4b9-216">Service registration methods</span></span>

<span data-ttu-id="bc4b9-217">Il Framework fornisce metodi di estensione per la registrazione del servizio utili in scenari specifici:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-217">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="bc4b9-218">Metodo</span><span class="sxs-lookup"><span data-stu-id="bc4b9-218">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="bc4b9-219">Automatico</span><span class="sxs-lookup"><span data-stu-id="bc4b9-219">Automatic</span></span><br><span data-ttu-id="bc4b9-220">object</span><span class="sxs-lookup"><span data-stu-id="bc4b9-220">object</span></span><br><span data-ttu-id="bc4b9-221">eliminazione</span><span class="sxs-lookup"><span data-stu-id="bc4b9-221">disposal</span></span> | <span data-ttu-id="bc4b9-222">Più elementi</span><span class="sxs-lookup"><span data-stu-id="bc4b9-222">Multiple</span></span><br><span data-ttu-id="bc4b9-223">implementazioni</span><span class="sxs-lookup"><span data-stu-id="bc4b9-223">implementations</span></span> | <span data-ttu-id="bc4b9-224">Pass args</span><span class="sxs-lookup"><span data-stu-id="bc4b9-224">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="bc4b9-225">Esempio:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-225">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="bc4b9-226">Sì</span><span class="sxs-lookup"><span data-stu-id="bc4b9-226">Yes</span></span>                             | <span data-ttu-id="bc4b9-227">Sì</span><span class="sxs-lookup"><span data-stu-id="bc4b9-227">Yes</span></span>                         | <span data-ttu-id="bc4b9-228">No</span><span class="sxs-lookup"><span data-stu-id="bc4b9-228">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="bc4b9-229">Esempi:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-229">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="bc4b9-230">Sì</span><span class="sxs-lookup"><span data-stu-id="bc4b9-230">Yes</span></span>                             | <span data-ttu-id="bc4b9-231">Sì</span><span class="sxs-lookup"><span data-stu-id="bc4b9-231">Yes</span></span>                         | <span data-ttu-id="bc4b9-232">Sì</span><span class="sxs-lookup"><span data-stu-id="bc4b9-232">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="bc4b9-233">Esempio:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-233">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="bc4b9-234">Sì</span><span class="sxs-lookup"><span data-stu-id="bc4b9-234">Yes</span></span>                             | <span data-ttu-id="bc4b9-235">No</span><span class="sxs-lookup"><span data-stu-id="bc4b9-235">No</span></span>                          | <span data-ttu-id="bc4b9-236">No</span><span class="sxs-lookup"><span data-stu-id="bc4b9-236">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="bc4b9-237">Esempi:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-237">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="bc4b9-238">No</span><span class="sxs-lookup"><span data-stu-id="bc4b9-238">No</span></span>                              | <span data-ttu-id="bc4b9-239">Sì</span><span class="sxs-lookup"><span data-stu-id="bc4b9-239">Yes</span></span>                         | <span data-ttu-id="bc4b9-240">Sì</span><span class="sxs-lookup"><span data-stu-id="bc4b9-240">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="bc4b9-241">Esempi:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-241">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="bc4b9-242">No</span><span class="sxs-lookup"><span data-stu-id="bc4b9-242">No</span></span>                              | <span data-ttu-id="bc4b9-243">No</span><span class="sxs-lookup"><span data-stu-id="bc4b9-243">No</span></span>                          | <span data-ttu-id="bc4b9-244">Sì</span><span class="sxs-lookup"><span data-stu-id="bc4b9-244">Yes</span></span>       |

<span data-ttu-id="bc4b9-245">Per ulteriori informazioni sull'eliminazione dei tipi, vedere la sezione [Eliminazione dei servizi](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-245">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="bc4b9-246">Quando si [simulano i tipi per il test](xref:test/integration-tests#inject-mock-services), è comune usare più implementazioni.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-246">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="bc4b9-247">La registrazione di un servizio con un solo tipo di implementazione equivale alla registrazione del servizio con lo stesso tipo di implementazione e di servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-247">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="bc4b9-248">Questo è il motivo per cui non è possibile registrare più implementazioni di un servizio usando i metodi che non accettano un tipo di servizio esplicito.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-248">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="bc4b9-249">Questi metodi possono registrare più _instances \* di un servizio, ma avranno tutti lo stesso tipo di *implementazione* .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-249">These methods can register multiple _instances\* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="bc4b9-250">Uno qualsiasi dei metodi di registrazione del servizio sopra indicati può essere utilizzato per registrare più istanze del servizio dello stesso tipo di servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-250">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="bc4b9-251">Nell'esempio seguente `AddSingleton` viene chiamato due volte con `IMyDependency` come tipo di servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-251">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="bc4b9-252">La seconda chiamata a `AddSingleton` sostituisce quella precedente quando viene risolta come `IMyDependency` e aggiunge a quella precedente quando più servizi vengono risolti tramite `IEnumerable<IMyDependency>` .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-252">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="bc4b9-253">I servizi vengono visualizzati nell'ordine in cui sono stati registrati durante la risoluzione tramite `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-253">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="bc4b9-254">Il Framework fornisce anche `TryAdd{LIFETIME}` metodi di estensione, che registrano il servizio solo se non è già stata registrata un'implementazione.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-254">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="bc4b9-255">Nell'esempio seguente, la chiamata a `AddSingleton` Registra `MyDependency` come implementazione per `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-255">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="bc4b9-256">La chiamata a `TryAddSingleton` non ha alcun effetto perché `IMyDependency` dispone già di un'implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-256">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="bc4b9-257">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-257">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="bc4b9-258">I metodi [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) registrano il servizio solo se non esiste già un'implementazione *dello stesso tipo* .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-258">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type* .</span></span> <span data-ttu-id="bc4b9-259">Più servizi vengono risolti tramite `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-259">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="bc4b9-260">Quando si registrano i servizi, lo sviluppatore deve aggiungere un'istanza se uno degli stessi tipi non è già stato aggiunto.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-260">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="bc4b9-261">In genere, gli autori `TryAddEnumerable` di librerie utilizzano per evitare la registrazione di più copie di un'implementazione nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-261">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="bc4b9-262">Nell'esempio seguente, la prima chiamata a viene `TryAddEnumerable` registrata `MyDependency` come implementazione per `IMyDependency1` .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-262">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="bc4b9-263">La seconda chiamata esegue la registrazione `MyDependency` per `IMyDependency2` .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-263">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="bc4b9-264">La terza chiamata non ha alcun effetto perché `IMyDependency1` dispone già di un'implementazione registrata di `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="bc4b9-264">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="bc4b9-265">La registrazione del servizio è in genere indipendente dall'ordine, tranne quando si registrano più implementazioni dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-265">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="bc4b9-266">`IServiceCollection` è una raccolta di <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> oggetti.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-266">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="bc4b9-267">Nell'esempio seguente viene illustrato come registrare un servizio creando e aggiungendo `ServiceDescriptor` :</span><span class="sxs-lookup"><span data-stu-id="bc4b9-267">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="bc4b9-268">I `Add{LIFETIME}` metodi predefiniti utilizzano lo stesso approccio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-268">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="bc4b9-269">Vedere ad esempio il [codice sorgente di AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-269">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="bc4b9-270">Comportamento dell'inserimento del costruttore</span><span class="sxs-lookup"><span data-stu-id="bc4b9-270">Constructor injection behavior</span></span>

<span data-ttu-id="bc4b9-271">I servizi possono essere risolti tramite:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-271">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="bc4b9-272"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-272"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="bc4b9-273">Crea oggetti che non sono registrati nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-273">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="bc4b9-274">Usato con le funzionalità del Framework, ad esempio gli [Helper Tag](xref:mvc/views/tag-helpers/intro), i controller MVC e gli elementi di [associazione di modelli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-274">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="bc4b9-275">I costruttori possono accettare argomenti non forniti tramite l'inserimento di dipendenze, ma gli argomenti devono assegnare valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-275">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="bc4b9-276">Quando i servizi vengono risolti da `IServiceProvider` o `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede un costruttore *pubblico* .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-276">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="bc4b9-277">Quando i servizi vengono risolti da `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede l'esistenza di un solo costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-277">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="bc4b9-278">Sebbene siano supportati gli overload dei costruttori, può essere presente un solo overload i cui argomenti possono essere tutti specificati tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-278">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="bc4b9-279">Contesti di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="bc4b9-279">Entity Framework contexts</span></span>

<span data-ttu-id="bc4b9-280">Per impostazione predefinita, i contesti di Entity Framework vengono aggiunti al contenitore del servizio usando la [durata con ambito](#service-lifetimes) poiché le operazioni del database dell'app Web sono in genere limitate all'ambito della richiesta client.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-280">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="bc4b9-281">Per usare una durata diversa, specificare la durata usando un <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> Overload.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-281">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="bc4b9-282">I servizi di una determinata durata non devono utilizzare un contesto di database con una durata minore della durata del servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-282">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="bc4b9-283">Opzioni di durata e di registrazione</span><span class="sxs-lookup"><span data-stu-id="bc4b9-283">Lifetime and registration options</span></span>

<span data-ttu-id="bc4b9-284">Per dimostrare la differenza tra le durate dei servizi e le relative opzioni di registrazione, prendere in considerazione le interfacce seguenti che rappresentano un'attività come operazione con un identificatore `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-284">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="bc4b9-285">A seconda del modo in cui viene configurata la durata del servizio di un'operazione per le interfacce seguenti, il contenitore fornisce le stesse istanze o diverse del servizio quando richiesto da una classe:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-285">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="bc4b9-286">La `Operation` classe seguente implementa tutte le interfacce precedenti.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-286">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="bc4b9-287">Il `Operation` costruttore genera un GUID e archivia gli ultimi 4 caratteri nella `OperationId` proprietà:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-287">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="bc4b9-288">Il `Startup.ConfigureServices` metodo crea più registrazioni della `Operation` classe in base alle durate denominate:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-288">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="bc4b9-289">L'app di esempio illustra le durate degli oggetti all'interno e tra le richieste.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-289">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="bc4b9-290">Il `IndexModel` e il middleware richiedono ogni tipo di `IOperation` tipo e registrano `OperationId` per ogni tipo:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-290">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="bc4b9-291">Analogamente a `IndexModel` , il middleware risolve gli stessi servizi:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-291">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="bc4b9-292">I servizi con ambito devono essere risolti nel `InvokeAsync` Metodo:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-292">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="bc4b9-293">L'output del logger Mostra:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-293">The logger output shows:</span></span>

* <span data-ttu-id="bc4b9-294">Gli oggetti *temporanei* sono sempre diversi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-294">*Transient* objects are always different.</span></span> <span data-ttu-id="bc4b9-295">Il `OperationId` valore temporaneo è diverso in `IndexModel` e nel middleware.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-295">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="bc4b9-296">Gli oggetti con *ambito* sono gli stessi per ogni richiesta, ma sono diversi in ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-296">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="bc4b9-297">Gli oggetti *singleton* sono gli stessi per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-297">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="bc4b9-298">Per ridurre l'output di registrazione, impostare "Logging: LogLevel: Microsoft: Error" nella *appsettings.Development.jssu* file:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-298">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="bc4b9-299">Chiamare i servizi da main</span><span class="sxs-lookup"><span data-stu-id="bc4b9-299">Call services from main</span></span>

<span data-ttu-id="bc4b9-300">Creare un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>IServiceScope con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) per risolvere un servizio con ambito nell'ambito di applicazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-300">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="bc4b9-301">Questo approccio è utile per l'accesso a un servizio con ambito all'avvio e per l'esecuzione di attività di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-301">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="bc4b9-302">Nell'esempio seguente viene illustrato come accedere al servizio con ambito `IMyDependency` e come chiamare il relativo `WriteMessage` metodo in `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="bc4b9-302">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="bc4b9-303">Convalida dell'ambito</span><span class="sxs-lookup"><span data-stu-id="bc4b9-303">Scope validation</span></span>

<span data-ttu-id="bc4b9-304">Quando l'app viene eseguita nell' [ambiente di sviluppo](xref:fundamentals/environments) e chiama [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) per compilare l'host, il provider di servizi predefinito esegue i controlli per verificare che:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-304">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="bc4b9-305">I servizi con ambito non vengono risolti dal provider di servizi radice.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-305">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="bc4b9-306">I servizi con ambito non vengono inseriti in singleton.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-306">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="bc4b9-307">Il provider di servizi radice venga creato con la chiamata di <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A>.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-307">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="bc4b9-308">La durata del provider di servizi radice corrisponde alla durata dell'app quando il provider inizia con l'app e viene eliminato quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-308">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="bc4b9-309">I servizi con ambito vengono eliminati dal contenitore che li ha creati.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-309">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="bc4b9-310">Se nel contenitore radice viene creato un servizio con ambito, la durata del servizio viene effettivamente promossa a Singleton perché viene eliminata dal contenitore radice solo quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-310">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="bc4b9-311">La convalida degli ambiti servizio rileva queste situazioni nel contesto della chiamata di `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-311">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="bc4b9-312">Per ulteriori informazioni, vedere [Convalida dell'ambito](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-312">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="bc4b9-313">Servizi di richiesta</span><span class="sxs-lookup"><span data-stu-id="bc4b9-313">Request Services</span></span>

<span data-ttu-id="bc4b9-314">I servizi disponibili all'interno di una richiesta di ASP.NET Core vengono esposti tramite la raccolta [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-314">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="bc4b9-315">Quando i servizi vengono richiesti dall'interno di una richiesta, i servizi e le relative dipendenze vengono risolti dalla `RequestServices` raccolta.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-315">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="bc4b9-316">Il Framework crea un ambito per ogni richiesta ed `RequestServices` espone il provider di servizi con ambito.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-316">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="bc4b9-317">Tutti i servizi con ambito sono validi fino a quando la richiesta è attiva.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-317">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="bc4b9-318">Preferisce richiedere le dipendenze come parametri del costruttore per la risoluzione dei servizi dalla `RequestServices` raccolta.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-318">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="bc4b9-319">In questo modo, le classi risultano più facili da testare.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-319">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="bc4b9-320">Progettare i servizi per l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="bc4b9-320">Design services for dependency injection</span></span>

<span data-ttu-id="bc4b9-321">Durante la progettazione di servizi per l'inserimento di dipendenze:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-321">When designing services for dependency injection:</span></span>

* <span data-ttu-id="bc4b9-322">Evitare membri e classi statiche con stato.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-322">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="bc4b9-323">Evitare di creare lo stato globale progettando le app in modo da usare i servizi singleton.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-323">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="bc4b9-324">Evitare la creazione diretta di istanze delle classi dipendenti all'interno di servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-324">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="bc4b9-325">La creazione diretta di istanze associa il codice a una determinata implementazione.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-325">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="bc4b9-326">Semplifica la testing dei servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-326">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="bc4b9-327">Se una classe dispone di molte dipendenze inserite, potrebbe essere un segno che la classe ha troppe responsabilità e viola il [principio di singola responsabilità (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-327">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="bc4b9-328">Tentare di effettuare il refactoring della classe spostando alcune delle responsabilità in nuove classi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-328">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="bc4b9-329">Tenere presente che :::no-loc(Razor)::: le classi del modello della pagina delle pagine e le classi controller MVC devono concentrarsi sulle problematiche dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-329">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="bc4b9-330">Eliminazione dei servizi</span><span class="sxs-lookup"><span data-stu-id="bc4b9-330">Disposal of services</span></span>

<span data-ttu-id="bc4b9-331">Il contenitore chiama <xref:System.IDisposable.Dispose%2A> per i tipi <xref:System.IDisposable> creati.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-331">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="bc4b9-332">I servizi risolti dal contenitore non devono mai essere eliminati dallo sviluppatore.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-332">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="bc4b9-333">Se un tipo o una factory viene registrata come singleton, il contenitore elimina il singleton automaticamente.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-333">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="bc4b9-334">Nell'esempio seguente i servizi vengono creati dal contenitore del servizio ed eliminati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-334">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="bc4b9-335">La console di debug Visualizza il seguente output dopo ogni aggiornamento della pagina di indice:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-335">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="bc4b9-336">Servizi non creati dal contenitore del servizio</span><span class="sxs-lookup"><span data-stu-id="bc4b9-336">Services not created by the service container</span></span>

<span data-ttu-id="bc4b9-337">Osservare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-337">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="bc4b9-338">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-338">In the preceding code:</span></span>

* <span data-ttu-id="bc4b9-339">Le istanze del servizio non vengono create dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-339">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="bc4b9-340">Il Framework non elimina automaticamente i servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-340">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="bc4b9-341">Lo sviluppatore è responsabile dell'eliminazione dei servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-341">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="bc4b9-342">Linee guida IDisposable per istanze temporanee e condivise</span><span class="sxs-lookup"><span data-stu-id="bc4b9-342">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="bc4b9-343">Durata temporanea e limitata</span><span class="sxs-lookup"><span data-stu-id="bc4b9-343">Transient, limited lifetime</span></span>

<span data-ttu-id="bc4b9-344">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="bc4b9-344">**Scenario**</span></span>

<span data-ttu-id="bc4b9-345">L'app richiede un' <xref:System.IDisposable> istanza con una durata temporanea per uno degli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-345">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="bc4b9-346">L'istanza viene risolta nell'ambito radice (contenitore radice).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-346">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="bc4b9-347">L'istanza deve essere eliminata prima della fine dell'ambito.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-347">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="bc4b9-348">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="bc4b9-348">**Solution**</span></span>

<span data-ttu-id="bc4b9-349">Usare il modello factory per creare un'istanza all'esterno dell'ambito padre.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-349">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="bc4b9-350">In questa situazione, in genere l'app dispone di un `Create` metodo che chiama direttamente il costruttore del tipo finale.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-350">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="bc4b9-351">Se il tipo finale ha altre dipendenze, la factory può:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-351">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="bc4b9-352">Ricevere un oggetto <xref:System.IServiceProvider> nel relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-352">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="bc4b9-353">Usare <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> per creare un'istanza dell'istanza all'esterno del contenitore, usando il contenitore per le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-353">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="bc4b9-354">Istanza condivisa, durata limitata</span><span class="sxs-lookup"><span data-stu-id="bc4b9-354">Shared instance, limited lifetime</span></span>

<span data-ttu-id="bc4b9-355">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="bc4b9-355">**Scenario**</span></span>

<span data-ttu-id="bc4b9-356">L'app richiede un' <xref:System.IDisposable> istanza condivisa tra più servizi, ma l' <xref:System.IDisposable> istanza deve avere una durata limitata.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-356">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="bc4b9-357">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="bc4b9-357">**Solution**</span></span>

<span data-ttu-id="bc4b9-358">Registrare l'istanza con una durata con ambito.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-358">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="bc4b9-359">Utilizzare <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> per creare un nuovo oggetto <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-359">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="bc4b9-360">Usare l'ambito <xref:System.IServiceProvider> per ottenere i servizi richiesti.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-360">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="bc4b9-361">Eliminare l'ambito quando non è più necessario.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-361">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="bc4b9-362">Linee guida generali sull'IDisposable</span><span class="sxs-lookup"><span data-stu-id="bc4b9-362">General IDisposable guidelines</span></span>

* <span data-ttu-id="bc4b9-363">Non registrare <xref:System.IDisposable> le istanze con una durata temporanea.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-363">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="bc4b9-364">Usare invece il modello Factory.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-364">Use the factory pattern instead.</span></span>
* <span data-ttu-id="bc4b9-365">Non risolvere <xref:System.IDisposable> le istanze con una durata temporanea o con ambito nell'ambito radice.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-365">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="bc4b9-366">L'unica eccezione è rappresentata dal caso in cui l'app crea/ricrea ed Elimina <xref:System.IServiceProvider> , ma questo non è un modello ideale.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-366">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="bc4b9-367">Per <xref:System.IDisposable> la ricezione di una dipendenza tramite di non è necessario che il ricevitore implementi <xref:System.IDisposable> se stesso.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-367">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="bc4b9-368">Il ricevitore della <xref:System.IDisposable> dipendenza non deve chiamare <xref:System.IDisposable.Dispose%2A> su tale dipendenza.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-368">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="bc4b9-369">Utilizzare gli ambiti per controllare la durata dei servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-369">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="bc4b9-370">Gli ambiti non sono gerarchici e non esiste alcuna connessione speciale tra gli ambiti.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-370">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="bc4b9-371">Sostituzione del contenitore di servizi predefinito</span><span class="sxs-lookup"><span data-stu-id="bc4b9-371">Default service container replacement</span></span>

<span data-ttu-id="bc4b9-372">Il contenitore dei servizi incorporato è progettato per soddisfare le esigenze del Framework e della maggior parte delle app consumer.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-372">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="bc4b9-373">È consigliabile usare il contenitore predefinito a meno che non sia necessaria una funzionalità specifica che non supporta, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-373">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="bc4b9-374">Inserimento di proprietà</span><span class="sxs-lookup"><span data-stu-id="bc4b9-374">Property injection</span></span>
* <span data-ttu-id="bc4b9-375">Inserimento in base al nome</span><span class="sxs-lookup"><span data-stu-id="bc4b9-375">Injection based on name</span></span>
* <span data-ttu-id="bc4b9-376">Contenitori figlio</span><span class="sxs-lookup"><span data-stu-id="bc4b9-376">Child containers</span></span>
* <span data-ttu-id="bc4b9-377">Gestione della durata personalizzata</span><span class="sxs-lookup"><span data-stu-id="bc4b9-377">Custom lifetime management</span></span>
* <span data-ttu-id="bc4b9-378">Supporto di `Func<T>` per l'inizializzazione differita</span><span class="sxs-lookup"><span data-stu-id="bc4b9-378">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="bc4b9-379">Registrazione basata sulle convenzioni</span><span class="sxs-lookup"><span data-stu-id="bc4b9-379">Convention-based registration</span></span>

<span data-ttu-id="bc4b9-380">Con ASP.NET Core app è possibile usare i contenitori di terze parti seguenti:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-380">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="bc4b9-381">Autofac</span><span class="sxs-lookup"><span data-stu-id="bc4b9-381">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="bc4b9-382">DryIoc</span><span class="sxs-lookup"><span data-stu-id="bc4b9-382">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="bc4b9-383">Laura</span><span class="sxs-lookup"><span data-stu-id="bc4b9-383">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="bc4b9-384">LightInject</span><span class="sxs-lookup"><span data-stu-id="bc4b9-384">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="bc4b9-385">Lamar</span><span class="sxs-lookup"><span data-stu-id="bc4b9-385">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="bc4b9-386">Stashbox</span><span class="sxs-lookup"><span data-stu-id="bc4b9-386">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="bc4b9-387">Unity</span><span class="sxs-lookup"><span data-stu-id="bc4b9-387">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="bc4b9-388">Thread safety</span><span class="sxs-lookup"><span data-stu-id="bc4b9-388">Thread safety</span></span>

<span data-ttu-id="bc4b9-389">Creare servizi singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-389">Create thread-safe singleton services.</span></span> <span data-ttu-id="bc4b9-390">Se un servizio singleton presenta una dipendenza da un servizio temporaneo, il servizio temporaneo potrebbe richiedere anche thread safety a seconda del modo in cui viene utilizzato dal singleton.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-390">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="bc4b9-391">Il metodo factory di un singolo servizio, ad esempio il secondo argomento di [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), non deve essere thread-safe.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-391">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="bc4b9-392">Analogamente a un `static` Costruttore Type (), è garantita la chiamata solo una volta da un singolo thread.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-392">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="bc4b9-393">Consigli</span><span class="sxs-lookup"><span data-stu-id="bc4b9-393">Recommendations</span></span>

* <span data-ttu-id="bc4b9-394">`async/await` la `Task` risoluzione del servizio basata su non è supportata.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-394">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="bc4b9-395">Poiché C# non supporta costruttori asincroni, utilizzare i metodi asincroni dopo la risoluzione sincrona del servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-395">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="bc4b9-396">Evitare di archiviare i dati e la configurazione direttamente nel contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-396">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="bc4b9-397">Ad esempio, il carrello acquisti di un utente non dovrebbe in genere essere aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-397">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="bc4b9-398">La configurazione deve usare il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-398">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="bc4b9-399">Allo stesso modo, evitare gli oggetti "titolare dei dati" che esistono solo per consentire l'accesso a un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-399">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="bc4b9-400">È preferibile richiedere l'elemento effettivo tramite inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-400">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="bc4b9-401">Evitare l'accesso statico ai servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-401">Avoid static access to services.</span></span> <span data-ttu-id="bc4b9-402">Ad esempio, evitare di acquisire [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) come proprietà o campo statico da usare altrove.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-402">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="bc4b9-403">Mantieni le fabbriche veloci e sincrone.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-403">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="bc4b9-404">Evitare di usare il *modello di localizzatore del servizio* .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-404">Avoid using the *service locator pattern* .</span></span> <span data-ttu-id="bc4b9-405">Ad esempio, non richiamare <xref:System.IServiceProvider.GetService%2A> per ottenere un'istanza del servizio quando è invece possibile usare l'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-405">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="bc4b9-406">**Non corretto:**</span><span class="sxs-lookup"><span data-stu-id="bc4b9-406">**Incorrect:**</span></span>

    ![Codice errato](dependency-injection/_static/bad.png)

  <span data-ttu-id="bc4b9-408">**Corretto** :</span><span class="sxs-lookup"><span data-stu-id="bc4b9-408">**Correct** :</span></span>

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
* <span data-ttu-id="bc4b9-409">Un'altra variazione del localizzatore del servizio da evitare è inserire una factory che risolve le dipendenze in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-409">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="bc4b9-410">Queste procedure combinano le strategie di [inversione del controllo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-410">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="bc4b9-411">Evitare l'accesso statico a `HttpContext` (ad esempio [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-411">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="bc4b9-412">Evitare chiamate a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-412">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="bc4b9-413">`BuildServiceProvider`La chiamata in genere si verifica quando lo sviluppatore desidera risolvere un servizio in `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-413">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="bc4b9-414">Si consideri, ad esempio, il caso in cui l'oggetto `LoginPath` viene caricato dalla configurazione.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-414">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="bc4b9-415">Evitare l'approccio seguente:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-415">Avoid the following approach:</span></span>

  ![codice errato durante la chiamata di BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="bc4b9-417">Nell'immagine precedente, selezionando la linea ondulata verde in `services.BuildServiceProvider` viene visualizzato l'avviso ASP0000 seguente:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-417">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="bc4b9-418">ASP0000 la chiamata a' BuildServiceProvider ' dal codice dell'applicazione comporta una copia aggiuntiva dei servizi singleton creati.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-418">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="bc4b9-419">Prendere in considerazione alternative come la dipendenza che inserisce i servizi come parametri per "Configure".</span><span class="sxs-lookup"><span data-stu-id="bc4b9-419">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="bc4b9-420">`BuildServiceProvider`La chiamata di crea un secondo contenitore, che può creare singleton incompleti e causare riferimenti a oggetti grafici tra più contenitori.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-420">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="bc4b9-421">Un modo corretto per ottenere `LoginPath` consiste nell'usare il supporto incorporato del modello di opzioni per l'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-421">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="bc4b9-422">I servizi temporanei Disposable vengono acquisiti dal contenitore per l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-422">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="bc4b9-423">Questa operazione può comportare una perdita di memoria se risolta dal contenitore di primo livello.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-423">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="bc4b9-424">Abilitare la convalida dell'ambito per assicurarsi che l'app non disponga di singleton che acquisiscono i servizi con ambito.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-424">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="bc4b9-425">Per ulteriori informazioni, vedere [Convalida dell'ambito](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-425">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="bc4b9-426">È tuttavia possibile che in alcuni casi queste raccomandazioni debbano essere ignorate.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-426">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="bc4b9-427">Le eccezioni sono rari, soprattutto casi speciali all'interno del Framework stesso.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-427">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="bc4b9-428">L'inserimento di dipendenze è un' *alternativa* ai modelli di accesso agli oggetti statici/globali.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-428">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="bc4b9-429">Se l'inserimento di dipendenze viene usato con l'accesso agli oggetti statico i vantaggi non saranno evidenti.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-429">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="bc4b9-430">Modelli consigliati per il multi-tenant in DI</span><span class="sxs-lookup"><span data-stu-id="bc4b9-430">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="bc4b9-431">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) è un Framework di applicazione per la creazione di applicazioni modulari multi-tenant in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-431">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="bc4b9-432">Per ulteriori informazioni, vedere la [documentazione di Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-432">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="bc4b9-433">Vedere gli [esempi di Orchard Core](https://github.com/OrchardCMS/OrchardCore.Samples) per esempi di come creare app modulari e multi-tenant usando solo il Framework Orchard core senza le funzionalità specifiche di CMS.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-433">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="bc4b9-434">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="bc4b9-434">Framework-provided services</span></span>

<span data-ttu-id="bc4b9-435">Il `Startup.ConfigureServices` metodo registra i servizi usati dall'app, incluse le funzionalità della piattaforma, ad esempio Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-435">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="bc4b9-436">Inizialmente, l'oggetto `IServiceCollection` fornito a `ConfigureServices` dispone di servizi definiti dal Framework a seconda della [configurazione dell'host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-436">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="bc4b9-437">Per le app basate sui modelli di ASP.NET Core, il Framework registra più di 250 servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-437">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="bc4b9-438">La tabella seguente elenca un piccolo esempio di questi servizi registrati dal Framework:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-438">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="bc4b9-439">Tipo di servizio</span><span class="sxs-lookup"><span data-stu-id="bc4b9-439">Service Type</span></span>                                                                                    | <span data-ttu-id="bc4b9-440">Durata</span><span class="sxs-lookup"><span data-stu-id="bc4b9-440">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="bc4b9-441">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="bc4b9-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="bc4b9-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-442">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="bc4b9-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-443">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="bc4b9-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-444">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="bc4b9-445">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="bc4b9-445">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="bc4b9-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-446">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="bc4b9-447">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="bc4b9-447">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="bc4b9-448">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-448">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="bc4b9-449">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-449">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="bc4b9-450">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-450">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="bc4b9-451">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="bc4b9-451">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="bc4b9-452">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-452">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="bc4b9-453">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-453">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="bc4b9-454">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-454">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="bc4b9-455">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="bc4b9-455">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="bc4b9-456">Modelli DI conferenza NDC per lo sviluppo DI app DI</span><span class="sxs-lookup"><span data-stu-id="bc4b9-456">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="bc4b9-457">Quattro modi per eliminare IDisposable in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bc4b9-457">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="bc4b9-458">Scrittura di codice pulito in ASP.NET Core con inserimento delle dipendenze (MSDN)</span><span class="sxs-lookup"><span data-stu-id="bc4b9-458">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="bc4b9-459">Explicit Dependencies Principle (Principio delle dipendenze esplicite)</span><span class="sxs-lookup"><span data-stu-id="bc4b9-459">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="bc4b9-460">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler) (Contenitori di inversione del controllo e modello di inserimento delle dipendenze)</span><span class="sxs-lookup"><span data-stu-id="bc4b9-460">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="bc4b9-461">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Come registrare un servizio con più interfacce in ASP.NET Core DI)</span><span class="sxs-lookup"><span data-stu-id="bc4b9-461">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bc4b9-462">Di [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="bc4b9-462">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="bc4b9-463">ASP.NET Core supporta lo schema progettuale di software per l'inserimento di dipendenze, una tecnica per ottenere l'[IoC (Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra le classi e le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-463">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="bc4b9-464">Per altre informazioni specifiche per l'inserimento delle dipendenze all'interno di controller MVC, vedere <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-464">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="bc4b9-465">[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bc4b9-465">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="bc4b9-466">Panoramica dell'inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="bc4b9-466">Overview of dependency injection</span></span>

<span data-ttu-id="bc4b9-467">Una *dipendenza* è qualsiasi oggetto richiesto da un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-467">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="bc4b9-468">Esaminare la classe `MyDependency` seguente con un metodo `WriteMessage` da cui dipendono altre classi in un'app:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-468">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="bc4b9-469">È possibile creare un'istanza della classe `MyDependency` per rendere il metodo `WriteMessage`disponibile per una classe.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-469">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="bc4b9-470">La classe `MyDependency` è una dipendenza della classe `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-470">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="bc4b9-471">La classe crea e dipende direttamente dall'istanza `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-471">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="bc4b9-472">Le dipendenze del codice (come nell'esempio precedente) sono problematiche e devono essere evitate per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-472">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="bc4b9-473">Per sostituire `MyDependency` con un'implementazione diversa, la classe deve essere modificata.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-473">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="bc4b9-474">Se `MyDependency` ha dipendenze, devono essere configurate dalla classe.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-474">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="bc4b9-475">In un progetto di grandi dimensioni con più classi che dipendono da `MyDependency`, il codice di configurazione diventa sparso in tutta l'app.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-475">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="bc4b9-476">È difficile eseguire unit test di questa implementazione.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-476">This implementation is difficult to unit test.</span></span> <span data-ttu-id="bc4b9-477">L'app dovrebbe usare una classe `MyDependency` fittizia o stub, ma ciò non è possibile con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-477">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="bc4b9-478">L'inserimento delle dipendenze consente di risolvere questi problemi tramite:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-478">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="bc4b9-479">L'uso di un'interfaccia o di una classe di base per astrarre l'implementazione delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-479">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="bc4b9-480">La registrazione della dipendenza in un contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-480">Registration of the dependency in a service container.</span></span> <span data-ttu-id="bc4b9-481">ASP.NET Core offre il contenitore di servizi predefinito <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-481">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="bc4b9-482">I servizi vengono registrati nel metodo `Startup.ConfigureServices` dell'app.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-482">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="bc4b9-483">L' *inserimento* del servizio nel costruttore della classe in cui viene usato.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-483">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="bc4b9-484">Il framework si assume la responsabilità della creazione di un'istanza della dipendenza e della sua eliminazione quando non è più necessaria.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-484">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="bc4b9-485">Nell'[app di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l'interfaccia `IMyDependency` definisce un metodo che il servizio fornisce all'app:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-485">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="bc4b9-486">Questa interfaccia viene implementata da un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-486">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="bc4b9-487">`MyDependency` richiede <xref:Microsoft.Extensions.Logging.ILogger`1> nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-487">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="bc4b9-488">Non è insolito usare l'inserimento delle dipendenze in modo concatenato.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-488">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="bc4b9-489">Ogni dipendenza richiesta richiede a sua volta le proprie dipendenze.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-489">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="bc4b9-490">Il contenitore risolve le dipendenze nel grafico e restituisce il servizio completamente risolto.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-490">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="bc4b9-491">Il set di dipendenze che devono essere risolte viene generalmente chiamato *albero delle dipendenze* o *grafico dipendenze* o *grafico degli oggetti* .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-491">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph* .</span></span>

<span data-ttu-id="bc4b9-492">`IMyDependency` e `ILogger<TCategoryName>` devono essere registrati nel contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-492">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="bc4b9-493">`IMyDependency` viene registrato in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-493">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bc4b9-494">`ILogger<TCategoryName>` viene registrato dall'infrastruttura di registrazione delle astrazioni, pertanto si tratta di un [servizio fornito dal Framework](#framework-provided-services) registrato per impostazione predefinita dal Framework.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-494">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="bc4b9-495">Il contenitore risolve `ILogger<TCategoryName>` avvalendosi di [tipi aperti (generici)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando l'esigenza di registrare ogni singolo [tipo costruito (generico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="bc4b9-495">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="bc4b9-496">Nell'app di esempio, il servizio `IMyDependency` viene registrato con il tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-496">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="bc4b9-497">La registrazione definisce come ambito della durata di servizio la durata di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-497">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="bc4b9-498">Le [durate dei servizi](#service-lifetimes) sono descritte più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-498">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="bc4b9-499">Ogni metodo di estensione `services.Add{SERVICE_NAME}` aggiunge (e potenzialmente configura) i servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-499">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="bc4b9-500">Ad esempio, `services.AddMvc()` aggiunge le pagine dei servizi :::no-loc(Razor)::: e MVC require.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-500">For example, `services.AddMvc()` adds the services :::no-loc(Razor)::: Pages and MVC require.</span></span> <span data-ttu-id="bc4b9-501">È consigliabile che le app seguano questa convenzione.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-501">We recommended that apps follow this convention.</span></span> <span data-ttu-id="bc4b9-502">Inserire i metodi di estensione nello spazio dei nomi [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) per incapsulare i gruppi di registrazioni di servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-502">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="bc4b9-503">Se il costruttore del servizio richiede un [tipo incorporato](/dotnet/csharp/language-reference/keywords/built-in-types-table), ad esempio `string`, è possibile inserirlo usando la [configurazione](xref:fundamentals/configuration/index) o il [modello di opzioni](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="bc4b9-503">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="bc4b9-504">È richiesta un'istanza del servizio tramite il costruttore di una classe in cui il servizio viene usato e assegnato a un campo privato.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-504">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="bc4b9-505">Il campo viene usato per accedere al servizio in base alle esigenze per l'intera classe.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-505">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="bc4b9-506">Nell'app di esempio, viene richiesta l'istanza `IMyDependency` usata per chiamare il metodo `WriteMessage` del servizio:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-506">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="bc4b9-507">Servizi inseriti all'avvio</span><span class="sxs-lookup"><span data-stu-id="bc4b9-507">Services injected into Startup</span></span>

<span data-ttu-id="bc4b9-508">`Startup`Quando si utilizza l'host generico () è possibile inserire nel costruttore solo i tipi di servizio seguenti <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="bc4b9-508">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="bc4b9-509">I servizi possono essere inseriti in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="bc4b9-509">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="bc4b9-510">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-510">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="bc4b9-511">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="bc4b9-511">Framework-provided services</span></span>

<span data-ttu-id="bc4b9-512">Il `Startup.ConfigureServices` metodo è responsabile della definizione dei servizi utilizzati dall'app, incluse le funzionalità della piattaforma, ad esempio Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-512">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="bc4b9-513">Inizialmente, l'oggetto `IServiceCollection` fornito a `ConfigureServices` dispone di servizi definiti dal Framework a seconda della [configurazione dell'host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-513">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="bc4b9-514">Non è insolito che un'app basata su un modello di ASP.NET Core disponga di centinaia di servizi registrati dal Framework.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-514">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="bc4b9-515">Nella tabella seguente è elencato un piccolo esempio di servizi registrati dal Framework.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-515">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="bc4b9-516">Tipo di servizio</span><span class="sxs-lookup"><span data-stu-id="bc4b9-516">Service Type</span></span> | <span data-ttu-id="bc4b9-517">Durata</span><span class="sxs-lookup"><span data-stu-id="bc4b9-517">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="bc4b9-518">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="bc4b9-518">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="bc4b9-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-519">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="bc4b9-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-520">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="bc4b9-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-521">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="bc4b9-522">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="bc4b9-522">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="bc4b9-523">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-523">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="bc4b9-524">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="bc4b9-524">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="bc4b9-525">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-525">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="bc4b9-526">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-526">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="bc4b9-527">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-527">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="bc4b9-528">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="bc4b9-528">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="bc4b9-529">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-529">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="bc4b9-530">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-530">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="bc4b9-531">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-531">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="bc4b9-532">Registrare servizi aggiuntivi con i metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="bc4b9-532">Register additional services with extension methods</span></span>

<span data-ttu-id="bc4b9-533">Quando è disponibile un metodo di estensione della raccolta di servizi per la registrazione di un servizio (e dei servizi dipendenti, se necessario), la convenzione consiste nell'usare un singolo metodo di estensione `Add{SERVICE_NAME}` per registrare tutti i servizi richiesti da tale servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-533">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="bc4b9-534">Il codice seguente è un esempio di come aggiungere servizi aggiuntivi al contenitore usando i metodi di estensione [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*> :</span><span class="sxs-lookup"><span data-stu-id="bc4b9-534">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="bc4b9-535">Per altre informazioni, vedere la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> nella documentazione delle API.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-535">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="bc4b9-536">Durate del servizio</span><span class="sxs-lookup"><span data-stu-id="bc4b9-536">Service lifetimes</span></span>

<span data-ttu-id="bc4b9-537">Scegliere una durata appropriata per ogni servizio registrato.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-537">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="bc4b9-538">I servizi ASP.NET Core possono essere configurati con le impostazioni di durata seguenti:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-538">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="bc4b9-539">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="bc4b9-539">Transient</span></span>

<span data-ttu-id="bc4b9-540">I servizi con durata temporanea (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) vengono creati ogni volta che vengono richiesti dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-540">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="bc4b9-541">Questa impostazione di durata è consigliata per i servizi semplici senza stati.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-541">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="bc4b9-542">Nelle app che elaborano le richieste, i servizi temporanei vengono eliminati alla fine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-542">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="bc4b9-543">Con ambito</span><span class="sxs-lookup"><span data-stu-id="bc4b9-543">Scoped</span></span>

<span data-ttu-id="bc4b9-544">I servizi con durata con ambito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) vengono creati una volta per ogni richiesta client (connessione).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-544">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="bc4b9-545">Nelle app che elaborano le richieste, i servizi con ambito vengono eliminati alla fine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-545">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="bc4b9-546">Quando si usa un servizio con ambito in un middleware, inserire il servizio nel metodo `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-546">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="bc4b9-547">Non inserire tramite il [Costruttore Injection](xref:mvc/controllers/dependency-injection#constructor-injection) perché impone al servizio di comportarsi come un singleton.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-547">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="bc4b9-548">Per altre informazioni, vedere <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-548">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="bc4b9-549">Singleton</span><span class="sxs-lookup"><span data-stu-id="bc4b9-549">Singleton</span></span>

<span data-ttu-id="bc4b9-550">I servizi con durata singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) vengono creati la prima volta che vengono richiesti (o quando si esegue `Startup.ConfigureServices` e viene specificata un'istanza con la registrazione del servizio).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-550">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="bc4b9-551">Tutte le richieste successive usano la stessa istanza.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-551">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="bc4b9-552">Se l'app richiede un comportamento singleton, è consigliabile consentire al contenitore del servizio di gestire la durata del servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-552">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="bc4b9-553">Non implementare lo schema progettuale singleton e fornire codice utente per gestire la durata dell'oggetto nella classe.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-553">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="bc4b9-554">Nelle app che elaborano le richieste, i servizi Singleton vengono eliminati quando il <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> viene eliminato alla chiusura dell'app.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-554">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="bc4b9-555">Può essere pericoloso risolvere un servizio con ambito da un singleton.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-555">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="bc4b9-556">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-556">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="bc4b9-557">Metodi di registrazione del servizio</span><span class="sxs-lookup"><span data-stu-id="bc4b9-557">Service registration methods</span></span>

<span data-ttu-id="bc4b9-558">I metodi di estensione per la registrazione del servizio offrono overload che risultano utili in scenari specifici.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-558">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="bc4b9-559">Metodo</span><span class="sxs-lookup"><span data-stu-id="bc4b9-559">Method</span></span> | <span data-ttu-id="bc4b9-560">Automatico</span><span class="sxs-lookup"><span data-stu-id="bc4b9-560">Automatic</span></span><br><span data-ttu-id="bc4b9-561">object</span><span class="sxs-lookup"><span data-stu-id="bc4b9-561">object</span></span><br><span data-ttu-id="bc4b9-562">eliminazione</span><span class="sxs-lookup"><span data-stu-id="bc4b9-562">disposal</span></span> | <span data-ttu-id="bc4b9-563">Più elementi</span><span class="sxs-lookup"><span data-stu-id="bc4b9-563">Multiple</span></span><br><span data-ttu-id="bc4b9-564">implementazioni</span><span class="sxs-lookup"><span data-stu-id="bc4b9-564">implementations</span></span> | <span data-ttu-id="bc4b9-565">Pass args</span><span class="sxs-lookup"><span data-stu-id="bc4b9-565">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="bc4b9-566">Esempio:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-566">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="bc4b9-567">Sì</span><span class="sxs-lookup"><span data-stu-id="bc4b9-567">Yes</span></span> | <span data-ttu-id="bc4b9-568">Sì</span><span class="sxs-lookup"><span data-stu-id="bc4b9-568">Yes</span></span> | <span data-ttu-id="bc4b9-569">No</span><span class="sxs-lookup"><span data-stu-id="bc4b9-569">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="bc4b9-570">Esempi:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-570">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="bc4b9-571">Sì</span><span class="sxs-lookup"><span data-stu-id="bc4b9-571">Yes</span></span> | <span data-ttu-id="bc4b9-572">Sì</span><span class="sxs-lookup"><span data-stu-id="bc4b9-572">Yes</span></span> | <span data-ttu-id="bc4b9-573">Sì</span><span class="sxs-lookup"><span data-stu-id="bc4b9-573">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="bc4b9-574">Esempio:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-574">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="bc4b9-575">Sì</span><span class="sxs-lookup"><span data-stu-id="bc4b9-575">Yes</span></span> | <span data-ttu-id="bc4b9-576">No</span><span class="sxs-lookup"><span data-stu-id="bc4b9-576">No</span></span> | <span data-ttu-id="bc4b9-577">No</span><span class="sxs-lookup"><span data-stu-id="bc4b9-577">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="bc4b9-578">Esempi:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-578">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="bc4b9-579">No</span><span class="sxs-lookup"><span data-stu-id="bc4b9-579">No</span></span> | <span data-ttu-id="bc4b9-580">Sì</span><span class="sxs-lookup"><span data-stu-id="bc4b9-580">Yes</span></span> | <span data-ttu-id="bc4b9-581">Sì</span><span class="sxs-lookup"><span data-stu-id="bc4b9-581">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="bc4b9-582">Esempi:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-582">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="bc4b9-583">No</span><span class="sxs-lookup"><span data-stu-id="bc4b9-583">No</span></span> | <span data-ttu-id="bc4b9-584">No</span><span class="sxs-lookup"><span data-stu-id="bc4b9-584">No</span></span> | <span data-ttu-id="bc4b9-585">Sì</span><span class="sxs-lookup"><span data-stu-id="bc4b9-585">Yes</span></span> |

<span data-ttu-id="bc4b9-586">Per ulteriori informazioni sull'eliminazione dei tipi, vedere la sezione [Eliminazione dei servizi](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-586">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="bc4b9-587">Uno scenario comune per più implementazioni è costituito dai [tipi di simulazioni per i test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-587">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="bc4b9-588">La registrazione di un servizio con un solo tipo di implementazione equivale alla registrazione del servizio con lo stesso tipo di implementazione e di servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-588">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="bc4b9-589">Questo è il motivo per cui non è possibile registrare più implementazioni di un servizio usando i metodi che non accettano un tipo di servizio esplicito.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-589">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="bc4b9-590">Questi metodi possono registrare più *istanze* di un servizio, ma avranno tutti lo stesso tipo di *implementazione* .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-590">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="bc4b9-591">Uno qualsiasi dei metodi di registrazione del servizio sopra indicati può essere utilizzato per registrare più istanze del servizio dello stesso tipo di servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-591">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="bc4b9-592">Nell'esempio seguente `AddSingleton` viene chiamato due volte con `IMyDependency` come tipo di servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-592">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="bc4b9-593">La seconda chiamata a `AddSingleton` sostituisce quella precedente quando viene risolta come `IMyDependency` e aggiunge a quella precedente quando più servizi vengono risolti tramite `IEnumerable<IMyDependency>` .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-593">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="bc4b9-594">I servizi vengono visualizzati nell'ordine in cui sono stati registrati durante la risoluzione tramite `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-594">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="bc4b9-595">Il Framework fornisce anche `TryAdd{LIFETIME}` metodi di estensione, che registrano il servizio solo se non è già stata registrata un'implementazione.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-595">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="bc4b9-596">Nell'esempio seguente, la chiamata a `AddSingleton` Registra `MyDependency` come implementazione per `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-596">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="bc4b9-597">La chiamata a `TryAddSingleton` non ha alcun effetto perché `IMyDependency` dispone già di un'implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-597">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="bc4b9-598">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-598">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="bc4b9-599">I metodi [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrano il servizio solo se non esiste già un'implementazione *dello stesso tipo* .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-599">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type* .</span></span> <span data-ttu-id="bc4b9-600">Più servizi vengono risolti tramite `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-600">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="bc4b9-601">Durante la registrazione di servizi, lo sviluppatore desidera aggiungere un'istanza solo se non ne è già stata aggiunta una dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-601">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="bc4b9-602">In genere, questo metodo viene utilizzato dagli autori di librerie per evitare di registrare due copie di un'istanza nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-602">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="bc4b9-603">Nell'esempio seguente, la prima riga registra `MyDep` per `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-603">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="bc4b9-604">La seconda riga registra `MyDep` per `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-604">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="bc4b9-605">La seconda riga non ha alcun effetto perché `IMyDep1` dispone già di un'implementazione registrata di `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-605">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="bc4b9-606">Comportamento dell'inserimento del costruttore</span><span class="sxs-lookup"><span data-stu-id="bc4b9-606">Constructor injection behavior</span></span>

<span data-ttu-id="bc4b9-607">I servizi possono essere risolti con due meccanismi:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-607">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="bc4b9-608"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Consente la creazione di oggetti senza registrazione del servizio nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-608"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="bc4b9-609">`ActivatorUtilities` viene usato con astrazioni esposte all'utente, ad esempio helper tag, controller MVC e strumenti di associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-609">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="bc4b9-610">I costruttori possono accettare argomenti non forniti tramite l'inserimento di dipendenze, ma gli argomenti devono assegnare valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-610">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="bc4b9-611">Quando i servizi vengono risolti da `IServiceProvider` o `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede un costruttore *pubblico* .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-611">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="bc4b9-612">Quando i servizi vengono risolti da `ActivatorUtilities` , l' [inserimento del costruttore](xref:mvc/controllers/dependency-injection#constructor-injection) richiede l'esistenza di un solo costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-612">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="bc4b9-613">Sebbene siano supportati gli overload dei costruttori, può essere presente un solo overload i cui argomenti possono essere tutti specificati tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-613">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="bc4b9-614">Contesti di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="bc4b9-614">Entity Framework contexts</span></span>

<span data-ttu-id="bc4b9-615">I contesti di Entity Framework vengono in genere aggiunti al contenitore di servizi usando la [durata con ambito](#service-lifetimes) perché le operazioni di database delle app Web hanno di solito come ambito la richiesta client.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-615">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="bc4b9-616">L'ambito della durata predefinita è se una durata non è specificata da un overload [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) durante la registrazione del contesto del database.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-616">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="bc4b9-617">I servizi con una durata specificata non devono usare un contesto di database con una durata più breve rispetto al servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-617">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="bc4b9-618">Opzioni di durata e di registrazione</span><span class="sxs-lookup"><span data-stu-id="bc4b9-618">Lifetime and registration options</span></span>

<span data-ttu-id="bc4b9-619">Per illustrare la differenza tra le opzioni di durata e registrazione, si considerino le interfacce seguenti che rappresentano le attività come un'operazione con un identificatore univoco, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-619">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="bc4b9-620">A seconda del modo in cui la durata di un servizio operativo viene configurata per le interfacce seguenti, il contenitore fornisce la stessa istanza o un'istanza diversa del servizio quando richiesto da una classe:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-620">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="bc4b9-621">Le interfacce vengono implementate nella classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-621">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="bc4b9-622">Il costruttore `Operation` genera un GUID se non ne viene fornito uno:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-622">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="bc4b9-623">Viene registrato un `OperationService` che dipende da ognuno degli altri tipi `Operation`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-623">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="bc4b9-624">Quando viene richiesto `OperationService` tramite l'inserimento delle dipendenze, riceve una nuova istanza di ogni servizio o un'istanza esistente in base alla durata del servizio dipendente.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-624">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="bc4b9-625">Se i servizi temporanei vengono creati quando vengono richiesti dal contenitore, `OperationId` del servizio `IOperationTransient` è diverso da `OperationId` di `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-625">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="bc4b9-626">`OperationService` riceve una nuova istanza della classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-626">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="bc4b9-627">La nuova istanza restituisce un `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-627">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="bc4b9-628">Se i servizi con ambito vengono creati per ogni richiesta client, `OperationId` del servizio `IOperationScoped` corrisponde a `OperationService` all'interno di una richiesta client.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-628">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="bc4b9-629">In tutte le richieste client entrambi i servizi condividono un valore `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-629">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="bc4b9-630">Se i servizi singleton e con istanza singleton vengono creati una sola volta e usati per tutte le richieste client e tutti i servizi, `OperationId` rimane costante in tutte le richieste di servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-630">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="bc4b9-631">In `Startup.ConfigureServices`, ogni tipo viene aggiunto al contenitore in base alla durata denominata:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-631">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="bc4b9-632">Il servizio `IOperationSingletonInstance` usa un'istanza specifica con un ID noto di `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-632">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="bc4b9-633">È chiaro quando questo tipo è in uso (il relativo GUID è composto da tutti zero).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-633">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="bc4b9-634">L'app di esempio dimostra le durate degli oggetti all'interno e tra le singole richieste.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-634">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="bc4b9-635">L'`IndexModel` dell'app di esempio richiede ogni genere di tipo `IOperation` e `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-635">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="bc4b9-636">La pagina visualizza quindi tutti i valori `OperationId` della classe del modello di pagina e del servizio tramite assegnazioni di proprietà:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-636">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="bc4b9-637">L'output seguente mostra i risultati delle due richieste:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-637">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="bc4b9-638">**Prima richiesta:**</span><span class="sxs-lookup"><span data-stu-id="bc4b9-638">**First request:**</span></span>

<span data-ttu-id="bc4b9-639">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-639">Controller operations:</span></span>

<span data-ttu-id="bc4b9-640">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="bc4b9-640">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="bc4b9-641">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="bc4b9-641">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="bc4b9-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="bc4b9-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="bc4b9-643">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="bc4b9-643">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="bc4b9-644">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-644">`OperationService` operations:</span></span>

<span data-ttu-id="bc4b9-645">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="bc4b9-645">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="bc4b9-646">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="bc4b9-646">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="bc4b9-647">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="bc4b9-647">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="bc4b9-648">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="bc4b9-648">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="bc4b9-649">**Seconda richiesta:**</span><span class="sxs-lookup"><span data-stu-id="bc4b9-649">**Second request:**</span></span>

<span data-ttu-id="bc4b9-650">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-650">Controller operations:</span></span>

<span data-ttu-id="bc4b9-651">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="bc4b9-651">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="bc4b9-652">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="bc4b9-652">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="bc4b9-653">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="bc4b9-653">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="bc4b9-654">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="bc4b9-654">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="bc4b9-655">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-655">`OperationService` operations:</span></span>

<span data-ttu-id="bc4b9-656">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="bc4b9-656">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="bc4b9-657">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="bc4b9-657">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="bc4b9-658">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="bc4b9-658">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="bc4b9-659">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="bc4b9-659">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="bc4b9-660">Osservare i valori `OperationId` che variano all'interno della richiesta e da una richiesta e l'altra:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-660">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="bc4b9-661">Gli oggetti *temporanei* sono sempre diversi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-661">*Transient* objects are always different.</span></span> <span data-ttu-id="bc4b9-662">Il valore `OperationId` temporaneo per la prima e la seconda richiesta client è diverso per entrambe le operazioni `OperationService` e in tutte le richieste client.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-662">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="bc4b9-663">Viene fornita una nuova istanza per ogni richiesta di servizio e richiesta client.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-663">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="bc4b9-664">Gli oggetti *con ambito* sono gli stessi all'interno di una richiesta client, ma variano nelle diverse richieste client.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-664">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="bc4b9-665">Gli oggetti *singleton* sono gli stessi per ogni oggetto e per ogni richiesta, indipendentemente dal fatto che un' `Operation` istanza venga fornita in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-665">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="bc4b9-666">Chiamare i servizi da main</span><span class="sxs-lookup"><span data-stu-id="bc4b9-666">Call services from main</span></span>

<span data-ttu-id="bc4b9-667">Creare un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>IServiceScope con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) per risolvere un servizio con ambito nell'ambito di applicazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-667">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="bc4b9-668">Questo approccio è utile per l'accesso a un servizio con ambito all'avvio e per l'esecuzione di attività di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-668">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="bc4b9-669">L'esempio seguente indica come ottenere un contesto per `MyScopedService` in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-669">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="bc4b9-670">Convalida dell'ambito</span><span class="sxs-lookup"><span data-stu-id="bc4b9-670">Scope validation</span></span>

<span data-ttu-id="bc4b9-671">Quando l'app viene eseguita nell'ambiente di sviluppo, il provider di servizi predefinito esegue controlli per verificare che:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-671">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="bc4b9-672">I servizi con ambito non vengano risolti direttamente o indirettamente dal provider di servizi radice.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-672">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="bc4b9-673">I servizi con ambito non vengano inseriti direttamente o indirettamente in singleton.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-673">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="bc4b9-674">Il provider di servizi radice venga creato con la chiamata di <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-674">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="bc4b9-675">La durata del provider di servizi radice corrisponde alla durata dell'app/server quando il provider viene avviato con l'app e viene eliminato alla chiusura dell'app.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-675">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="bc4b9-676">I servizi con ambito vengono eliminati dal contenitore che li ha creati.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-676">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="bc4b9-677">Se un servizio con ambito viene creato nel contenitore radice, la durata del servizio viene di fatto convertita in singleton, perché il servizio viene eliminato solo dal contenitore radice alla chiusura dell'app/server.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-677">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="bc4b9-678">La convalida degli ambiti servizio rileva queste situazioni nel contesto della chiamata di `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-678">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="bc4b9-679">Per altre informazioni, vedere <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-679">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="bc4b9-680">Servizi di richiesta</span><span class="sxs-lookup"><span data-stu-id="bc4b9-680">Request Services</span></span>

<span data-ttu-id="bc4b9-681">I servizi disponibili all'interno di una richiesta ASP.NET Core da `HttpContext` vengono esposti tramite la raccolta [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-681">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="bc4b9-682">I servizi di richiesta rappresentano i servizi configurati e richiesti come parte dell'app.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-682">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="bc4b9-683">Quando gli oggetti specificano dipendenze, le dipendenze sono soddisfatte dai tipi individuati in `RequestServices`, non in `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-683">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="bc4b9-684">In generale, l'app non deve usare queste proprietà direttamente.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-684">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="bc4b9-685">Richiedere invece i tipi richiesti dalle classi tramite i costruttori di classi e consentire al framework di inserire le dipendenze.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-685">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="bc4b9-686">Si ottengono classi più facili da testare.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-686">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="bc4b9-687">È consigliabile richiedere le dipendenze come parametri del costruttore per l'accesso alla raccolta `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-687">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="bc4b9-688">Progettare i servizi per l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="bc4b9-688">Design services for dependency injection</span></span>

<span data-ttu-id="bc4b9-689">Le procedure consigliate prevedono di:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-689">Best practices are to:</span></span>

* <span data-ttu-id="bc4b9-690">Progettare i servizi per usare l'inserimento delle dipendenze per ottenere le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-690">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="bc4b9-691">Evitare membri e classi statiche con stato.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-691">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="bc4b9-692">Progettare le app in modo da usare i servizi singleton, evitando così di creare uno stato globale.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-692">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="bc4b9-693">Evitare la creazione diretta di istanze delle classi dipendenti all'interno di servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-693">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="bc4b9-694">La creazione diretta di istanze associa il codice a una determinata implementazione.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-694">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="bc4b9-695">Fare in modo che le classi siano piccole, con factoring corretto e facili da testare.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-695">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="bc4b9-696">Se una classe sembra avere troppe dipendenze inserite, ciò significa in genere che la classe ha un numero eccessivo di responsabilità e sta violando il [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (principio di responsabilità singola).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-696">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="bc4b9-697">Tentare di effettuare il refactoring della classe spostando alcune delle responsabilità in una nuova classe.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-697">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="bc4b9-698">Tenere presente che :::no-loc(Razor)::: le classi del modello della pagina delle pagine e le classi controller MVC devono concentrarsi sulle problematiche dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-698">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="bc4b9-699">Di conseguenza, le regole business e i dettagli di implementazione di accesso ai dati devono essere inseriti in classi appropriate per queste [problematiche separate](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-699">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="bc4b9-700">Eliminazione dei servizi</span><span class="sxs-lookup"><span data-stu-id="bc4b9-700">Disposal of services</span></span>

<span data-ttu-id="bc4b9-701">Il contenitore chiama <xref:System.IDisposable.Dispose*> per i tipi <xref:System.IDisposable> creati.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-701">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="bc4b9-702">Se un'istanza viene aggiunta al contenitore dal codice utente, non viene eliminata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-702">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="bc4b9-703">Nell'esempio seguente i servizi vengono creati dal contenitore del servizio ed eliminati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-703">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="bc4b9-704">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-704">In the following example:</span></span>

* <span data-ttu-id="bc4b9-705">Le istanze del servizio non vengono create dal contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-705">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="bc4b9-706">Le durate di servizio previste non sono note dal Framework.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-706">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="bc4b9-707">Il Framework non elimina automaticamente i servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-707">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="bc4b9-708">Se i servizi non vengono eliminati in modo esplicito nel codice dello sviluppatore, vengono mantenuti fino a quando l'app non viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-708">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="bc4b9-709">Linee guida IDisposable per istanze temporanee e condivise</span><span class="sxs-lookup"><span data-stu-id="bc4b9-709">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="bc4b9-710">Durata temporanea e limitata</span><span class="sxs-lookup"><span data-stu-id="bc4b9-710">Transient, limited lifetime</span></span>

<span data-ttu-id="bc4b9-711">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="bc4b9-711">**Scenario**</span></span>

<span data-ttu-id="bc4b9-712">L'app richiede un' <xref:System.IDisposable> istanza con una durata temporanea per uno degli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-712">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="bc4b9-713">L'istanza viene risolta nell'ambito radice.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-713">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="bc4b9-714">L'istanza deve essere eliminata prima della fine dell'ambito.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-714">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="bc4b9-715">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="bc4b9-715">**Solution**</span></span>

<span data-ttu-id="bc4b9-716">Usare il modello factory per creare un'istanza all'esterno dell'ambito padre.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-716">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="bc4b9-717">In questa situazione, in genere l'app dispone di un `Create` metodo che chiama direttamente il costruttore del tipo finale.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-717">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="bc4b9-718">Se il tipo finale ha altre dipendenze, la factory può:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-718">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="bc4b9-719">Ricevere un oggetto <xref:System.IServiceProvider> nel relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-719">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="bc4b9-720">Usare <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> per creare un'istanza dell'istanza all'esterno del contenitore, usando il contenitore per le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-720">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="bc4b9-721">Istanza condivisa, durata limitata</span><span class="sxs-lookup"><span data-stu-id="bc4b9-721">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="bc4b9-722">**Scenario**</span><span class="sxs-lookup"><span data-stu-id="bc4b9-722">**Scenario**</span></span>

<span data-ttu-id="bc4b9-723">L'app richiede un' <xref:System.IDisposable> istanza condivisa tra più servizi, ma <xref:System.IDisposable> deve avere una durata limitata.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-723">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="bc4b9-724">**Soluzione**</span><span class="sxs-lookup"><span data-stu-id="bc4b9-724">**Solution**</span></span>

<span data-ttu-id="bc4b9-725">Registrare l'istanza con una durata con ambito.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-725">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="bc4b9-726">Usare <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> per avviare e creare un nuovo oggetto <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-726">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="bc4b9-727">Usare l'ambito <xref:System.IServiceProvider> per ottenere i servizi richiesti.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-727">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="bc4b9-728">Eliminare l'ambito quando la durata deve terminare.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-728">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="bc4b9-729">Linee guida generali</span><span class="sxs-lookup"><span data-stu-id="bc4b9-729">General Guidelines</span></span>

* <span data-ttu-id="bc4b9-730">Non registrare <xref:System.IDisposable> istanze con un ambito temporaneo.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-730">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="bc4b9-731">Usare invece il modello Factory.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-731">Use the factory pattern instead.</span></span>
* <span data-ttu-id="bc4b9-732">Non risolvere istanze temporanee o <xref:System.IDisposable> con ambito nell'ambito radice.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-732">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="bc4b9-733">L'unica eccezione generale si verifica quando l'app crea/ricrea ed Elimina <xref:System.IServiceProvider> , che non è un modello ideale.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-733">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="bc4b9-734">Per <xref:System.IDisposable> la ricezione di una dipendenza tramite di non è necessario che il ricevitore implementi <xref:System.IDisposable> se stesso.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-734">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="bc4b9-735">Il ricevitore della <xref:System.IDisposable> dipendenza non deve chiamare <xref:System.IDisposable.Dispose%2A> su tale dipendenza.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-735">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="bc4b9-736">Gli ambiti devono essere usati per controllare la durata dei servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-736">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="bc4b9-737">Gli ambiti non sono gerarchici e non esiste alcuna connessione speciale tra gli ambiti.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-737">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="bc4b9-738">Sostituzione del contenitore di servizi predefinito</span><span class="sxs-lookup"><span data-stu-id="bc4b9-738">Default service container replacement</span></span>

<span data-ttu-id="bc4b9-739">Il contenitore dei servizi incorporato è progettato per soddisfare le esigenze del Framework e della maggior parte delle app consumer.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-739">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="bc4b9-740">Si consiglia di usare il contenitore predefinito a meno che non sia necessaria una funzionalità specifica che il contenitore incorporato non supporta, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-740">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="bc4b9-741">Inserimento di proprietà</span><span class="sxs-lookup"><span data-stu-id="bc4b9-741">Property injection</span></span>
* <span data-ttu-id="bc4b9-742">Inserimento in base al nome</span><span class="sxs-lookup"><span data-stu-id="bc4b9-742">Injection based on name</span></span>
* <span data-ttu-id="bc4b9-743">Contenitori figlio</span><span class="sxs-lookup"><span data-stu-id="bc4b9-743">Child containers</span></span>
* <span data-ttu-id="bc4b9-744">Gestione della durata personalizzata</span><span class="sxs-lookup"><span data-stu-id="bc4b9-744">Custom lifetime management</span></span>
* <span data-ttu-id="bc4b9-745">Supporto di `Func<T>` per l'inizializzazione differita</span><span class="sxs-lookup"><span data-stu-id="bc4b9-745">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="bc4b9-746">Registrazione basata sulle convenzioni</span><span class="sxs-lookup"><span data-stu-id="bc4b9-746">Convention-based registration</span></span>

<span data-ttu-id="bc4b9-747">Con ASP.NET Core app è possibile usare i contenitori di terze parti seguenti:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-747">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="bc4b9-748">Autofac</span><span class="sxs-lookup"><span data-stu-id="bc4b9-748">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="bc4b9-749">DryIoc</span><span class="sxs-lookup"><span data-stu-id="bc4b9-749">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="bc4b9-750">Laura</span><span class="sxs-lookup"><span data-stu-id="bc4b9-750">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="bc4b9-751">LightInject</span><span class="sxs-lookup"><span data-stu-id="bc4b9-751">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="bc4b9-752">Lamar</span><span class="sxs-lookup"><span data-stu-id="bc4b9-752">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="bc4b9-753">Stashbox</span><span class="sxs-lookup"><span data-stu-id="bc4b9-753">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="bc4b9-754">Unity</span><span class="sxs-lookup"><span data-stu-id="bc4b9-754">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="bc4b9-755">Thread safety</span><span class="sxs-lookup"><span data-stu-id="bc4b9-755">Thread safety</span></span>

<span data-ttu-id="bc4b9-756">Creare servizi singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-756">Create thread-safe singleton services.</span></span> <span data-ttu-id="bc4b9-757">Se un servizio singleton ha una dipendenza in un servizio temporaneo, è necessario che anche il servizio temporaneo sia thread-safe, a seconda di come viene usato dal singleton.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-757">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="bc4b9-758">Il metodo factory di un singolo servizio, ad esempio il secondo argomento di [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), non deve essere thread-safe.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-758">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="bc4b9-759">Come nel caso di un costruttore di tipo (`static`), è garantito che venga chiamato una sola volta da un singolo thread.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-759">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="bc4b9-760">Consigli</span><span class="sxs-lookup"><span data-stu-id="bc4b9-760">Recommendations</span></span>

* <span data-ttu-id="bc4b9-761">La risoluzione basata sui servizi `async/await` e `Task` non è supportata.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-761">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="bc4b9-762">Dato che C# non supporta i costruttori asincroni, il modello consigliato consiste nell'usare i metodi asincroni dopo avere risolto in modo sincrono il servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-762">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="bc4b9-763">Evitare di archiviare i dati e la configurazione direttamente nel contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-763">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="bc4b9-764">Ad esempio, il carrello acquisti di un utente non dovrebbe in genere essere aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-764">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="bc4b9-765">La configurazione deve usare il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-765">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="bc4b9-766">Analogamente, evitare gli oggetti "contenitori di dati" che hanno la sola funzione di consentire l'accesso ad altri oggetti.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-766">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="bc4b9-767">È preferibile richiedere l'elemento effettivo tramite inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-767">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="bc4b9-768">Evitare l'accesso statico ai servizi.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-768">Avoid static access to services.</span></span> <span data-ttu-id="bc4b9-769">Ad esempio, evitare di digitare in modo statico [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) per l'uso altrove.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-769">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="bc4b9-770">Evitare di usare il *modello del localizzatore di servizi* , che combina [inversione delle strategie di controllo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-770">Avoid using the *service locator pattern* , which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="bc4b9-771">Non richiamare <xref:System.IServiceProvider.GetService*> per ottenere un'istanza del servizio quando si può usare invece di:</span><span class="sxs-lookup"><span data-stu-id="bc4b9-771">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="bc4b9-772">**Non corretto:**</span><span class="sxs-lookup"><span data-stu-id="bc4b9-772">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="bc4b9-773">**Corretto** :</span><span class="sxs-lookup"><span data-stu-id="bc4b9-773">**Correct** :</span></span>

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

* <span data-ttu-id="bc4b9-774">Evitare di inserire una factory che risolve le dipendenze in fase di esecuzione usando <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="bc4b9-774">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="bc4b9-775">Evitare l'accesso statico a `HttpContext` (ad esempio [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="bc4b9-775">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="bc4b9-776">È tuttavia possibile che in alcuni casi queste raccomandazioni debbano essere ignorate.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-776">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="bc4b9-777">Le eccezioni sono rari, soprattutto casi speciali all'interno del Framework stesso.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-777">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="bc4b9-778">L'inserimento di dipendenze è un' *alternativa* ai modelli di accesso agli oggetti statici/globali.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-778">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="bc4b9-779">Se l'inserimento di dipendenze viene usato con l'accesso agli oggetti statico i vantaggi non saranno evidenti.</span><span class="sxs-lookup"><span data-stu-id="bc4b9-779">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bc4b9-780">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="bc4b9-780">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="bc4b9-781">Quattro modi per eliminare IDisposable in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bc4b9-781">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="bc4b9-782">Scrittura di codice pulito in ASP.NET Core con inserimento delle dipendenze (MSDN)</span><span class="sxs-lookup"><span data-stu-id="bc4b9-782">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="bc4b9-783">Explicit Dependencies Principle (Principio delle dipendenze esplicite)</span><span class="sxs-lookup"><span data-stu-id="bc4b9-783">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="bc4b9-784">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler) (Contenitori di inversione del controllo e modello di inserimento delle dipendenze)</span><span class="sxs-lookup"><span data-stu-id="bc4b9-784">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="bc4b9-785">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Come registrare un servizio con più interfacce in ASP.NET Core DI)</span><span class="sxs-lookup"><span data-stu-id="bc4b9-785">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
