---
title: BlazorInserimento di dipendenze ASP.NET Core
author: guardrex
description: Informazioni su come le Blazor app possono inserire i servizi nei componenti.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: 0e99e2e3e2dafae0c35d2cfe6903bf4f511f5dc1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402884"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="24083-103">BlazorInserimento di dipendenze ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="24083-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="24083-104">Di [Rainer Stropek](https://www.timecockpit.com) e [Mike entusiasmanti](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="24083-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

Blazor<span data-ttu-id="24083-105">supporta l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="24083-105"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="24083-106">Le app possono usare i servizi predefiniti inserendoli in componenti.</span><span class="sxs-lookup"><span data-stu-id="24083-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="24083-107">Le app possono anche definire e registrare servizi personalizzati e renderli disponibili nell'app tramite DI.</span><span class="sxs-lookup"><span data-stu-id="24083-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="24083-108">DI è una tecnica per accedere ai servizi configurati in una posizione centrale.</span><span class="sxs-lookup"><span data-stu-id="24083-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="24083-109">Questa operazione può essere utile nelle Blazor app per:</span><span class="sxs-lookup"><span data-stu-id="24083-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="24083-110">Condividere una singola istanza di una classe di servizio in molti componenti, noti come un servizio *singleton* .</span><span class="sxs-lookup"><span data-stu-id="24083-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="24083-111">Separare i componenti da classi di servizi concrete usando astrazioni di riferimento.</span><span class="sxs-lookup"><span data-stu-id="24083-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="24083-112">Si consideri ad esempio un'interfaccia `IDataAccess` per l'accesso ai dati nell'app.</span><span class="sxs-lookup"><span data-stu-id="24083-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="24083-113">L'interfaccia viene implementata da una `DataAccess` classe concreta e registrata come servizio nel contenitore del servizio dell'app.</span><span class="sxs-lookup"><span data-stu-id="24083-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="24083-114">Quando un componente usa il per ricevere un' `IDataAccess` implementazione di, il componente non è associato al tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="24083-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="24083-115">L'implementazione può essere scambiata, ad esempio per un'implementazione fittizia negli unit test.</span><span class="sxs-lookup"><span data-stu-id="24083-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="24083-116">Servizi predefiniti</span><span class="sxs-lookup"><span data-stu-id="24083-116">Default services</span></span>

<span data-ttu-id="24083-117">I servizi predefiniti vengono aggiunti automaticamente alla raccolta di servizi dell'app.</span><span class="sxs-lookup"><span data-stu-id="24083-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="24083-118">Servizio</span><span class="sxs-lookup"><span data-stu-id="24083-118">Service</span></span> | <span data-ttu-id="24083-119">Durata</span><span class="sxs-lookup"><span data-stu-id="24083-119">Lifetime</span></span> | <span data-ttu-id="24083-120">Descrizione</span><span class="sxs-lookup"><span data-stu-id="24083-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="24083-121">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="24083-121">Transient</span></span> | <span data-ttu-id="24083-122">Fornisce metodi per l'invio di richieste HTTP e la ricezione di risposte HTTP da una risorsa identificata da un URI.</span><span class="sxs-lookup"><span data-stu-id="24083-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="24083-123">L'istanza di <xref:System.Net.Http.HttpClient> in un' Blazor WebAssembly applicazione utilizza il browser per gestire il traffico HTTP in background.</span><span class="sxs-lookup"><span data-stu-id="24083-123">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br>Blazor Server<span data-ttu-id="24083-124">per impostazione predefinita, le app non includono un <xref:System.Net.Http.HttpClient> configurato come servizio.</span><span class="sxs-lookup"><span data-stu-id="24083-124"> apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="24083-125">Fornire un <xref:System.Net.Http.HttpClient> a un' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="24083-125">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="24083-126">Per altre informazioni, vedere <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="24083-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="24083-127">Singleton ( Blazor WebAssembly )</span><span class="sxs-lookup"><span data-stu-id="24083-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="24083-128">Con ambito ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="24083-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="24083-129">Rappresenta un'istanza di un runtime JavaScript in cui vengono inviate le chiamate a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="24083-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="24083-130">Per altre informazioni, vedere <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="24083-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="24083-131">Singleton ( Blazor WebAssembly )</span><span class="sxs-lookup"><span data-stu-id="24083-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="24083-132">Con ambito ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="24083-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="24083-133">Contiene gli helper per lavorare con gli URI e lo stato di navigazione.</span><span class="sxs-lookup"><span data-stu-id="24083-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="24083-134">Per ulteriori informazioni, vedere [URI e Helper dello stato di navigazione](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="24083-134">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="24083-135">Un provider di servizi personalizzato non fornisce automaticamente i servizi predefiniti elencati nella tabella.</span><span class="sxs-lookup"><span data-stu-id="24083-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="24083-136">Se si usa un provider di servizi personalizzato e si richiede uno dei servizi indicati nella tabella, aggiungere i servizi necessari al nuovo provider di servizi.</span><span class="sxs-lookup"><span data-stu-id="24083-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="24083-137">Aggiungere servizi a un'app</span><span class="sxs-lookup"><span data-stu-id="24083-137">Add services to an app</span></span>

### Blazor WebAssembly

<span data-ttu-id="24083-138">Configurare i servizi per la raccolta di servizi dell'app nel `Main` metodo di `Program.cs` .</span><span class="sxs-lookup"><span data-stu-id="24083-138">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="24083-139">Nell'esempio seguente l' `MyDependency` implementazione è registrata per `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="24083-139">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="24083-140">Una volta compilato l'host, è possibile accedere ai servizi dall'ambito radice prima DI eseguire il rendering di tutti i componenti.</span><span class="sxs-lookup"><span data-stu-id="24083-140">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="24083-141">Questa operazione può essere utile per eseguire la logica di inizializzazione prima del rendering del contenuto:</span><span class="sxs-lookup"><span data-stu-id="24083-141">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="24083-142">L'host fornisce anche un'istanza di configurazione centrale per l'app.</span><span class="sxs-lookup"><span data-stu-id="24083-142">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="24083-143">Basandosi sull'esempio precedente, l'URL del servizio meteo viene passato da un'origine di configurazione predefinita (ad esempio, `appsettings.json` ) a `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="24083-143">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

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

### Blazor Server

<span data-ttu-id="24083-144">Dopo aver creato una nuova app, esaminare il `Startup.ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="24083-144">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="24083-145">Al <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> metodo viene passato un oggetto <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , che è un elenco di oggetti del descrittore del servizio ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ).</span><span class="sxs-lookup"><span data-stu-id="24083-145">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="24083-146">I servizi vengono aggiunti fornendo descrittori del servizio alla raccolta di servizi.</span><span class="sxs-lookup"><span data-stu-id="24083-146">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="24083-147">Nell'esempio seguente viene illustrato il concetto con l' `IDataAccess` interfaccia e la relativa implementazione concreta `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="24083-147">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="24083-148">Durata del servizio</span><span class="sxs-lookup"><span data-stu-id="24083-148">Service lifetime</span></span>

<span data-ttu-id="24083-149">I servizi possono essere configurati con le durate mostrate nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="24083-149">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="24083-150">Durata</span><span class="sxs-lookup"><span data-stu-id="24083-150">Lifetime</span></span> | <span data-ttu-id="24083-151">Descrizione</span><span class="sxs-lookup"><span data-stu-id="24083-151">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssembly<span data-ttu-id="24083-152">Attualmente le app non hanno un concetto di ambiti di.</span><span class="sxs-lookup"><span data-stu-id="24083-152"> apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="24083-153">`Scoped`-i servizi registrati si comportano come `Singleton` servizi.</span><span class="sxs-lookup"><span data-stu-id="24083-153">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="24083-154">Tuttavia, il Blazor Server modello di hosting supporta il `Scoped` ciclo di vita.</span><span class="sxs-lookup"><span data-stu-id="24083-154">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="24083-155">Nelle Blazor Server app, la registrazione di un servizio con ambito ha come ambito la *connessione*.</span><span class="sxs-lookup"><span data-stu-id="24083-155">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="24083-156">Per questo motivo, è preferibile usare i servizi con ambito per i servizi che devono avere come ambito l'utente corrente, anche se l'obiettivo corrente è eseguire sul lato client nel browser.</span><span class="sxs-lookup"><span data-stu-id="24083-156">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="24083-157">La creazione di una *singola istanza* del servizio.</span><span class="sxs-lookup"><span data-stu-id="24083-157">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="24083-158">Tutti i componenti che richiedono un `Singleton` servizio ricevono un'istanza dello stesso servizio.</span><span class="sxs-lookup"><span data-stu-id="24083-158">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="24083-159">Ogni volta che un componente ottiene un'istanza di un `Transient` servizio dal contenitore del servizio, riceve una *nuova istanza* del servizio.</span><span class="sxs-lookup"><span data-stu-id="24083-159">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="24083-160">Il sistema DI è basato sul sistema DI ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="24083-160">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="24083-161">Per altre informazioni, vedere <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="24083-161">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="24083-162">Richiedere un servizio in un componente</span><span class="sxs-lookup"><span data-stu-id="24083-162">Request a service in a component</span></span>

<span data-ttu-id="24083-163">Una volta aggiunti i servizi alla raccolta di servizi, inserire i servizi nei componenti utilizzando la direttiva [ \@ Inject](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="24083-163">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="24083-164">[`@inject`](xref:mvc/views/razor#inject)dispone di due parametri:</span><span class="sxs-lookup"><span data-stu-id="24083-164">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="24083-165">Tipo: tipo di servizio da inserire.</span><span class="sxs-lookup"><span data-stu-id="24083-165">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="24083-166">Property: nome della proprietà che riceve il servizio app inserito.</span><span class="sxs-lookup"><span data-stu-id="24083-166">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="24083-167">La proprietà non richiede la creazione manuale.</span><span class="sxs-lookup"><span data-stu-id="24083-167">The property doesn't require manual creation.</span></span> <span data-ttu-id="24083-168">Il compilatore crea la proprietà.</span><span class="sxs-lookup"><span data-stu-id="24083-168">The compiler creates the property.</span></span>

<span data-ttu-id="24083-169">Per altre informazioni, vedere <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="24083-169">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="24083-170">Usare più [`@inject`](xref:mvc/views/razor#inject) istruzioni per inserire servizi diversi.</span><span class="sxs-lookup"><span data-stu-id="24083-170">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="24083-171">Nell'esempio seguente viene illustrato come utilizzare [`@inject`](xref:mvc/views/razor#inject) .</span><span class="sxs-lookup"><span data-stu-id="24083-171">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="24083-172">Il servizio che implementa `Services.IDataAccess` viene inserito nella proprietà del componente `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="24083-172">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="24083-173">Si noti come il codice usa solo l' `IDataAccess` astrazione:</span><span class="sxs-lookup"><span data-stu-id="24083-173">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="24083-174">Internamente, la proprietà generata ( `DataRepository` ) utilizza l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="24083-174">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="24083-175">In genere, questo attributo non viene utilizzato direttamente.</span><span class="sxs-lookup"><span data-stu-id="24083-175">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="24083-176">Se è necessaria una classe base per i componenti e le proprietà inserite sono necessarie anche per la classe base, aggiungere manualmente l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attributo:</span><span class="sxs-lookup"><span data-stu-id="24083-176">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="24083-177">Nei componenti derivati dalla classe di base, la [`@inject`](xref:mvc/views/razor#inject) direttiva non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="24083-177">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="24083-178">La <xref:Microsoft.AspNetCore.Components.InjectAttribute> classe della classe base è sufficiente:</span><span class="sxs-lookup"><span data-stu-id="24083-178">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="24083-179">Usare l'inserimento DI dipendenze nei servizi</span><span class="sxs-lookup"><span data-stu-id="24083-179">Use DI in services</span></span>

<span data-ttu-id="24083-180">Servizi complessi potrebbe richiedere servizi aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="24083-180">Complex services might require additional services.</span></span> <span data-ttu-id="24083-181">Nell'esempio precedente, `DataAccess` potrebbe richiedere il <xref:System.Net.Http.HttpClient> servizio predefinito.</span><span class="sxs-lookup"><span data-stu-id="24083-181">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="24083-182">[`@inject`](xref:mvc/views/razor#inject)(o l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attributo) non è disponibile per l'uso nei servizi.</span><span class="sxs-lookup"><span data-stu-id="24083-182">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="24083-183">È necessario usare invece l' *inserimento del costruttore* .</span><span class="sxs-lookup"><span data-stu-id="24083-183">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="24083-184">I servizi necessari vengono aggiunti aggiungendo parametri al costruttore del servizio.</span><span class="sxs-lookup"><span data-stu-id="24083-184">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="24083-185">Quando si crea il servizio, vengono riconosciuti i servizi richiesti nel costruttore e forniti DI conseguenza.</span><span class="sxs-lookup"><span data-stu-id="24083-185">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="24083-186">Prerequisiti per l'inserimento del costruttore:</span><span class="sxs-lookup"><span data-stu-id="24083-186">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="24083-187">È necessario che esista un costruttore i cui argomenti possono essere tutti soddisfatti da DI.</span><span class="sxs-lookup"><span data-stu-id="24083-187">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="24083-188">Sono consentiti parametri aggiuntivi non analizzati da DI se specificano i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="24083-188">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="24083-189">Il costruttore applicabile deve essere `public` .</span><span class="sxs-lookup"><span data-stu-id="24083-189">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="24083-190">È necessario che esista un costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="24083-190">One applicable constructor must exist.</span></span> <span data-ttu-id="24083-191">In caso di ambiguità, viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="24083-191">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="24083-192">Classi di componenti di base dell'utilità per gestire un ambito DI</span><span class="sxs-lookup"><span data-stu-id="24083-192">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="24083-193">Nelle app ASP.NET Core, i servizi con ambito hanno in genere come ambito la richiesta corrente.</span><span class="sxs-lookup"><span data-stu-id="24083-193">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="24083-194">Al termine della richiesta, tutti i servizi con ambito o temporaneo vengono eliminati dal sistema DI.</span><span class="sxs-lookup"><span data-stu-id="24083-194">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="24083-195">Nelle Blazor Server app, l'ambito della richiesta dura per la durata della connessione client, che può comportare la permanenza di servizi temporanei e con ambito più a lungo del previsto.</span><span class="sxs-lookup"><span data-stu-id="24083-195">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="24083-196">Nelle Blazor WebAssembly app i servizi registrati con una durata con ambito vengono considerati come singleton, quindi vivono più a lungo rispetto ai servizi con ambito nelle app ASP.NET Core tipiche.</span><span class="sxs-lookup"><span data-stu-id="24083-196">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="24083-197">Per rilevare servizi temporanei monouso in un'app, vedere la sezione [rilevare gli eliminabili temporanei](#detect-transient-disposables) .</span><span class="sxs-lookup"><span data-stu-id="24083-197">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="24083-198">Un approccio che limita la durata di un servizio nelle Blazor app è l'uso del <xref:Microsoft.AspNetCore.Components.OwningComponentBase> tipo.</span><span class="sxs-lookup"><span data-stu-id="24083-198">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="24083-199"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>è un tipo astratto derivato da <xref:Microsoft.AspNetCore.Components.ComponentBase> che consente di creare un ambito di che corrisponde alla durata del componente.</span><span class="sxs-lookup"><span data-stu-id="24083-199"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="24083-200">Con questo ambito, è possibile usare i servizi DI i con una durata con ambito e fare in modo che siano attivi fino a quando il componente.</span><span class="sxs-lookup"><span data-stu-id="24083-200">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="24083-201">Quando il componente viene eliminato definitivamente, vengono eliminati anche i servizi del provider di servizi con ambito.</span><span class="sxs-lookup"><span data-stu-id="24083-201">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="24083-202">Questa operazione può essere utile per i servizi che:</span><span class="sxs-lookup"><span data-stu-id="24083-202">This can be useful for services that:</span></span>

* <span data-ttu-id="24083-203">È necessario riutilizzarlo all'interno di un componente, perché la durata temporanea non è appropriata.</span><span class="sxs-lookup"><span data-stu-id="24083-203">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="24083-204">Non devono essere condivise tra i componenti, perché la durata singleton non è appropriata.</span><span class="sxs-lookup"><span data-stu-id="24083-204">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="24083-205">Sono disponibili due versioni del <xref:Microsoft.AspNetCore.Components.OwningComponentBase> tipo:</span><span class="sxs-lookup"><span data-stu-id="24083-205">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="24083-206"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>è un elemento figlio astratto e disposable del <xref:Microsoft.AspNetCore.Components.ComponentBase> tipo con una <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> proprietà protetta di tipo <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="24083-206"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="24083-207">Questo provider può essere utilizzato per risolvere i servizi che hanno come ambito la durata del componente.</span><span class="sxs-lookup"><span data-stu-id="24083-207">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="24083-208">I servizi DI inserimento nel componente usando [`@inject`](xref:mvc/views/razor#inject) o l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attributo non vengono creati nell'ambito del componente.</span><span class="sxs-lookup"><span data-stu-id="24083-208">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="24083-209">Per utilizzare l'ambito del componente, i servizi devono essere risolti utilizzando <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> o <xref:System.IServiceProvider.GetService%2A> .</span><span class="sxs-lookup"><span data-stu-id="24083-209">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="24083-210">Tutti i servizi risolti utilizzando il <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider hanno le dipendenze fornite dallo stesso ambito.</span><span class="sxs-lookup"><span data-stu-id="24083-210">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="24083-211"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601>deriva da <xref:Microsoft.AspNetCore.Components.OwningComponentBase> e aggiunge una <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> proprietà che restituisce un'istanza di `T` dal provider dell'ambito di.</span><span class="sxs-lookup"><span data-stu-id="24083-211"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="24083-212">Questo tipo è un modo pratico per accedere ai servizi con ambito senza usare un'istanza di <xref:System.IServiceProvider> quando è presente un servizio primario richiesto dall'app dal contenitore di inserimento delle dipendenze usando l'ambito del componente.</span><span class="sxs-lookup"><span data-stu-id="24083-212">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="24083-213">La <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> proprietà è disponibile, in modo che l'app possa ottenere i servizi di altri tipi, se necessario.</span><span class="sxs-lookup"><span data-stu-id="24083-213">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="24083-214">Uso di Entity Framework DbContext da DI</span><span class="sxs-lookup"><span data-stu-id="24083-214">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="24083-215">Un tipo di servizio comune da recuperare da un in app Web è Entity Framework oggetti (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="24083-215">One common service type to retrieve from DI in web apps is Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> objects.</span></span> <span data-ttu-id="24083-216"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>Per impostazione predefinita, la registrazione dei servizi EF tramite aggiunge <xref:Microsoft.EntityFrameworkCore.DbContext> come servizio con ambito.</span><span class="sxs-lookup"><span data-stu-id="24083-216">Registering EF services using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> adds the <xref:Microsoft.EntityFrameworkCore.DbContext> as a scoped service by default.</span></span> <span data-ttu-id="24083-217">La registrazione come servizio con ambito può causare problemi nelle Blazor app perché causa <xref:Microsoft.EntityFrameworkCore.DbContext> la lunga durata delle istanze e la condivisione nell'app.</span><span class="sxs-lookup"><span data-stu-id="24083-217">Registering as a scoped service can lead to problems in Blazor apps because it causes <xref:Microsoft.EntityFrameworkCore.DbContext> instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="24083-218"><xref:Microsoft.EntityFrameworkCore.DbContext>non è thread-safe e non deve essere usato simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="24083-218"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="24083-219">A seconda dell'app, l'uso <xref:Microsoft.AspNetCore.Components.OwningComponentBase> di per limitare l'ambito di un <xref:Microsoft.EntityFrameworkCore.DbContext> a un singolo componente *può* risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="24083-219">Depending on the app, using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> to limit the scope of a <xref:Microsoft.EntityFrameworkCore.DbContext> to a single component *may* solve the issue.</span></span> <span data-ttu-id="24083-220">Se un componente non utilizza un oggetto <xref:Microsoft.EntityFrameworkCore.DbContext> in parallelo, la derivazione del componente da <xref:Microsoft.AspNetCore.Components.OwningComponentBase> e il recupero <xref:Microsoft.EntityFrameworkCore.DbContext> di da <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> è sufficiente perché garantisce quanto segue:</span><span class="sxs-lookup"><span data-stu-id="24083-220">If a component doesn't use a <xref:Microsoft.EntityFrameworkCore.DbContext> in parallel, deriving the component from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and retrieving the <xref:Microsoft.EntityFrameworkCore.DbContext> from <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> is sufficient because it ensures that:</span></span>

* <span data-ttu-id="24083-221">I componenti separati non condividono un <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="24083-221">Separate components don't share a <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>
* <span data-ttu-id="24083-222">Il <xref:Microsoft.EntityFrameworkCore.DbContext> viene vissuto solo fino a quando il componente dipende da esso.</span><span class="sxs-lookup"><span data-stu-id="24083-222">The <xref:Microsoft.EntityFrameworkCore.DbContext> lives only as long as the component depending on it.</span></span>

<span data-ttu-id="24083-223">Se un singolo componente può usare <xref:Microsoft.EntityFrameworkCore.DbContext> simultaneamente, ad esempio ogni volta che un utente seleziona un pulsante, anche l'uso di <xref:Microsoft.AspNetCore.Components.OwningComponentBase> non evita problemi con le operazioni EF simultanee.</span><span class="sxs-lookup"><span data-stu-id="24083-223">If a single component might use a <xref:Microsoft.EntityFrameworkCore.DbContext> concurrently (for example, every time a user selects a button), even using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="24083-224">In tal caso, usare un oggetto diverso <xref:Microsoft.EntityFrameworkCore.DbContext> per ogni operazione EF logica.</span><span class="sxs-lookup"><span data-stu-id="24083-224">In that case, use a different <xref:Microsoft.EntityFrameworkCore.DbContext> for each logical EF operation.</span></span> <span data-ttu-id="24083-225">Usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="24083-225">Use either of the following approaches:</span></span>

* <span data-ttu-id="24083-226">Creare <xref:Microsoft.EntityFrameworkCore.DbContext> direttamente usando <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> come argomento, che può essere recuperato da di ed è thread-safe.</span><span class="sxs-lookup"><span data-stu-id="24083-226">Create the <xref:Microsoft.EntityFrameworkCore.DbContext> directly using <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> as an argument, which can be retrieved from DI and is thread safe.</span></span>

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

* <span data-ttu-id="24083-227">Registrare <xref:Microsoft.EntityFrameworkCore.DbContext> nel contenitore del servizio con una durata temporanea:</span><span class="sxs-lookup"><span data-stu-id="24083-227">Register the <xref:Microsoft.EntityFrameworkCore.DbContext> in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="24083-228">Quando si registra il contesto, usare <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="24083-228">When registering the context, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="24083-229">Il <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> metodo di estensione accetta due parametri facoltativi di tipo <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> .</span><span class="sxs-lookup"><span data-stu-id="24083-229">The <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method takes two optional parameters of type <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.</span></span> <span data-ttu-id="24083-230">Per usare questo approccio, solo il `contextLifetime` parametro deve essere <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="24083-230">To use this approach, only the `contextLifetime` parameter needs to be <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="24083-231">`optionsLifetime`può contenere il valore predefinito di <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="24083-231">`optionsLifetime` can keep its default value of <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType>.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="24083-232">Il temporaneo <xref:Microsoft.EntityFrameworkCore.DbContext> può essere inserito normalmente (usando [`@inject`](xref:mvc/views/razor#inject) ) in componenti che non eseguono più operazioni EF in parallelo.</span><span class="sxs-lookup"><span data-stu-id="24083-232">The transient <xref:Microsoft.EntityFrameworkCore.DbContext> can be injected as normal (using [`@inject`](xref:mvc/views/razor#inject)) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="24083-233">Quelli che possono eseguire contemporaneamente più operazioni EF possono richiedere <xref:Microsoft.EntityFrameworkCore.DbContext> oggetti distinti per ogni operazione parallela usando <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> .</span><span class="sxs-lookup"><span data-stu-id="24083-233">Those that may perform multiple EF operations simultaneously can request separate <xref:Microsoft.EntityFrameworkCore.DbContext> objects for each parallel operation using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A>.</span></span>

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

## <a name="detect-transient-disposables"></a><span data-ttu-id="24083-234">Rilevare eliminabili temporanei</span><span class="sxs-lookup"><span data-stu-id="24083-234">Detect transient disposables</span></span>

<span data-ttu-id="24083-235">Gli esempi seguenti illustrano come rilevare servizi temporanei monouso in un'app che deve usare <xref:Microsoft.AspNetCore.Components.OwningComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="24083-235">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="24083-236">Per ulteriori informazioni, vedere la sezione relativa [alle classi dei componenti di base dell'utilità per gestire una](#utility-base-component-classes-to-manage-a-di-scope) sezione dell'ambito.</span><span class="sxs-lookup"><span data-stu-id="24083-236">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

### Blazor WebAssembly

<span data-ttu-id="24083-237">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="24083-237">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="24083-238">Nell' `TransientDisposable` esempio seguente viene rilevato ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="24083-238">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### Blazor Server

<span data-ttu-id="24083-239">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="24083-239">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="24083-240">`Program`:</span><span class="sxs-lookup"><span data-stu-id="24083-240">`Program`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

<span data-ttu-id="24083-241">Nell' `TransientDependency` esempio seguente viene rilevato ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="24083-241">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a><span data-ttu-id="24083-242">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="24083-242">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="24083-243">`IDisposable`linee guida per le istanze temporanee e condivise</span><span class="sxs-lookup"><span data-stu-id="24083-243">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
