---
title: BlazorInserimento di dipendenze ASP.NET Core
author: guardrex
description: Informazioni su come le Blazor app possono inserire i servizi nei componenti.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: c68deb5237754872e11bfd9c83275b9a3b147319
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556515"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="a9a5d-103">BlazorInserimento di dipendenze ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a9a5d-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="a9a5d-104">Di [Rainer Stropek](https://www.timecockpit.com) e [Mike entusiasmanti](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="a9a5d-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="a9a5d-105">Blazor supporta l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a9a5d-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a9a5d-106">Le app possono usare i servizi predefiniti inserendoli in componenti.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="a9a5d-107">Le app possono anche definire e registrare servizi personalizzati e renderli disponibili nell'app tramite DI.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="a9a5d-108">DI è una tecnica per accedere ai servizi configurati in una posizione centrale.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="a9a5d-109">Questa operazione può essere utile nelle Blazor app per:</span><span class="sxs-lookup"><span data-stu-id="a9a5d-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="a9a5d-110">Condividere una singola istanza di una classe di servizio in molti componenti, noti come un servizio *singleton* .</span><span class="sxs-lookup"><span data-stu-id="a9a5d-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="a9a5d-111">Separare i componenti da classi di servizi concrete usando astrazioni di riferimento.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="a9a5d-112">Si consideri ad esempio un'interfaccia `IDataAccess` per l'accesso ai dati nell'app.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="a9a5d-113">L'interfaccia viene implementata da una `DataAccess` classe concreta e registrata come servizio nel contenitore del servizio dell'app.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="a9a5d-114">Quando un componente usa il per ricevere un' `IDataAccess` implementazione di, il componente non è associato al tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="a9a5d-115">L'implementazione può essere scambiata, ad esempio per un'implementazione fittizia negli unit test.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="a9a5d-116">Servizi predefiniti</span><span class="sxs-lookup"><span data-stu-id="a9a5d-116">Default services</span></span>

<span data-ttu-id="a9a5d-117">I servizi predefiniti vengono aggiunti automaticamente alla raccolta di servizi dell'app.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="a9a5d-118">Servizio</span><span class="sxs-lookup"><span data-stu-id="a9a5d-118">Service</span></span> | <span data-ttu-id="a9a5d-119">Durata</span><span class="sxs-lookup"><span data-stu-id="a9a5d-119">Lifetime</span></span> | <span data-ttu-id="a9a5d-120">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a9a5d-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="a9a5d-121">Con ambito</span><span class="sxs-lookup"><span data-stu-id="a9a5d-121">Scoped</span></span> | <span data-ttu-id="a9a5d-122">Fornisce metodi per l'invio di richieste HTTP e la ricezione di risposte HTTP da una risorsa identificata da un URI.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="a9a5d-123">L'istanza di <xref:System.Net.Http.HttpClient> in un' Blazor WebAssembly applicazione utilizza il browser per gestire il traffico HTTP in background.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-123">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="a9a5d-124">Blazor Server per impostazione predefinita, le app non includono un <xref:System.Net.Http.HttpClient> configurato come servizio.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-124">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="a9a5d-125">Fornire un <xref:System.Net.Http.HttpClient> a un' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-125">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="a9a5d-126">Per altre informazioni, vedere <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-126">For more information, see <xref:blazor/call-web-api>.</span></span><br><br><span data-ttu-id="a9a5d-127">Un <xref:System.Net.Http.HttpClient> viene registrato come servizio con ambito, non come singleton.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-127">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="a9a5d-128">Per ulteriori informazioni, vedere la sezione [durata del servizio](#service-lifetime) .</span><span class="sxs-lookup"><span data-stu-id="a9a5d-128">For more information, see the [Service lifetime](#service-lifetime) section.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="a9a5d-129">Singleton ( Blazor WebAssembly )</span><span class="sxs-lookup"><span data-stu-id="a9a5d-129">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="a9a5d-130">Con ambito ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="a9a5d-130">Scoped (Blazor Server)</span></span> | <span data-ttu-id="a9a5d-131">Rappresenta un'istanza di un runtime JavaScript in cui vengono inviate le chiamate a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-131">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="a9a5d-132">Per altre informazioni, vedere <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-132">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="a9a5d-133">Singleton ( Blazor WebAssembly )</span><span class="sxs-lookup"><span data-stu-id="a9a5d-133">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="a9a5d-134">Con ambito ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="a9a5d-134">Scoped (Blazor Server)</span></span> | <span data-ttu-id="a9a5d-135">Contiene gli helper per lavorare con gli URI e lo stato di navigazione.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-135">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="a9a5d-136">Per ulteriori informazioni, vedere [URI e Helper dello stato di navigazione](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="a9a5d-136">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="a9a5d-137">Un provider di servizi personalizzato non fornisce automaticamente i servizi predefiniti elencati nella tabella.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-137">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="a9a5d-138">Se si usa un provider di servizi personalizzato e si richiede uno dei servizi indicati nella tabella, aggiungere i servizi necessari al nuovo provider di servizi.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-138">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="a9a5d-139">Aggiungere servizi a un'app</span><span class="sxs-lookup"><span data-stu-id="a9a5d-139">Add services to an app</span></span>

