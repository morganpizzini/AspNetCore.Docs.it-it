---
title: ASP.NET'iniezione di dipendenza da Core Blazor
author: guardrex
description: Scopri Blazor come le app possono inserire servizi nei componenti.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 4cdde9ee8c9fd9adf00894a067d32965b180e5ec
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658074"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="991eb-103">iniezione di dipendenza di ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="991eb-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="991eb-104">Di [Rainer Stropek](https://www.timecockpit.com) e [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="991eb-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="991eb-105">Blazor supporta l'iniezione di [dipendenza (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="991eb-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="991eb-106">Le app possono usare i servizi incorporati inserendoli nei componenti.</span><span class="sxs-lookup"><span data-stu-id="991eb-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="991eb-107">Le app possono anche definire e registrare servizi personalizzati e renderli disponibili in tutta l'app tramite DI.</span><span class="sxs-lookup"><span data-stu-id="991eb-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="991eb-108">DI è una tecnica per l'accesso ai servizi configurati in una posizione centrale.</span><span class="sxs-lookup"><span data-stu-id="991eb-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="991eb-109">Questo può essere utile nelle applicazioni Blazor per:</span><span class="sxs-lookup"><span data-stu-id="991eb-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="991eb-110">Condividere una singola istanza di una classe di servizio tra più componenti, noto come servizio *singleton.*</span><span class="sxs-lookup"><span data-stu-id="991eb-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="991eb-111">Separare i componenti da classi di servizio concrete usando astrazioni di riferimento.</span><span class="sxs-lookup"><span data-stu-id="991eb-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="991eb-112">Si consideri ad `IDataAccess` esempio un'interfaccia per l'accesso ai dati nell'app.</span><span class="sxs-lookup"><span data-stu-id="991eb-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="991eb-113">L'interfaccia viene implementata da una classe concreta `DataAccess` e registrata come servizio nel contenitore dei servizi dell'app.</span><span class="sxs-lookup"><span data-stu-id="991eb-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="991eb-114">Quando un componente utilizza `IDataAccess` DI per ricevere un'implementazione, il componente non viene associato al tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="991eb-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="991eb-115">L'implementazione può essere scambiata, ad esempio per un'implementazione fittizia negli unit test.</span><span class="sxs-lookup"><span data-stu-id="991eb-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="991eb-116">Servizi predefiniti</span><span class="sxs-lookup"><span data-stu-id="991eb-116">Default services</span></span>

<span data-ttu-id="991eb-117">I servizi predefiniti vengono aggiunti automaticamente alla raccolta di servizi dell'app.</span><span class="sxs-lookup"><span data-stu-id="991eb-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="991eb-118">Service</span><span class="sxs-lookup"><span data-stu-id="991eb-118">Service</span></span> | <span data-ttu-id="991eb-119">Durata</span><span class="sxs-lookup"><span data-stu-id="991eb-119">Lifetime</span></span> | <span data-ttu-id="991eb-120">Descrizione</span><span class="sxs-lookup"><span data-stu-id="991eb-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="991eb-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="991eb-121">Singleton</span></span> | <span data-ttu-id="991eb-122">Fornisce metodi per l'invio di richieste HTTP e la ricezione di risposte HTTP da una risorsa identificata da un URI.</span><span class="sxs-lookup"><span data-stu-id="991eb-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="991eb-123">L'istanza `HttpClient` di in un'app Blazor WebAssembly utilizza il browser per la gestione del traffico HTTP in background.</span><span class="sxs-lookup"><span data-stu-id="991eb-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="991eb-124">Le app del server Blazor non includono una `HttpClient` configurazione come servizio per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="991eb-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="991eb-125">Fornire `HttpClient` un'app Blazor Server.Provide an to a Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="991eb-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="991eb-126">Per altre informazioni, vedere <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="991eb-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="991eb-127">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="991eb-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="991eb-128">Ambito (server Blazor)</span><span class="sxs-lookup"><span data-stu-id="991eb-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="991eb-129">Rappresenta un'istanza di un runtime JavaScript in cui vengono inviate chiamate JavaScript.</span><span class="sxs-lookup"><span data-stu-id="991eb-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="991eb-130">Per altre informazioni, vedere <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="991eb-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| `NavigationManager` | <span data-ttu-id="991eb-131">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="991eb-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="991eb-132">Ambito (server Blazor)</span><span class="sxs-lookup"><span data-stu-id="991eb-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="991eb-133">Contiene helper per l'utilizzo con URI e stato di spostamento.</span><span class="sxs-lookup"><span data-stu-id="991eb-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="991eb-134">Per ulteriori informazioni, vedere [URI e helper dello stato](xref:blazor/routing#uri-and-navigation-state-helpers)di navigazione .</span><span class="sxs-lookup"><span data-stu-id="991eb-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="991eb-135">Un provider di servizi personalizzato non fornisce automaticamente i servizi predefiniti elencati nella tabella.</span><span class="sxs-lookup"><span data-stu-id="991eb-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="991eb-136">Se si utilizza un provider di servizi personalizzato e si richiede uno dei servizi illustrati nella tabella, aggiungere i servizi necessari al nuovo provider di servizi.</span><span class="sxs-lookup"><span data-stu-id="991eb-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="991eb-137">Aggiungere servizi a un'app</span><span class="sxs-lookup"><span data-stu-id="991eb-137">Add services to an app</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="991eb-138">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="991eb-138">Blazor WebAssembly</span></span>

<span data-ttu-id="991eb-139">Configurare i servizi per la `Main` raccolta di servizi dell'app nel metodo di *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="991eb-139">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="991eb-140">Nell'esempio seguente, `MyDependency` l'implementazione viene registrata per `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="991eb-140">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="991eb-141">Una volta compilato l'host, è possibile accedere ai servizi dall'ambito DI radice prima del rendering di qualsiasi componente.</span><span class="sxs-lookup"><span data-stu-id="991eb-141">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="991eb-142">Ciò può essere utile per l'esecuzione della logica di inizializzazione prima del rendering del contenuto:This can be useful for running initialization logic before rendering content:</span><span class="sxs-lookup"><span data-stu-id="991eb-142">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="991eb-143">L'host fornisce anche un'istanza di configurazione centrale per l'app.</span><span class="sxs-lookup"><span data-stu-id="991eb-143">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="991eb-144">Basandosi sull'esempio precedente, l'URL del servizio meteo viene passato da un'origine `InitializeWeatherAsync`di configurazione predefinita (ad esempio, *appsettings.json*) a :</span><span class="sxs-lookup"><span data-stu-id="991eb-144">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

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

### <a name="blazor-server"></a><span data-ttu-id="991eb-145">Server Blazor</span><span class="sxs-lookup"><span data-stu-id="991eb-145">Blazor Server</span></span>

<span data-ttu-id="991eb-146">Dopo aver creato una `Startup.ConfigureServices` nuova app, esamina il metodo:</span><span class="sxs-lookup"><span data-stu-id="991eb-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="991eb-147">Al `ConfigureServices` metodo viene <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>passato un oggetto , che<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>è un elenco di oggetti descrittore del servizio ( ).</span><span class="sxs-lookup"><span data-stu-id="991eb-147">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="991eb-148">I servizi vengono aggiunti fornendo i descrittori dei servizi alla raccolta di servizi.</span><span class="sxs-lookup"><span data-stu-id="991eb-148">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="991eb-149">Nell'esempio seguente viene `IDataAccess` illustrato il concetto con l'interfaccia e la relativa implementazione `DataAccess`concreta :</span><span class="sxs-lookup"><span data-stu-id="991eb-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="991eb-150">Durata del servizio</span><span class="sxs-lookup"><span data-stu-id="991eb-150">Service lifetime</span></span>

<span data-ttu-id="991eb-151">I servizi possono essere configurati con le durate illustrate nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="991eb-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="991eb-152">Durata</span><span class="sxs-lookup"><span data-stu-id="991eb-152">Lifetime</span></span> | <span data-ttu-id="991eb-153">Descrizione</span><span class="sxs-lookup"><span data-stu-id="991eb-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor<span data-ttu-id="991eb-154">Le app WebAssembly non hanno attualmente un concetto di ambiti DI.</span><span class="sxs-lookup"><span data-stu-id="991eb-154"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="991eb-155">`Scoped`I servizi registrati `Singleton` si comportano come servizi.</span><span class="sxs-lookup"><span data-stu-id="991eb-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="991eb-156">Tuttavia, Blazor il modello di `Scoped` hosting Server supporta la durata.</span><span class="sxs-lookup"><span data-stu-id="991eb-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="991eb-157">Nelle Blazor app server, l'ambito della registrazione di un servizio con ambito ha come ambito la *connessione.*</span><span class="sxs-lookup"><span data-stu-id="991eb-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="991eb-158">Per questo motivo, l'utilizzo di servizi con ambito è preferibile per i servizi che devono essere nell'ambito dell'utente corrente, anche se la finalità corrente consiste nell'eseguire il lato client nel browser.</span><span class="sxs-lookup"><span data-stu-id="991eb-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="991eb-159">DI crea una *singola istanza* del servizio.</span><span class="sxs-lookup"><span data-stu-id="991eb-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="991eb-160">Tutti i `Singleton` componenti che richiedono un servizio ricevono un'istanza dello stesso servizio.</span><span class="sxs-lookup"><span data-stu-id="991eb-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="991eb-161">Ogni volta che un componente `Transient` ottiene un'istanza di un servizio dal contenitore dei servizi, riceve una *nuova istanza* del servizio.</span><span class="sxs-lookup"><span data-stu-id="991eb-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="991eb-162">Il sistema DI si basa sul sistema DI in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="991eb-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="991eb-163">Per altre informazioni, vedere <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="991eb-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="991eb-164">Richiedere un servizio in un componenteRequest a service in a component</span><span class="sxs-lookup"><span data-stu-id="991eb-164">Request a service in a component</span></span>

<span data-ttu-id="991eb-165">Dopo aver aggiunto i servizi alla raccolta di servizi, inserire i servizi nei componenti usando la direttiva inject Razor.After [ \@services](xref:mvc/views/razor#inject) are added to the service collection, inject the services into the components using the inject Razor directive.</span><span class="sxs-lookup"><span data-stu-id="991eb-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="991eb-166">`@inject`ha due parametri:</span><span class="sxs-lookup"><span data-stu-id="991eb-166">`@inject` has two parameters:</span></span>

* <span data-ttu-id="991eb-167">Digitare &ndash; il tipo di servizio da iniettare.</span><span class="sxs-lookup"><span data-stu-id="991eb-167">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="991eb-168">Proprietà &ndash; Il nome della proprietà che riceve il servizio app inserito.</span><span class="sxs-lookup"><span data-stu-id="991eb-168">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="991eb-169">La proprietà non richiede la creazione manuale.</span><span class="sxs-lookup"><span data-stu-id="991eb-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="991eb-170">Il compilatore crea la proprietà .</span><span class="sxs-lookup"><span data-stu-id="991eb-170">The compiler creates the property.</span></span>

<span data-ttu-id="991eb-171">Per altre informazioni, vedere <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="991eb-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="991eb-172">Utilizzare `@inject` più istruzioni per inserire servizi diversi.</span><span class="sxs-lookup"><span data-stu-id="991eb-172">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="991eb-173">Nell'esempio riportato di seguito viene illustrato come usare `@inject`.</span><span class="sxs-lookup"><span data-stu-id="991eb-173">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="991eb-174">L'implementazione `Services.IDataAccess` del servizio viene inserita nella proprietà `DataRepository`del componente.</span><span class="sxs-lookup"><span data-stu-id="991eb-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="991eb-175">Si noti come il `IDataAccess` codice utilizza solo l'astrazione:Note how the code is only using the abstraction:</span><span class="sxs-lookup"><span data-stu-id="991eb-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="991eb-176">Internamente, la proprietà`DataRepository`generata `InjectAttribute` ( ) utilizza l'attributo .</span><span class="sxs-lookup"><span data-stu-id="991eb-176">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="991eb-177">In genere, questo attributo non viene utilizzato direttamente.</span><span class="sxs-lookup"><span data-stu-id="991eb-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="991eb-178">Se è necessaria una classe base per i componenti e sono necessarie `InjectAttribute`anche proprietà inserite per la classe base, aggiungere manualmente il metodo :</span><span class="sxs-lookup"><span data-stu-id="991eb-178">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="991eb-179">Nei componenti derivati dalla `@inject` classe base, la direttiva non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="991eb-179">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="991eb-180">La `InjectAttribute` classe base è sufficiente:</span><span class="sxs-lookup"><span data-stu-id="991eb-180">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="991eb-181">Usare DI nei serviziUse DI in services</span><span class="sxs-lookup"><span data-stu-id="991eb-181">Use DI in services</span></span>

<span data-ttu-id="991eb-182">Servizi complessi potrebbero richiedere servizi aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="991eb-182">Complex services might require additional services.</span></span> <span data-ttu-id="991eb-183">Nell'esempio precedente, `DataAccess` potrebbe `HttpClient` essere necessario il servizio predefinito.</span><span class="sxs-lookup"><span data-stu-id="991eb-183">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="991eb-184">`@inject`(o `InjectAttribute`il ) non è disponibile per l'utilizzo nei servizi.</span><span class="sxs-lookup"><span data-stu-id="991eb-184">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="991eb-185">È invece necessario *utilizzare l'inserimento del costruttore.*</span><span class="sxs-lookup"><span data-stu-id="991eb-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="991eb-186">I servizi necessari vengono aggiunti aggiungendo parametri al costruttore del servizio.</span><span class="sxs-lookup"><span data-stu-id="991eb-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="991eb-187">Quando DI crea il servizio, riconosce i servizi necessari nel costruttore e li fornisce di conseguenza.</span><span class="sxs-lookup"><span data-stu-id="991eb-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="991eb-188">Prerequisiti per l'inserimento del costruttore:Prerequisites for constructor injection:</span><span class="sxs-lookup"><span data-stu-id="991eb-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="991eb-189">Deve esistere un costruttore i cui argomenti possono essere tutti soddisfatti da DI.</span><span class="sxs-lookup"><span data-stu-id="991eb-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="991eb-190">Se specificano valori predefiniti, sono consentiti parametri aggiuntivi non coperti da DI.</span><span class="sxs-lookup"><span data-stu-id="991eb-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="991eb-191">Il costruttore applicabile deve essere *public*.</span><span class="sxs-lookup"><span data-stu-id="991eb-191">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="991eb-192">Deve esistere un costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="991eb-192">One applicable constructor must exist.</span></span> <span data-ttu-id="991eb-193">In caso di ambiguità, DI genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="991eb-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="991eb-194">Classi di componenti di base dell'utilità per gestire un ambito DI</span><span class="sxs-lookup"><span data-stu-id="991eb-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="991eb-195">Nelle app ASP.NET Core, i servizi con ambito hanno in genere ambito la richiesta corrente.</span><span class="sxs-lookup"><span data-stu-id="991eb-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="991eb-196">Al termine della richiesta, tutti i servizi con ambito o temporanei vengono eliminati dal sistema DI.</span><span class="sxs-lookup"><span data-stu-id="991eb-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="991eb-197">Nelle Blazor app server, l'ambito della richiesta dura per la durata della connessione client, il che può comportare servizi temporanei e con ambito molto più lungo del previsto.</span><span class="sxs-lookup"><span data-stu-id="991eb-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="991eb-198">Nelle Blazor app WebAssembly, i servizi registrati con una durata con ambito vengono considerati singleton, pertanto risiedono più a lungo dei servizi con ambito nelle tipiche app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="991eb-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="991eb-199">Un approccio che limita Blazor la durata `OwningComponentBase` di un servizio nelle app è l'uso del tipo.</span><span class="sxs-lookup"><span data-stu-id="991eb-199">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="991eb-200">`OwningComponentBase`è un tipo `ComponentBase` astratto derivato da che crea un ambito DI corrispondente alla durata del componente.</span><span class="sxs-lookup"><span data-stu-id="991eb-200">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="991eb-201">Utilizzando questo ambito, è possibile utilizzare i servizi DI con una durata con ambito e farli vivere fino a quando il componente.</span><span class="sxs-lookup"><span data-stu-id="991eb-201">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="991eb-202">Quando il componente viene eliminato, vengono eliminati anche i servizi del provider di servizi con ambito del componente.</span><span class="sxs-lookup"><span data-stu-id="991eb-202">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="991eb-203">Ciò può essere utile per i servizi che:</span><span class="sxs-lookup"><span data-stu-id="991eb-203">This can be useful for services that:</span></span>

* <span data-ttu-id="991eb-204">Deve essere riutilizzato all'interno di un componente, poiché la durata transitoria è inappropriata.</span><span class="sxs-lookup"><span data-stu-id="991eb-204">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="991eb-205">Non deve essere condiviso tra i componenti, poiché la durata del singolo è inappropriata.</span><span class="sxs-lookup"><span data-stu-id="991eb-205">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="991eb-206">Sono disponibili `OwningComponentBase` due versioni del tipo:</span><span class="sxs-lookup"><span data-stu-id="991eb-206">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="991eb-207">`OwningComponentBase`è un figlio astratto `ComponentBase` e usa `ScopedServices` e `IServiceProvider`getta del tipo con una proprietà protetta di tipo .</span><span class="sxs-lookup"><span data-stu-id="991eb-207">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="991eb-208">Questo provider può essere utilizzato per risolvere i servizi che hanno come ambito la durata del componente.</span><span class="sxs-lookup"><span data-stu-id="991eb-208">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="991eb-209">I servizi DI inseriti `@inject` nel `InjectAttribute` `[Inject]`componente utilizzando o il ( ) non vengono creati nell'ambito del componente.</span><span class="sxs-lookup"><span data-stu-id="991eb-209">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="991eb-210">Per utilizzare l'ambito del componente, `ScopedServices.GetRequiredService` i `ScopedServices.GetService`servizi devono essere risolti utilizzando o .</span><span class="sxs-lookup"><span data-stu-id="991eb-210">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="991eb-211">Tutti i servizi `ScopedServices` risolti tramite il provider hanno le dipendenze fornite dallo stesso ambito.</span><span class="sxs-lookup"><span data-stu-id="991eb-211">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="991eb-212">`OwningComponentBase<T>`deriva da `OwningComponentBase` e aggiunge `Service` una proprietà `T` che restituisce un'istanza di dal provider DI con ambito.</span><span class="sxs-lookup"><span data-stu-id="991eb-212">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="991eb-213">Questo tipo è un modo pratico per accedere `IServiceProvider` ai servizi con ambito senza usare un'istanza di quando è presente un servizio primario richiesto dall'app dal contenitore DI usando l'ambito del componente.</span><span class="sxs-lookup"><span data-stu-id="991eb-213">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="991eb-214">La `ScopedServices` proprietà è disponibile, in modo che l'app possa ottenere servizi di altri tipi, se necessario.</span><span class="sxs-lookup"><span data-stu-id="991eb-214">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="991eb-215">Utilizzo di Entity Framework DbContext da DI</span><span class="sxs-lookup"><span data-stu-id="991eb-215">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="991eb-216">Un tipo di servizio comune da recuperare da DI `DbContext` nelle applicazioni web è Entity Framework (EF) oggetti.</span><span class="sxs-lookup"><span data-stu-id="991eb-216">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="991eb-217">Registrazione di servizi `IServiceCollection.AddDbContext` di `DbContext` EF utilizzando aggiunge il come un servizio con ambito per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="991eb-217">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="991eb-218">La registrazione come servizio con ambito Blazor può causare `DbContext` problemi nelle app perché causa istanze di lunga durata e condivise nell'app.</span><span class="sxs-lookup"><span data-stu-id="991eb-218">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="991eb-219">`DbContext`non è thread-safe e non deve essere utilizzato contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="991eb-219">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="991eb-220">A seconda dell'app, `OwningComponentBase` l'utilizzo `DbContext` di un componente può *risolvere* il problema.</span><span class="sxs-lookup"><span data-stu-id="991eb-220">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="991eb-221">Se un componente non `DbContext` utilizza un in parallelo, `OwningComponentBase` derivare `DbContext` il `ScopedServices` componente da e recuperare il da è sufficiente perché garantisce che:If a component doesn't use a in parallel, deriving the component from and retrieving the from is sufficient because it ensures that:</span><span class="sxs-lookup"><span data-stu-id="991eb-221">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="991eb-222">Componenti separati non condividono un `DbContext`file .</span><span class="sxs-lookup"><span data-stu-id="991eb-222">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="991eb-223">Le `DbContext` vite durano solo fino a quando il componente dipende da esso.</span><span class="sxs-lookup"><span data-stu-id="991eb-223">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="991eb-224">Se un singolo componente `DbContext` potrebbe utilizzare un contemporaneamente (ad esempio, ogni `OwningComponentBase` volta che un utente seleziona un pulsante), anche l'utilizzo non consente di evitare problemi con le operazioni simultanee di EF.</span><span class="sxs-lookup"><span data-stu-id="991eb-224">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="991eb-225">In tal caso, `DbContext` utilizzare un diverso per ogni operazione di EF logico.</span><span class="sxs-lookup"><span data-stu-id="991eb-225">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="991eb-226">Utilizzare uno dei seguenti approcci:</span><span class="sxs-lookup"><span data-stu-id="991eb-226">Use either of the following approaches:</span></span>

* <span data-ttu-id="991eb-227">Creare `DbContext` il `DbContextOptions<TContext>` direttamente utilizzando come argomento, che può essere recuperato da DI ed è thread-safe.</span><span class="sxs-lookup"><span data-stu-id="991eb-227">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
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

* <span data-ttu-id="991eb-228">Registrare `DbContext` l'oggetto nel contenitore dei servizi con una durata temporanea:Register the in the service container with a transient lifetime:</span><span class="sxs-lookup"><span data-stu-id="991eb-228">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="991eb-229">Quando si registra il `ServiceLifetime.Transient`contesto, utilizzare .</span><span class="sxs-lookup"><span data-stu-id="991eb-229">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="991eb-230">Il `AddDbContext` metodo di estensione accetta `ServiceLifetime`due parametri facoltativi di tipo .</span><span class="sxs-lookup"><span data-stu-id="991eb-230">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="991eb-231">Per utilizzare questo approccio, solo il `contextLifetime` parametro deve essere `ServiceLifetime.Transient`.</span><span class="sxs-lookup"><span data-stu-id="991eb-231">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="991eb-232">`optionsLifetime`è possibile mantenere `ServiceLifetime.Scoped`il valore predefinito di .</span><span class="sxs-lookup"><span data-stu-id="991eb-232">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="991eb-233">Il transitorio `DbContext` può essere inserito `@inject`come di consueto (utilizzando ) in componenti che non eseguirà più operazioni di EF in parallelo.</span><span class="sxs-lookup"><span data-stu-id="991eb-233">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="991eb-234">Quelli che possono eseguire più operazioni di `DbContext` FFi contemporaneamente `IServiceProvider.GetRequiredService`possono richiedere oggetti separati per ogni operazione parallela utilizzando .</span><span class="sxs-lookup"><span data-stu-id="991eb-234">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
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

## <a name="additional-resources"></a><span data-ttu-id="991eb-235">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="991eb-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