### Blazor WebAssembly

<span data-ttu-id="a9a5d-140">Configurare i servizi per la raccolta di servizi dell'app nel `Main` metodo di `Program.cs` .</span><span class="sxs-lookup"><span data-stu-id="a9a5d-140">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="a9a5d-141">Nell'esempio seguente l' `MyDependency` implementazione è registrata per `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="a9a5d-141">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using Microsoft.Extensions.DependencyInjection;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<IMyDependency, MyDependency>();

        ...

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="a9a5d-142">Una volta compilato l'host, è possibile accedere ai servizi dall'ambito radice prima DI eseguire il rendering di tutti i componenti.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-142">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="a9a5d-143">Questa operazione può essere utile per eseguire la logica di inizializzazione prima del rendering del contenuto:</span><span class="sxs-lookup"><span data-stu-id="a9a5d-143">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<WeatherService>();

        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="a9a5d-144">L'host fornisce anche un'istanza di configurazione centrale per l'app.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-144">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="a9a5d-145">Basandosi sull'esempio precedente, l'URL del servizio meteo viene passato da un'origine di configurazione predefinita (ad esempio, `appsettings.json` ) a `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="a9a5d-145">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<WeatherService>();

        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### Blazor Server

<span data-ttu-id="a9a5d-146">Dopo aver creato una nuova app, esaminare il `Startup.ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="a9a5d-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="a9a5d-147">Al <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> metodo viene passato un oggetto <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , che è un elenco di oggetti del descrittore del servizio ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ).</span><span class="sxs-lookup"><span data-stu-id="a9a5d-147">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="a9a5d-148">I servizi vengono aggiunti nel `ConfigureServices` Metodo fornendo descrittori del servizio alla raccolta di servizi.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-148">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="a9a5d-149">Nell'esempio seguente viene illustrato il concetto con l' `IDataAccess` interfaccia e la relativa implementazione concreta `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="a9a5d-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="a9a5d-150">Durata del servizio</span><span class="sxs-lookup"><span data-stu-id="a9a5d-150">Service lifetime</span></span>

<span data-ttu-id="a9a5d-151">I servizi possono essere configurati con le durate mostrate nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="a9a5d-152">Durata</span><span class="sxs-lookup"><span data-stu-id="a9a5d-152">Lifetime</span></span> | <span data-ttu-id="a9a5d-153">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a9a5d-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p><span data-ttu-id="a9a5d-154">Blazor WebAssembly Attualmente le app non hanno un concetto di ambiti di.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-154">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="a9a5d-155">`Scoped`-i servizi registrati si comportano come `Singleton` servizi.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-155">`Scoped`-registered services behave like `Singleton` services.</span></span></p><p><span data-ttu-id="a9a5d-156">Il Blazor Server modello di hosting supporta la `Scoped` durata tra le richieste HTTP, ma non tra i messaggi di connessione/circuito SingalR tra i componenti caricati nel client.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-156">The Blazor Server hosting model supports the `Scoped` lifetime across HTTP requests but not across SingalR connection/circuit messages among components that are loaded on the client.</span></span> <span data-ttu-id="a9a5d-157">Le Razor pagine o la parte MVC dell'app considera i servizi con ambito normalmente e ricrea i servizi in *ogni richiesta http* quando si naviga tra pagine o viste oppure da una pagina o da una vista a un componente.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-157">The Razor Pages or MVC portion of the app treats scoped services normally and recreates the services on *each HTTP request* when navigating among pages or views or from a page or view to a component.</span></span> <span data-ttu-id="a9a5d-158">I servizi con ambito non vengono ricostruiti quando ci si sposta tra i componenti nel client, dove la comunicazione con il server avviene tramite la SignalR connessione del circuito dell'utente, non tramite richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-158">Scoped services aren't reconstructed when navigating among components on the client, where the communication to the server takes place over the SignalR connection of the user's circuit, not via HTTP requests.</span></span> <span data-ttu-id="a9a5d-159">Negli scenari dei componenti seguenti nel client, i servizi con ambito vengono ricostruiti perché viene creato un nuovo circuito per l'utente:</span><span class="sxs-lookup"><span data-stu-id="a9a5d-159">In the following component scenarios on the client, scoped services are reconstructed because a new circuit is created for the user:</span></span></p><ul><li><span data-ttu-id="a9a5d-160">L'utente chiude la finestra del browser.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-160">The user closes the browser's window.</span></span> <span data-ttu-id="a9a5d-161">L'utente apre una nuova finestra e torna all'app.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-161">The user opens a new window and navigates back to the app.</span></span></li><li><span data-ttu-id="a9a5d-162">L'utente chiude l'ultima scheda dell'app in una finestra del browser.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-162">The user closes the last tab of the app in a browser window.</span></span> <span data-ttu-id="a9a5d-163">L'utente apre una nuova scheda e torna all'app.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-163">The user opens a new tab and navigates back to the app.</span></span></li><li><span data-ttu-id="a9a5d-164">L'utente seleziona il pulsante ricarica/Aggiorna del browser.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-164">The user selects the browser's reload/refresh button.</span></span></li></ul><p><span data-ttu-id="a9a5d-165">Per ulteriori informazioni sul mantenimento dello stato utente tra i servizi con ambito nelle Blazor Server app, vedere <xref:blazor/hosting-models?pivots=server> .</span><span class="sxs-lookup"><span data-stu-id="a9a5d-165">For more information on preserving user state across scoped services in Blazor Server apps, see <xref:blazor/hosting-models?pivots=server>.</span></span></p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="a9a5d-166">La creazione di una *singola istanza* del servizio.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-166">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="a9a5d-167">Tutti i componenti che richiedono un `Singleton` servizio ricevono un'istanza dello stesso servizio.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-167">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="a9a5d-168">Ogni volta che un componente ottiene un'istanza di un `Transient` servizio dal contenitore del servizio, riceve una *nuova istanza* del servizio.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-168">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="a9a5d-169">Il sistema DI è basato sul sistema DI ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-169">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="a9a5d-170">Per altre informazioni, vedere <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-170">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="a9a5d-171">Richiedere un servizio in un componente</span><span class="sxs-lookup"><span data-stu-id="a9a5d-171">Request a service in a component</span></span>

<span data-ttu-id="a9a5d-172">Una volta aggiunti i servizi alla raccolta di servizi, inserire i servizi nei componenti utilizzando la direttiva [ \@ Inject](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="a9a5d-172">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="a9a5d-173">[`@inject`](xref:mvc/views/razor#inject) dispone di due parametri:</span><span class="sxs-lookup"><span data-stu-id="a9a5d-173">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="a9a5d-174">Tipo: tipo di servizio da inserire.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-174">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="a9a5d-175">Property: nome della proprietà che riceve il servizio app inserito.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-175">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="a9a5d-176">La proprietà non richiede la creazione manuale.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-176">The property doesn't require manual creation.</span></span> <span data-ttu-id="a9a5d-177">Il compilatore crea la proprietà.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-177">The compiler creates the property.</span></span>

<span data-ttu-id="a9a5d-178">Per altre informazioni, vedere <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-178">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="a9a5d-179">Usare più [`@inject`](xref:mvc/views/razor#inject) istruzioni per inserire servizi diversi.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-179">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="a9a5d-180">Nell'esempio seguente viene illustrato come utilizzare [`@inject`](xref:mvc/views/razor#inject) .</span><span class="sxs-lookup"><span data-stu-id="a9a5d-180">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="a9a5d-181">Il servizio che implementa `Services.IDataAccess` viene inserito nella proprietà del componente `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="a9a5d-181">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="a9a5d-182">Si noti come il codice usa solo l' `IDataAccess` astrazione:</span><span class="sxs-lookup"><span data-stu-id="a9a5d-182">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="a9a5d-183">Internamente, la proprietà generata ( `DataRepository` ) utilizza l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-183">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="a9a5d-184">In genere, questo attributo non viene utilizzato direttamente.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-184">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="a9a5d-185">Se è necessaria una classe base per i componenti e le proprietà inserite sono necessarie anche per la classe base, aggiungere manualmente l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attributo:</span><span class="sxs-lookup"><span data-stu-id="a9a5d-185">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="a9a5d-186">Nei componenti derivati dalla classe di base, la [`@inject`](xref:mvc/views/razor#inject) direttiva non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-186">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="a9a5d-187">La <xref:Microsoft.AspNetCore.Components.InjectAttribute> classe della classe base è sufficiente:</span><span class="sxs-lookup"><span data-stu-id="a9a5d-187">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="a9a5d-188">Usare l'inserimento DI dipendenze nei servizi</span><span class="sxs-lookup"><span data-stu-id="a9a5d-188">Use DI in services</span></span>

<span data-ttu-id="a9a5d-189">Servizi complessi potrebbe richiedere servizi aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-189">Complex services might require additional services.</span></span> <span data-ttu-id="a9a5d-190">Nell'esempio precedente, `DataAccess` potrebbe richiedere il <xref:System.Net.Http.HttpClient> servizio predefinito.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-190">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="a9a5d-191">[`@inject`](xref:mvc/views/razor#inject) (o l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attributo) non è disponibile per l'uso nei servizi.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-191">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="a9a5d-192">È necessario usare invece l' *inserimento del costruttore* .</span><span class="sxs-lookup"><span data-stu-id="a9a5d-192">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="a9a5d-193">I servizi necessari vengono aggiunti aggiungendo parametri al costruttore del servizio.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-193">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="a9a5d-194">Quando si crea il servizio, vengono riconosciuti i servizi richiesti nel costruttore e forniti DI conseguenza.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-194">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="a9a5d-195">Nell'esempio seguente, il costruttore riceve una <xref:System.Net.Http.HttpClient> via di.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-195">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="a9a5d-196"><xref:System.Net.Http.HttpClient> è un servizio predefinito.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-196"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="a9a5d-197">Prerequisiti per l'inserimento del costruttore:</span><span class="sxs-lookup"><span data-stu-id="a9a5d-197">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="a9a5d-198">È necessario che esista un costruttore i cui argomenti possono essere tutti soddisfatti da DI.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-198">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="a9a5d-199">Sono consentiti parametri aggiuntivi non analizzati da DI se specificano i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-199">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="a9a5d-200">Il costruttore applicabile deve essere `public` .</span><span class="sxs-lookup"><span data-stu-id="a9a5d-200">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="a9a5d-201">È necessario che esista un costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-201">One applicable constructor must exist.</span></span> <span data-ttu-id="a9a5d-202">In caso di ambiguità, viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-202">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="a9a5d-203">Classi di componenti di base dell'utilità per gestire un ambito DI</span><span class="sxs-lookup"><span data-stu-id="a9a5d-203">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="a9a5d-204">Nelle app ASP.NET Core, i servizi con ambito hanno in genere come ambito la richiesta corrente.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-204">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="a9a5d-205">Al termine della richiesta, tutti i servizi con ambito o temporaneo vengono eliminati dal sistema DI.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-205">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="a9a5d-206">Nelle Blazor Server app, l'ambito della richiesta dura per la durata della connessione client, che può comportare la permanenza di servizi temporanei e con ambito più a lungo del previsto.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-206">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="a9a5d-207">Nelle Blazor WebAssembly app i servizi registrati con una durata con ambito vengono considerati come singleton, quindi vivono più a lungo rispetto ai servizi con ambito nelle app ASP.NET Core tipiche.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-207">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="a9a5d-208">Per rilevare servizi temporanei monouso in un'app, vedere la sezione [rilevare gli eliminabili temporanei](#detect-transient-disposables) .</span><span class="sxs-lookup"><span data-stu-id="a9a5d-208">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="a9a5d-209">Un approccio che limita la durata di un servizio nelle Blazor app è l'uso del <xref:Microsoft.AspNetCore.Components.OwningComponentBase> tipo.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-209">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="a9a5d-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> è un tipo astratto derivato da <xref:Microsoft.AspNetCore.Components.ComponentBase> che consente di creare un ambito di che corrisponde alla durata del componente.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="a9a5d-211">Con questo ambito, è possibile usare i servizi DI i con una durata con ambito e fare in modo che siano attivi fino a quando il componente.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-211">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="a9a5d-212">Quando il componente viene eliminato definitivamente, vengono eliminati anche i servizi del provider di servizi con ambito.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-212">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="a9a5d-213">Questa operazione può essere utile per i servizi che:</span><span class="sxs-lookup"><span data-stu-id="a9a5d-213">This can be useful for services that:</span></span>

* <span data-ttu-id="a9a5d-214">È necessario riutilizzarlo all'interno di un componente, perché la durata temporanea non è appropriata.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-214">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="a9a5d-215">Non devono essere condivise tra i componenti, perché la durata singleton non è appropriata.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-215">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="a9a5d-216">Sono disponibili due versioni del <xref:Microsoft.AspNetCore.Components.OwningComponentBase> tipo:</span><span class="sxs-lookup"><span data-stu-id="a9a5d-216">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="a9a5d-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> è un elemento figlio astratto e disposable del <xref:Microsoft.AspNetCore.Components.ComponentBase> tipo con una <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> proprietà protetta di tipo <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="a9a5d-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="a9a5d-218">Questo provider può essere utilizzato per risolvere i servizi che hanno come ambito la durata del componente.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-218">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="a9a5d-219">I servizi DI inserimento nel componente usando [`@inject`](xref:mvc/views/razor#inject) o l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attributo non vengono creati nell'ambito del componente.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-219">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="a9a5d-220">Per utilizzare l'ambito del componente, i servizi devono essere risolti utilizzando <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> o <xref:System.IServiceProvider.GetService%2A> .</span><span class="sxs-lookup"><span data-stu-id="a9a5d-220">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="a9a5d-221">Tutti i servizi risolti utilizzando il <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider hanno le dipendenze fornite dallo stesso ambito.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-221">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="a9a5d-222"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> deriva da <xref:Microsoft.AspNetCore.Components.OwningComponentBase> e aggiunge una <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> proprietà che restituisce un'istanza di `T` dal provider dell'ambito di.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-222"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="a9a5d-223">Questo tipo è un modo pratico per accedere ai servizi con ambito senza usare un'istanza di <xref:System.IServiceProvider> quando è presente un servizio primario richiesto dall'app dal contenitore di inserimento delle dipendenze usando l'ambito del componente.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-223">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="a9a5d-224">La <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> proprietà è disponibile, in modo che l'app possa ottenere i servizi di altri tipi, se necessario.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-224">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="a9a5d-225">Uso di un Entity Framework Core (EF Core) DbContext da DI</span><span class="sxs-lookup"><span data-stu-id="a9a5d-225">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="a9a5d-226">Per altre informazioni, vedere <xref:blazor/blazor-server-ef-core>.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-226">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="a9a5d-227">Rilevare eliminabili temporanei</span><span class="sxs-lookup"><span data-stu-id="a9a5d-227">Detect transient disposables</span></span>

<span data-ttu-id="a9a5d-228">Gli esempi seguenti illustrano come rilevare servizi temporanei monouso in un'app che deve usare <xref:Microsoft.AspNetCore.Components.OwningComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="a9a5d-228">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="a9a5d-229">Per ulteriori informazioni, vedere la sezione relativa [alle classi dei componenti di base dell'utilità per gestire una](#utility-base-component-classes-to-manage-a-di-scope) sezione dell'ambito.</span><span class="sxs-lookup"><span data-stu-id="a9a5d-229">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

### Blazor WebAssembly

<span data-ttu-id="a9a5d-230">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="a9a5d-230">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="a9a5d-231">Nell' `TransientDisposable` esempio seguente viene rilevato ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="a9a5d-231">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

### Blazor Server

<span data-ttu-id="a9a5d-232">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="a9a5d-232">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="a9a5d-233">`Program`:</span><span class="sxs-lookup"><span data-stu-id="a9a5d-233">`Program`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

<span data-ttu-id="a9a5d-234">Nell' `TransientDependency` esempio seguente viene rilevato ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="a9a5d-234">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a><span data-ttu-id="a9a5d-235">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a9a5d-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="a9a5d-236">`IDisposable` linee guida per le istanze temporanee e condivise</span><span class="sxs-lookup"><span data-stu-id="a9a5d-236">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
