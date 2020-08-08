---
title: Host generico .NET
author: rick-anderson
description: Informazioni sull'host generico .NET Core, che gestisce l'avvio e la durata delle app.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/generic-host
ms.openlocfilehash: 7f662aac3714e49e6a3a63175415a1a63b0940c2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017428"
---
# <a name="net-generic-host"></a><span data-ttu-id="3bc47-103">Host generico .NET</span><span class="sxs-lookup"><span data-stu-id="3bc47-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="3bc47-104">I modelli di ASP.NET Core creano un host generico .NET Core, <xref:Microsoft.Extensions.Hosting.HostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="3bc47-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="3bc47-105">Definizione host</span><span class="sxs-lookup"><span data-stu-id="3bc47-105">Host definition</span></span>

<span data-ttu-id="3bc47-106">Un *host* è un oggetto che incapsula le risorse di un'app, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="3bc47-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="3bc47-107">Inserimento di dipendenze (DI)</span><span class="sxs-lookup"><span data-stu-id="3bc47-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="3bc47-108">Registrazione</span><span class="sxs-lookup"><span data-stu-id="3bc47-108">Logging</span></span>
* <span data-ttu-id="3bc47-109">Configurazione</span><span class="sxs-lookup"><span data-stu-id="3bc47-109">Configuration</span></span>
* <span data-ttu-id="3bc47-110">`IHostedService` Implementazioni</span><span class="sxs-lookup"><span data-stu-id="3bc47-110">`IHostedService` implementations</span></span>

<span data-ttu-id="3bc47-111">Quando viene avviato, un host chiama <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> su ogni implementazione di <xref:Microsoft.Extensions.Hosting.IHostedService> registrata nella raccolta di servizi ospitati del contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="3bc47-111">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="3bc47-112">In un'app Web, una delle implementazioni di `IHostedService` è un servizio web che avvia un'[implementazione del server HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="3bc47-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="3bc47-113">Il motivo principale per cui tutte le risorse interdipendenti dell'app sono incluse in un unico oggetto è la gestione del ciclo di vita, vale a dire il controllo sull'avvio dell'app e sull'arresto normale.</span><span class="sxs-lookup"><span data-stu-id="3bc47-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="3bc47-114">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="3bc47-114">Set up a host</span></span>

<span data-ttu-id="3bc47-115">L'host è in genere configurato, compilato ed eseguito da codice nella classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="3bc47-116">Il metodo `Main`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-116">The `Main` method:</span></span>

* <span data-ttu-id="3bc47-117">Chiama un metodo `CreateHostBuilder` per creare e configurare un oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="3bc47-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="3bc47-118">Chiamate `Build` e metodi `Run` nell'oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="3bc47-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="3bc47-119">I modelli Web ASP.NET Core generano il codice seguente per creare un host generico:</span><span class="sxs-lookup"><span data-stu-id="3bc47-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="3bc47-120">Il codice seguente crea un host generico utilizzando un carico di lavoro non HTTP.</span><span class="sxs-lookup"><span data-stu-id="3bc47-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="3bc47-121">L' `IHostedService` implementazione viene aggiunta al contenitore DI di:</span><span class="sxs-lookup"><span data-stu-id="3bc47-121">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="3bc47-122">Per un carico di lavoro HTTP, il metodo `Main` è lo stesso ma `CreateHostBuilder` chiama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="3bc47-123">Il codice precedente viene generato dai modelli di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3bc47-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="3bc47-124">Se l'app usa Entity Framework Core, non modificare il nome o la firma del metodo `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="3bc47-125">Gli [strumenti di Entity Framework Core](/ef/core/miscellaneous/cli/) si aspettano di trovare un metodo `CreateHostBuilder` che configura l'host senza eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="3bc47-126">Per altre informazioni, vedere [Creazione DbContext in fase di progettazione](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="3bc47-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="3bc47-127">Impostazioni predefinite del generatore</span><span class="sxs-lookup"><span data-stu-id="3bc47-127">Default builder settings</span></span>

<span data-ttu-id="3bc47-128">Il metodo <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="3bc47-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="3bc47-129">Imposta la [radice del contenuto](xref:fundamentals/index#content-root) sul percorso restituito da <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="3bc47-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="3bc47-130">Carica la configurazione dell'host da:</span><span class="sxs-lookup"><span data-stu-id="3bc47-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="3bc47-131">Variabili di ambiente con prefisso `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="3bc47-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="3bc47-132">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="3bc47-132">Command-line arguments.</span></span>
* <span data-ttu-id="3bc47-133">Carica la configurazione dell'app da:</span><span class="sxs-lookup"><span data-stu-id="3bc47-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="3bc47-134">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="3bc47-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="3bc47-135">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="3bc47-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="3bc47-136">[Segreti del manager](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="3bc47-137">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="3bc47-137">Environment variables.</span></span>
  * <span data-ttu-id="3bc47-138">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="3bc47-138">Command-line arguments.</span></span>
* <span data-ttu-id="3bc47-139">Aggiunge i provider di [log](xref:fundamentals/logging/index) seguenti:</span><span class="sxs-lookup"><span data-stu-id="3bc47-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="3bc47-140">Console</span><span class="sxs-lookup"><span data-stu-id="3bc47-140">Console</span></span>
  * <span data-ttu-id="3bc47-141">Debug</span><span class="sxs-lookup"><span data-stu-id="3bc47-141">Debug</span></span>
  * <span data-ttu-id="3bc47-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="3bc47-142">EventSource</span></span>
  * <span data-ttu-id="3bc47-143">EventLog (solo quando è in esecuzione su Windows)</span><span class="sxs-lookup"><span data-stu-id="3bc47-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="3bc47-144">Abilita la [convalida dell'ambito](xref:fundamentals/dependency-injection#scope-validation) e la [convalida delle dipendenze](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando l'ambiente è lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="3bc47-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="3bc47-145">Il metodo `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="3bc47-146">Carica la configurazione host dalle variabili di ambiente con prefisso `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="3bc47-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="3bc47-147">Imposta il server [Kestrel](xref:fundamentals/servers/kestrel) come server Web e lo configura usando i provider di configurazione dell'host dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="3bc47-148">Per le opzioni predefinite del server Kestrel, vedere <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="3bc47-149">Aggiunge [il middleware dell'applicazione di filtri dell'host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="3bc47-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="3bc47-150">Aggiunge il [middleware delle intestazioni con inoltri](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è uguale a `true` .</span><span class="sxs-lookup"><span data-stu-id="3bc47-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="3bc47-151">Abilita l'integrazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="3bc47-151">Enables IIS integration.</span></span> <span data-ttu-id="3bc47-152">Per le opzioni predefinite di IIS, vedere <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="3bc47-153">Le sezioni [Impostazioni per tutti i tipi di app](#settings-for-all-app-types) e [Impostazioni per le app Web](#settings-for-web-apps) più avanti in questo articolo mostrano come eseguire l'override delle impostazioni predefinite del generatore.</span><span class="sxs-lookup"><span data-stu-id="3bc47-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="3bc47-154">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="3bc47-154">Framework-provided services</span></span>

<span data-ttu-id="3bc47-155">I servizi seguenti vengono registrati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="3bc47-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="3bc47-156">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="3bc47-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="3bc47-157">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="3bc47-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="3bc47-158">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="3bc47-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="3bc47-159">Per ulteriori informazioni sui servizi forniti dal Framework, vedere <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="3bc47-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="3bc47-160">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="3bc47-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="3bc47-161">Inserire il servizio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (in precedenza `IApplicationLifetime`) in qualsiasi classe per gestire le attività post-avvio e di arresto normale.</span><span class="sxs-lookup"><span data-stu-id="3bc47-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="3bc47-162">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi del gestore dell'evento di avvio e di arresto.</span><span class="sxs-lookup"><span data-stu-id="3bc47-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="3bc47-163">L'interfaccia include anche un metodo `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="3bc47-164">L'esempio seguente è un' `IHostedService` implementazione di che registra `IHostApplicationLifetime` gli eventi:</span><span class="sxs-lookup"><span data-stu-id="3bc47-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="3bc47-165">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="3bc47-165">IHostLifetime</span></span>

<span data-ttu-id="3bc47-166">L'implementazione <xref:Microsoft.Extensions.Hosting.IHostLifetime> controlla quando l'host viene avviato e quando si arresta.</span><span class="sxs-lookup"><span data-stu-id="3bc47-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="3bc47-167">Viene usata l'ultima implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="3bc47-167">The last implementation registered is used.</span></span>

<span data-ttu-id="3bc47-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` è l'implementazione `IHostLifetime` predefinita.</span><span class="sxs-lookup"><span data-stu-id="3bc47-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="3bc47-169">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="3bc47-170">Ascolta la <kbd>combinazione di tasti CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM e chiama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> per avviare il processo di arresto.</span><span class="sxs-lookup"><span data-stu-id="3bc47-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="3bc47-171">Sblocca estensioni come [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="3bc47-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="3bc47-172">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="3bc47-172">IHostEnvironment</span></span>

<span data-ttu-id="3bc47-173">Inserire il <xref:Microsoft.Extensions.Hosting.IHostEnvironment> servizio in una classe per ottenere informazioni sulle impostazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3bc47-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="3bc47-174">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="3bc47-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="3bc47-175">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="3bc47-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="3bc47-176">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="3bc47-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="3bc47-177">Le app Web implementano l' `IWebHostEnvironment` interfaccia, che eredita `IHostEnvironment` e aggiunge [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="3bc47-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="3bc47-178">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="3bc47-178">Host configuration</span></span>

<span data-ttu-id="3bc47-179">La configurazione dell'host viene usata per le proprietà dell'implementazione <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="3bc47-180">La configurazione dell'host è disponibile da [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) all'interno di <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="3bc47-181">Dopo `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` viene sostituito con la configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="3bc47-182">Per aggiungere la configurazione dell'host, chiamare <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="3bc47-183">`ConfigureHostConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="3bc47-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="3bc47-184">L'host usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="3bc47-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="3bc47-185">Il provider di variabili di ambiente con prefisso `DOTNET_` e argomenti della riga di comando sono inclusi in `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3bc47-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="3bc47-186">Per le app Web, viene aggiunto il provider di variabili di ambiente con prefisso `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="3bc47-187">Il prefisso viene rimosso quando vengono lette le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="3bc47-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="3bc47-188">Ad esempio, il valore della variabile di ambiente per `ASPNETCORE_ENVIRONMENT` diventa il valore di configurazione dell'host per la chiave `environment`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="3bc47-189">L'esempio seguente crea la configurazione host:</span><span class="sxs-lookup"><span data-stu-id="3bc47-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="3bc47-190">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="3bc47-190">App configuration</span></span>

<span data-ttu-id="3bc47-191">La configurazione dell'app viene creata chiamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="3bc47-192">`ConfigureAppConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="3bc47-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="3bc47-193">L'app usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="3bc47-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="3bc47-194">La configurazione creata da `ConfigureAppConfiguration` è disponibile in [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) per le operazioni successive e come servizio da DI.</span><span class="sxs-lookup"><span data-stu-id="3bc47-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="3bc47-195">La configurazione dell'host viene aggiunta anche alla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="3bc47-196">Per altre informazioni, vedere [Configurazione in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="3bc47-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="3bc47-197">Impostazioni per tutti i tipi di app</span><span class="sxs-lookup"><span data-stu-id="3bc47-197">Settings for all app types</span></span>

<span data-ttu-id="3bc47-198">Questa sezione elenca le impostazioni dell'host che si applicano ai carichi di lavoro HTTP e non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="3bc47-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="3bc47-199">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="3bc47-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="3bc47-200">ApplicationName</span></span>

<span data-ttu-id="3bc47-201">La proprietà [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) viene impostata dalla configurazione dell'host durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="3bc47-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="3bc47-202">**Chiave**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="3bc47-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="3bc47-203">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-203">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-204">**Impostazione predefinita**: il nome dell'assembly che contiene il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="3bc47-205">**Variabile di ambiente**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="3bc47-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="3bc47-206">per impostare questo valore usare la variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="3bc47-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="3bc47-207">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="3bc47-207">ContentRoot</span></span>

<span data-ttu-id="3bc47-208">La proprietà [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina il punto in cui l'host inizia a cercare i file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="3bc47-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="3bc47-209">Se il percorso non esiste, l'host non verrà avviato.</span><span class="sxs-lookup"><span data-stu-id="3bc47-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="3bc47-210">**Chiave**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="3bc47-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="3bc47-211">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-211">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-212">**Impostazione predefinita**: la cartella in cui risiede l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="3bc47-213">**Variabile di ambiente**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="3bc47-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="3bc47-214">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseContentRoot` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="3bc47-215">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="3bc47-215">For more information, see:</span></span>

* [<span data-ttu-id="3bc47-216">Nozioni fondamentali: radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="3bc47-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="3bc47-217">WebRoot</span><span class="sxs-lookup"><span data-stu-id="3bc47-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="3bc47-218">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="3bc47-218">EnvironmentName</span></span>

<span data-ttu-id="3bc47-219">La proprietà [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) può essere impostata su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="3bc47-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="3bc47-220">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="3bc47-221">I valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="3bc47-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="3bc47-222">**Chiave**:`environment`</span><span class="sxs-lookup"><span data-stu-id="3bc47-222">**Key**: `environment`</span></span>  
<span data-ttu-id="3bc47-223">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-223">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-224">**Impostazione predefinita**:`Production`</span><span class="sxs-lookup"><span data-stu-id="3bc47-224">**Default**: `Production`</span></span>  
<span data-ttu-id="3bc47-225">**Variabile di ambiente**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="3bc47-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="3bc47-226">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseEnvironment` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="3bc47-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="3bc47-227">ShutdownTimeout</span></span>

<span data-ttu-id="3bc47-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) imposta il timeout per <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="3bc47-229">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="3bc47-229">The default value is five seconds.</span></span>  <span data-ttu-id="3bc47-230">Durante il periodo di timeout, l'host:</span><span class="sxs-lookup"><span data-stu-id="3bc47-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="3bc47-231">attiva [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="3bc47-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="3bc47-232">Tenta di arrestare i servizi ospitati, registrando gli errori per i servizi che non si interrompono.</span><span class="sxs-lookup"><span data-stu-id="3bc47-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="3bc47-233">Se il periodo di timeout scade prima che siano stati arrestati tutti i servizi ospitati, gli eventuali servizi attivi rimanenti vengono interrotti quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="3bc47-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="3bc47-234">I servizi si arrestano anche se non hanno completato l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="3bc47-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="3bc47-235">Se l'arresto dei servizi richiede più tempo, aumentare il valore di timeout.</span><span class="sxs-lookup"><span data-stu-id="3bc47-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="3bc47-236">**Chiave**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="3bc47-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="3bc47-237">**Tipo**:`int`</span><span class="sxs-lookup"><span data-stu-id="3bc47-237">**Type**: `int`</span></span>  
<span data-ttu-id="3bc47-238">**Impostazione predefinita**: 5 secondi</span><span class="sxs-lookup"><span data-stu-id="3bc47-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="3bc47-239">**Variabile di ambiente**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="3bc47-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="3bc47-240">Per impostare questo valore, usare la variabile di ambiente o configurare `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="3bc47-241">Nell'esempio seguente il timeout viene impostato su 20 secondi:</span><span class="sxs-lookup"><span data-stu-id="3bc47-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="3bc47-242">Impostazioni per le app Web</span><span class="sxs-lookup"><span data-stu-id="3bc47-242">Settings for web apps</span></span>

<span data-ttu-id="3bc47-243">Alcune impostazioni host si applicano solo ai carichi di lavoro HTTP.</span><span class="sxs-lookup"><span data-stu-id="3bc47-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="3bc47-244">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="3bc47-245">Per queste impostazioni sono disponibili i metodi di estensione su `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="3bc47-246">Gli esempi di codice che illustrano come chiamare i metodi di estensione presumono che `webBuilder` sia un'istanza di `IWebHostBuilder`, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="3bc47-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="3bc47-247">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="3bc47-247">CaptureStartupErrors</span></span>

<span data-ttu-id="3bc47-248">Quando è `false`, gli errori durante l'avvio causano la chiusura dell'host.</span><span class="sxs-lookup"><span data-stu-id="3bc47-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="3bc47-249">Quando è `true`, l'host acquisisce le eccezioni durante l'avvio e tenta di avviare il server.</span><span class="sxs-lookup"><span data-stu-id="3bc47-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="3bc47-250">**Chiave**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="3bc47-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="3bc47-251">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="3bc47-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3bc47-252">**Impostazione predefinita**: il valore predefinito è `false` a meno che l'app non venga eseguita con Kestrel in IIS, in tal caso il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="3bc47-253">**Variabile di ambiente**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="3bc47-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="3bc47-254">Per impostare questo valore, usare la configurazione o chiamare `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="3bc47-255">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="3bc47-255">DetailedErrors</span></span>

<span data-ttu-id="3bc47-256">Quando è abilitata o quando l'ambiente è `Development`, l'app acquisisce gli errori dettagliati.</span><span class="sxs-lookup"><span data-stu-id="3bc47-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="3bc47-257">**Chiave**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="3bc47-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="3bc47-258">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="3bc47-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3bc47-259">**Impostazione predefinita**:`false`</span><span class="sxs-lookup"><span data-stu-id="3bc47-259">**Default**: `false`</span></span>  
<span data-ttu-id="3bc47-260">**Variabile di ambiente**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="3bc47-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="3bc47-261">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="3bc47-262">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="3bc47-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="3bc47-263">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da caricare all'avvio.</span><span class="sxs-lookup"><span data-stu-id="3bc47-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="3bc47-264">Sebbene il valore di configurazione predefinito sia una stringa vuota, gli assembly di avvio dell'hosting includono sempre l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="3bc47-265">Quando vengono specificati, gli assembly di avvio dell'hosting vengono aggiunti all'assembly dell'app per essere caricati quando l'app compila i servizi comuni durante l'avvio.</span><span class="sxs-lookup"><span data-stu-id="3bc47-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="3bc47-266">**Chiave**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="3bc47-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="3bc47-267">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-267">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-268">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="3bc47-268">**Default**: Empty string</span></span>  
<span data-ttu-id="3bc47-269">**Variabile di ambiente**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="3bc47-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="3bc47-270">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="3bc47-271">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="3bc47-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="3bc47-272">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da escludere all'avvio.</span><span class="sxs-lookup"><span data-stu-id="3bc47-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="3bc47-273">**Chiave**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="3bc47-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="3bc47-274">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-274">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-275">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="3bc47-275">**Default**: Empty string</span></span>  
<span data-ttu-id="3bc47-276">**Variabile di ambiente**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="3bc47-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="3bc47-277">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="3bc47-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="3bc47-278">HTTPS_Port</span></span>

<span data-ttu-id="3bc47-279">Porta di reindirizzamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3bc47-279">The HTTPS redirect port.</span></span> <span data-ttu-id="3bc47-280">Usata per [imporre HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="3bc47-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="3bc47-281">**Chiave**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="3bc47-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="3bc47-282">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-282">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-283">**Impostazione predefinita**: un valore predefinito non è impostato.</span><span class="sxs-lookup"><span data-stu-id="3bc47-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="3bc47-284">**Variabile di ambiente**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="3bc47-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="3bc47-285">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="3bc47-286">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="3bc47-286">PreferHostingUrls</span></span>

<span data-ttu-id="3bc47-287">Indica se l'host deve essere in ascolto sugli URL configurati con `IWebHostBuilder` anziché gli URL configurati con l' `IServer` implementazione di.</span><span class="sxs-lookup"><span data-stu-id="3bc47-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="3bc47-288">**Chiave**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="3bc47-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="3bc47-289">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="3bc47-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3bc47-290">**Impostazione predefinita**:`true`</span><span class="sxs-lookup"><span data-stu-id="3bc47-290">**Default**: `true`</span></span>  
<span data-ttu-id="3bc47-291">**Variabile di ambiente**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="3bc47-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="3bc47-292">Per impostare questo valore, usare la variabile di ambiente o chiamare `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="3bc47-293">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="3bc47-293">PreventHostingStartup</span></span>

<span data-ttu-id="3bc47-294">Impedisce il caricamento automatico degli assembly di avvio dell'hosting, inclusi gli assembly di avvio dell'hosting configurati dall'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="3bc47-295">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="3bc47-296">**Chiave**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="3bc47-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="3bc47-297">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="3bc47-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3bc47-298">**Impostazione predefinita**:`false`</span><span class="sxs-lookup"><span data-stu-id="3bc47-298">**Default**: `false`</span></span>  
<span data-ttu-id="3bc47-299">**Variabile di ambiente**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="3bc47-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="3bc47-300">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="3bc47-301">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="3bc47-301">StartupAssembly</span></span>

<span data-ttu-id="3bc47-302">L'assembly per la ricerca della classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="3bc47-303">**Chiave**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="3bc47-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="3bc47-304">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-304">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-305">**Impostazione predefinita**: assembly dell'app</span><span class="sxs-lookup"><span data-stu-id="3bc47-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="3bc47-306">**Variabile di ambiente**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="3bc47-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="3bc47-307">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="3bc47-308">`UseStartup` può richiedere un nome di assembly (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="3bc47-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="3bc47-309">Se vengono chiamati più metodi `UseStartup`, l'ultimo metodo ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="3bc47-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="3bc47-310">URL</span><span class="sxs-lookup"><span data-stu-id="3bc47-310">URLs</span></span>

<span data-ttu-id="3bc47-311">Elenco delimitato da punto e virgola degli indirizzi IP o gli indirizzi host con le porte e protocolli su cui il server deve eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="3bc47-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="3bc47-312">Ad esempio, `http://localhost:123`</span><span class="sxs-lookup"><span data-stu-id="3bc47-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="3bc47-313">Usare "\*" per indicare che il server deve eseguire l'ascolto per le richieste su tutti gli indirizzi IP o nomi host usando la porta e il protocollo specificati (ad esempio, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="3bc47-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="3bc47-314">Il protocollo (`http://` o `https://`) deve essere incluso con ogni URL.</span><span class="sxs-lookup"><span data-stu-id="3bc47-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="3bc47-315">I formati supportati variano a seconda del server.</span><span class="sxs-lookup"><span data-stu-id="3bc47-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="3bc47-316">**Chiave**:`urls`</span><span class="sxs-lookup"><span data-stu-id="3bc47-316">**Key**: `urls`</span></span>  
<span data-ttu-id="3bc47-317">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-317">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-318">**Impostazione predefinita**: `http://localhost:5000` e`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="3bc47-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="3bc47-319">**Variabile di ambiente**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="3bc47-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="3bc47-320">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="3bc47-321">Kestrel ha una propria API di configurazione degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="3bc47-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="3bc47-322">Per altre informazioni, vedere <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="3bc47-323">WebRoot</span><span class="sxs-lookup"><span data-stu-id="3bc47-323">WebRoot</span></span>

<span data-ttu-id="3bc47-324">La proprietà [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina il percorso relativo degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="3bc47-325">Se il percorso non esiste, viene usato un provider di file no-op.</span><span class="sxs-lookup"><span data-stu-id="3bc47-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="3bc47-326">**Chiave**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="3bc47-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="3bc47-327">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-327">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-328">**Impostazione predefinita**: il valore predefinito è `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="3bc47-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="3bc47-329">Il percorso di *{radice del contenuto}/wwwroot* deve esistere.</span><span class="sxs-lookup"><span data-stu-id="3bc47-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="3bc47-330">**Variabile di ambiente**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="3bc47-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="3bc47-331">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseWebRoot` su `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="3bc47-332">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="3bc47-332">For more information, see:</span></span>

* [<span data-ttu-id="3bc47-333">Nozioni fondamentali: radice Web</span><span class="sxs-lookup"><span data-stu-id="3bc47-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="3bc47-334">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="3bc47-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="3bc47-335">Gestire la durata dell'host</span><span class="sxs-lookup"><span data-stu-id="3bc47-335">Manage the host lifetime</span></span>

<span data-ttu-id="3bc47-336">Chiamare metodi sull'implementazione <xref:Microsoft.Extensions.Hosting.IHost> incorporata per avviare e arrestare l'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="3bc47-337">Questi metodi influiscono su tutte le implementazioni <xref:Microsoft.Extensions.Hosting.IHostedService> che vengono registrate nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="3bc47-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="3bc47-338">Esegui</span><span class="sxs-lookup"><span data-stu-id="3bc47-338">Run</span></span>

<span data-ttu-id="3bc47-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> esegue l'app e blocca il thread di chiamata fino all'arresto dell'host.</span><span class="sxs-lookup"><span data-stu-id="3bc47-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="3bc47-340">RunAsync</span><span class="sxs-lookup"><span data-stu-id="3bc47-340">RunAsync</span></span>

<span data-ttu-id="3bc47-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> esegue l'app e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="3bc47-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="3bc47-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="3bc47-342">RunConsoleAsync</span></span>

<span data-ttu-id="3bc47-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Abilita il supporto della console, compila e avvia l'host e attende la <kbd>Ctrl</kbd> + chiusura di CTRL<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="3bc47-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="3bc47-344">Avvio</span><span class="sxs-lookup"><span data-stu-id="3bc47-344">Start</span></span>

<span data-ttu-id="3bc47-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> avvia l'host in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="3bc47-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="3bc47-346">StartAsync</span><span class="sxs-lookup"><span data-stu-id="3bc47-346">StartAsync</span></span>

<span data-ttu-id="3bc47-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> avvia l'host e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="3bc47-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="3bc47-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> viene chiamato all'avvio di `StartAsync`, che attende il completamento prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="3bc47-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="3bc47-349">Questo è utile per ritardare l'avvio fino al segnale trasmesso da un evento esterno.</span><span class="sxs-lookup"><span data-stu-id="3bc47-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="3bc47-350">StopAsync</span><span class="sxs-lookup"><span data-stu-id="3bc47-350">StopAsync</span></span>

<span data-ttu-id="3bc47-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> prova ad arrestare l'host entro il timeout specificato.</span><span class="sxs-lookup"><span data-stu-id="3bc47-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="3bc47-352">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="3bc47-352">WaitForShutdown</span></span>

<span data-ttu-id="3bc47-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blocca il thread chiamante finché l'arresto non viene attivato da IHostLifetime, ad esempio tramite <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="3bc47-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="3bc47-354">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="3bc47-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="3bc47-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato l'arresto tramite il token specificato, quindi chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="3bc47-356">Controllo esterno</span><span class="sxs-lookup"><span data-stu-id="3bc47-356">External control</span></span>

<span data-ttu-id="3bc47-357">Il controllo diretto della durata dell'host può essere ottenuto usando metodi che possono essere chiamati dall'esterno:</span><span class="sxs-lookup"><span data-stu-id="3bc47-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3bc47-358">Le app ASP.NET Core configurano e avviano un host.</span><span class="sxs-lookup"><span data-stu-id="3bc47-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="3bc47-359">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="3bc47-360">Questo articolo illustra l'host generico di ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), che si usa per le app che non elaborano le richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="3bc47-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="3bc47-361">Lo scopo dell'host generico è separare la pipeline HTTP dall'API dell'host Web, per abilitare una gamma più ampia di scenari host.</span><span class="sxs-lookup"><span data-stu-id="3bc47-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="3bc47-362">La messaggistica, le attività in background e altri carichi di lavoro non HTTP basati sull'host generico traggono vantaggio dalle funzionalità a montaggio incrociato come la configurazione, l'inserimento di dipendenze (DI) e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="3bc47-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="3bc47-363">L'host generico è una nuova funzionalità introdotta in ASP.NET Core 2.1 e non è adatto agli scenari di hosting Web.</span><span class="sxs-lookup"><span data-stu-id="3bc47-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="3bc47-364">Per gli scenari di hosting Web usare l'[host Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="3bc47-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="3bc47-365">L'host generico sostituirà l'host Web in una versione futura, funzionando come API host principale negli scenari sia HTTP che non HTTP.</span><span class="sxs-lookup"><span data-stu-id="3bc47-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="3bc47-366">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3bc47-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3bc47-367">Quando si esegue l'app di esempio in [Visual Studio Code](https://code.visualstudio.com/), usare un *terminale esterno o integrato*.</span><span class="sxs-lookup"><span data-stu-id="3bc47-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="3bc47-368">Non eseguire l'esempio in un ambiente `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="3bc47-369">Per impostare la console in Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="3bc47-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="3bc47-370">Aprire il file *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="3bc47-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="3bc47-371">Nella configurazione **.NET Core Launch (console)** trovare la voce **console**.</span><span class="sxs-lookup"><span data-stu-id="3bc47-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="3bc47-372">Impostare il valore su `externalTerminal` o `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="3bc47-373">Introduzione</span><span class="sxs-lookup"><span data-stu-id="3bc47-373">Introduction</span></span>

<span data-ttu-id="3bc47-374">La libreria host generico è disponibile nello spazio dei nomi <xref:Microsoft.Extensions.Hosting> e viene specificata dal pacchetto [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="3bc47-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="3bc47-375">Il pacchetto `Microsoft.Extensions.Hosting` è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 o versioni successive).</span><span class="sxs-lookup"><span data-stu-id="3bc47-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="3bc47-376"><xref:Microsoft.Extensions.Hosting.IHostedService> è il punto di ingresso per l'esecuzione del codice.</span><span class="sxs-lookup"><span data-stu-id="3bc47-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="3bc47-377">Ogni implementazione <xref:Microsoft.Extensions.Hosting.IHostedService> viene eseguita nell'ordine di [registrazione del servizio in ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="3bc47-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="3bc47-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> viene chiamato su ogni <xref:Microsoft.Extensions.Hosting.IHostedService> quando viene avviato l'host e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> viene chiamato in ordine di registrazione inverso quando l'host viene chiuso normalmente.</span><span class="sxs-lookup"><span data-stu-id="3bc47-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="3bc47-379">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="3bc47-379">Set up a host</span></span>

<span data-ttu-id="3bc47-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> è il componente principale che le librerie e le app usano per inizializzare, compilare ed eseguire l'host:</span><span class="sxs-lookup"><span data-stu-id="3bc47-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="3bc47-381">Opzioni</span><span class="sxs-lookup"><span data-stu-id="3bc47-381">Options</span></span>

<span data-ttu-id="3bc47-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configura le opzioni per <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="3bc47-383">Timeout di arresto</span><span class="sxs-lookup"><span data-stu-id="3bc47-383">Shutdown timeout</span></span>

<span data-ttu-id="3bc47-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> imposta il timeout per <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="3bc47-385">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="3bc47-385">The default value is five seconds.</span></span>

<span data-ttu-id="3bc47-386">La seguente configurazione di opzione in `Program.Main` aumenta il timeout predefinito di cinque secondi di chiusura a 20 secondi:</span><span class="sxs-lookup"><span data-stu-id="3bc47-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="3bc47-387">Servizi predefiniti</span><span class="sxs-lookup"><span data-stu-id="3bc47-387">Default services</span></span>

<span data-ttu-id="3bc47-388">I servizi seguenti vengono registrati durante l'inizializzazione dell'host:</span><span class="sxs-lookup"><span data-stu-id="3bc47-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="3bc47-389">[Ambiente](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="3bc47-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="3bc47-390">[Configurazione](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="3bc47-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="3bc47-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="3bc47-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="3bc47-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="3bc47-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="3bc47-393">[Opzioni](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="3bc47-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="3bc47-394">[Registrazione](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="3bc47-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="3bc47-395">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="3bc47-395">Host configuration</span></span>

<span data-ttu-id="3bc47-396">La configurazione dell'host viene creata tramite:</span><span class="sxs-lookup"><span data-stu-id="3bc47-396">Host configuration is created by:</span></span>

* <span data-ttu-id="3bc47-397">Chiamata ai metodi di estensione su <xref:Microsoft.Extensions.Hosting.IHostBuilder> per impostare la [radice del contenuto](#content-root) e l'[ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="3bc47-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="3bc47-398">Lettura della configurazione dai provider di configurazione in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="3bc47-399">Metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="3bc47-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="3bc47-400">Chiave applicazione (nome)</span><span class="sxs-lookup"><span data-stu-id="3bc47-400">Application key (name)</span></span>

<span data-ttu-id="3bc47-401">La proprietà [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) viene impostata dalla configurazione dell'host durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="3bc47-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="3bc47-402">Per impostare il valore in modo esplicito, usare [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="3bc47-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="3bc47-403">**Chiave**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="3bc47-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="3bc47-404">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-404">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-405">**Impostazione predefinita**: il nome dell'assembly contenente il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="3bc47-406">**Imposta utilizzando**:`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="3bc47-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="3bc47-407">**Variabile di ambiente**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` è [facoltativo e definito dall'utente](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="3bc47-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="3bc47-408">Radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="3bc47-408">Content root</span></span>

<span data-ttu-id="3bc47-409">Questa impostazione determina la posizione da cui l'host inizia la ricerca dei file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="3bc47-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="3bc47-410">**Chiave**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="3bc47-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="3bc47-411">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-411">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-412">**Impostazione predefinita**: il valore predefinito corrisponde alla cartella contenente l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="3bc47-413">**Imposta utilizzando**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="3bc47-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="3bc47-414">**Variabile di ambiente**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` è [facoltativo e definito dall'utente](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="3bc47-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="3bc47-415">Se il percorso non esiste, l'host non verrà avviato.</span><span class="sxs-lookup"><span data-stu-id="3bc47-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="3bc47-416">Per altre informazioni, vedere [nozioni fondamentali: radice del contenuto](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="3bc47-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="3bc47-417">Environment</span><span class="sxs-lookup"><span data-stu-id="3bc47-417">Environment</span></span>

<span data-ttu-id="3bc47-418">Imposta l' [ambiente](xref:fundamentals/environments)dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="3bc47-419">**Chiave**:`environment`</span><span class="sxs-lookup"><span data-stu-id="3bc47-419">**Key**: `environment`</span></span>  
<span data-ttu-id="3bc47-420">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-420">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-421">**Impostazione predefinita**:`Production`</span><span class="sxs-lookup"><span data-stu-id="3bc47-421">**Default**: `Production`</span></span>  
<span data-ttu-id="3bc47-422">**Imposta utilizzando**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="3bc47-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="3bc47-423">**Variabile di ambiente**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` è [facoltativo e definito dall'utente](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="3bc47-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="3bc47-424">L'ambiente può essere impostato su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="3bc47-424">The environment can be set to any value.</span></span> <span data-ttu-id="3bc47-425">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="3bc47-426">I valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="3bc47-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="3bc47-427">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="3bc47-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="3bc47-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per creare un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfiguration> per l'host.</span><span class="sxs-lookup"><span data-stu-id="3bc47-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="3bc47-429">La configurazione dell'host viene usata per inizializzare l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> da usare nel processo di compilazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="3bc47-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="3bc47-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="3bc47-431">L'host usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="3bc47-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="3bc47-432">Nessun provider è incluso per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="3bc47-432">No providers are included by default.</span></span> <span data-ttu-id="3bc47-433">È necessario specificare in modo esplicito tutti i provider di configurazione richiesti dall'app in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, inclusi:</span><span class="sxs-lookup"><span data-stu-id="3bc47-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="3bc47-434">Configurazione dei file (ad esempio, da un file *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="3bc47-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="3bc47-435">Configurazione delle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="3bc47-435">Environment variable configuration.</span></span>
* <span data-ttu-id="3bc47-436">Configurazione degli argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="3bc47-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="3bc47-437">Tutti gli altri provider di configurazione necessari.</span><span class="sxs-lookup"><span data-stu-id="3bc47-437">Any other required configuration providers.</span></span>

<span data-ttu-id="3bc47-438">La configurazione file dell'host viene abilitata specificando il percorso di base dell'app con `SetBasePath` seguito da una chiamata a uno dei [provider di configurazione dei file](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="3bc47-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="3bc47-439">L'app di esempio usa un file JSON, *hostsettings.json*, e chiama <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> per utilizzare le impostazioni di configurazione host del file.</span><span class="sxs-lookup"><span data-stu-id="3bc47-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="3bc47-440">Per aggiungere la [configurazione delle variabili di ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) dell'host, chiamare <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sul generatore di host.</span><span class="sxs-lookup"><span data-stu-id="3bc47-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="3bc47-441">`AddEnvironmentVariables` accetta un prefisso facoltativo definito dall'utente.</span><span class="sxs-lookup"><span data-stu-id="3bc47-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="3bc47-442">L'app di esempio usa un prefisso di `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="3bc47-443">Il prefisso viene rimosso quando vengono lette le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="3bc47-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="3bc47-444">Quando viene configurato l'host dell'app di esempio, il valore della variabile di ambiente per `PREFIX_ENVIRONMENT` diventa il valore di configurazione dell'host per la chiave `environment`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="3bc47-445">Durante lo sviluppo quando si usa [Visual Studio](https://visualstudio.microsoft.com) o si esegue un'app con `dotnet run`, le variabili di ambiente possono essere impostate nel file *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3bc47-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="3bc47-446">Durante lo sviluppo in [Visual Studio Code](https://code.visualstudio.com/), le variabili di ambiente possono essere impostate nel file *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="3bc47-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="3bc47-447">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="3bc47-448">La [configurazione della riga di comando](xref:fundamentals/configuration/index#command-line-configuration-provider) viene aggiunta chiamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="3bc47-449">La configurazione della riga di comando viene aggiunta per ultima per consentire agli argomenti della riga di comando di eseguire l'override della configurazione fornita dai provider di configurazione precedenti.</span><span class="sxs-lookup"><span data-stu-id="3bc47-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="3bc47-450">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3bc47-450">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="3bc47-451">È possibile fornire una configurazione aggiuntiva con le chiavi [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="3bc47-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="3bc47-452">Configurazione di esempio di `HostBuilder` che usa <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="3bc47-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="3bc47-453">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="3bc47-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="3bc47-454">La configurazione dell'app viene creata chiamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sull'implementazione <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="3bc47-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per creare un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfiguration> per l'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="3bc47-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="3bc47-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="3bc47-457">L'app usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="3bc47-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="3bc47-458">La configurazione creata da <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> è disponibile in [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) per le operazioni successive e in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="3bc47-459">La configurazione dell'app riceve automaticamente la configurazione dell'host fornita da [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="3bc47-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="3bc47-460">Configurazione app di esempio che usa <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="3bc47-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="3bc47-461">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="3bc47-461">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="3bc47-462">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="3bc47-462">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="3bc47-463">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="3bc47-463">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="3bc47-464">Per spostare i file delle impostazioni nella directory di output, specificare i file delle impostazioni come [elementi di progetto MSBuild](/visualstudio/msbuild/common-msbuild-project-items) nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="3bc47-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="3bc47-465">L'app di esempio sposta i file delle impostazioni dell'app JSON e *hostsettings.json* con l'elemento `<Content>` seguente:</span><span class="sxs-lookup"><span data-stu-id="3bc47-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="3bc47-466">I metodi di estensione di configurazione, ad esempio <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> e <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, necessitano di altri pacchetti NuGet, ad esempio [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) e [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="3bc47-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="3bc47-467">A meno che l'app non usi il [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), questi pacchetti devono essere aggiunti al progetto oltre al pacchetto [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) di base.</span><span class="sxs-lookup"><span data-stu-id="3bc47-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="3bc47-468">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="3bc47-469">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="3bc47-469">ConfigureServices</span></span>

<span data-ttu-id="3bc47-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> aggiunge servizi al contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3bc47-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="3bc47-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="3bc47-472">Un servizio ospitato è una classe con logica di attività in background che implementa l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="3bc47-473">Per altre informazioni, vedere <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="3bc47-474">L'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa il metodo di estensione `AddHostedService` per aggiungere all'app un servizio per gli eventi di durata (`LifetimeEventsHostedService`) e un'attività in background con timeout (`TimedHostedService`):</span><span class="sxs-lookup"><span data-stu-id="3bc47-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="3bc47-475">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="3bc47-475">ConfigureLogging</span></span>

<span data-ttu-id="3bc47-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> aggiunge un delegato per configurare l'interfaccia <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornita.</span><span class="sxs-lookup"><span data-stu-id="3bc47-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="3bc47-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="3bc47-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="3bc47-478">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="3bc47-478">UseConsoleLifetime</span></span>

<span data-ttu-id="3bc47-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>Ascolta la <kbd>combinazione di tasti CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM e chiama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> per avviare il processo di arresto.</span><span class="sxs-lookup"><span data-stu-id="3bc47-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="3bc47-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>Sblocca le estensioni, ad esempio [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="3bc47-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="3bc47-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` è preregistrata come implementazione di durata predefinita.</span><span class="sxs-lookup"><span data-stu-id="3bc47-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="3bc47-482">Viene usata l'ultima durata registrata.</span><span class="sxs-lookup"><span data-stu-id="3bc47-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="3bc47-483">Configurazione contenitore</span><span class="sxs-lookup"><span data-stu-id="3bc47-483">Container configuration</span></span>

<span data-ttu-id="3bc47-484">Per supportare l'inserimento di altri contenitori, l'host può accettare un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="3bc47-485">La disponibilità di una factory non fa parte della registrazione del contenitore DI, ma è una funzionalità intrinseca dell'host usata per creare il contenitore DI concreto.</span><span class="sxs-lookup"><span data-stu-id="3bc47-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="3bc47-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) esegue l'override della factory predefinita usata per la creazione del provider di servizi dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="3bc47-487">La configurazione del contenitore personalizzato è gestita dal metodo <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="3bc47-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> offre un'esperienza fortemente tipizzata per la configurazione del contenitore sulla base dell'API host sottostante.</span><span class="sxs-lookup"><span data-stu-id="3bc47-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="3bc47-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="3bc47-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="3bc47-490">Creare un contenitore di servizi per l'app:</span><span class="sxs-lookup"><span data-stu-id="3bc47-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="3bc47-491">Specificare una factory per il contenitore di servizi:</span><span class="sxs-lookup"><span data-stu-id="3bc47-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="3bc47-492">Usare la factory e configurare il contenitore di servizi personalizzato per l'app:</span><span class="sxs-lookup"><span data-stu-id="3bc47-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="3bc47-493">Estendibilità</span><span class="sxs-lookup"><span data-stu-id="3bc47-493">Extensibility</span></span>

<span data-ttu-id="3bc47-494">L'estensibilità host viene eseguita con i metodi di estensione su <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="3bc47-495">L'esempio seguente illustra come un metodo di estensione estende un'implementazione <xref:Microsoft.Extensions.Hosting.IHostBuilder> con l'esempio [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) dimostrato in <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="3bc47-496">Un'app stabilisce il metodo di estensione `UseHostedService` per registrare il servizio ospitato passato in `T`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="3bc47-497">Gestire l'host</span><span class="sxs-lookup"><span data-stu-id="3bc47-497">Manage the host</span></span>

<span data-ttu-id="3bc47-498">L'implementazione <xref:Microsoft.Extensions.Hosting.IHost> è responsabile dell'avvio e arresto delle implementazioni <xref:Microsoft.Extensions.Hosting.IHostedService> che sono registrate nel contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="3bc47-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="3bc47-499">Esegui</span><span class="sxs-lookup"><span data-stu-id="3bc47-499">Run</span></span>

<span data-ttu-id="3bc47-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> esegue l'app e blocca il thread di chiamata fino all'arresto dell'host:</span><span class="sxs-lookup"><span data-stu-id="3bc47-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="3bc47-501">RunAsync</span><span class="sxs-lookup"><span data-stu-id="3bc47-501">RunAsync</span></span>

<span data-ttu-id="3bc47-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> esegue l'app e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto:</span><span class="sxs-lookup"><span data-stu-id="3bc47-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="3bc47-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="3bc47-503">RunConsoleAsync</span></span>

<span data-ttu-id="3bc47-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Abilita il supporto della console, compila e avvia l'host e attende la <kbd>Ctrl</kbd> + chiusura di CTRL<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="3bc47-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="3bc47-505">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="3bc47-505">Start and StopAsync</span></span>

<span data-ttu-id="3bc47-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> avvia l'host in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="3bc47-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="3bc47-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> prova ad arrestare l'host entro il timeout specificato.</span><span class="sxs-lookup"><span data-stu-id="3bc47-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="3bc47-508">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="3bc47-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="3bc47-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> avvia l'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="3bc47-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> arresta l'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="3bc47-511">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="3bc47-511">WaitForShutdown</span></span>

<span data-ttu-id="3bc47-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>viene attivato tramite <xref:Microsoft.Extensions.Hosting.IHostLifetime> , ad esempio (in `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` attesa di <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="3bc47-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="3bc47-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="3bc47-514">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="3bc47-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="3bc47-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato l'arresto tramite il token specificato, quindi chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="3bc47-516">Controllo esterno</span><span class="sxs-lookup"><span data-stu-id="3bc47-516">External control</span></span>

<span data-ttu-id="3bc47-517">Il controllo esterno dell'host può essere ottenuto usando metodi che possono essere chiamati dall'esterno:</span><span class="sxs-lookup"><span data-stu-id="3bc47-517">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="3bc47-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> viene chiamato all'avvio di <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, che attende il completamento prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="3bc47-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="3bc47-519">Questo è utile per ritardare l'avvio fino al segnale trasmesso da un evento esterno.</span><span class="sxs-lookup"><span data-stu-id="3bc47-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="3bc47-520">Interfaccia IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="3bc47-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="3bc47-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> include informazioni sull'ambiente di hosting dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="3bc47-522">Usare l'[inserimento di un costruttore](xref:fundamentals/dependency-injection) per ottenere <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> per poterne usare le proprietà e i metodi di estensione:</span><span class="sxs-lookup"><span data-stu-id="3bc47-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="3bc47-523">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="3bc47-524">Interfaccia IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="3bc47-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="3bc47-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> consente attività post-avvio e di arresto, incluse le richieste di arresto normale.</span><span class="sxs-lookup"><span data-stu-id="3bc47-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="3bc47-526">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi <xref:System.Action> che definiscono gli eventi di avvio e arresto.</span><span class="sxs-lookup"><span data-stu-id="3bc47-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="3bc47-527">Token di annullamento</span><span class="sxs-lookup"><span data-stu-id="3bc47-527">Cancellation Token</span></span> | <span data-ttu-id="3bc47-528">Attivato quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="3bc47-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="3bc47-529">L'host è stato completamente avviato.</span><span class="sxs-lookup"><span data-stu-id="3bc47-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="3bc47-530">L'host sta completando un arresto normale.</span><span class="sxs-lookup"><span data-stu-id="3bc47-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="3bc47-531">Tutte le richieste devono essere elaborate.</span><span class="sxs-lookup"><span data-stu-id="3bc47-531">All requests should be processed.</span></span> <span data-ttu-id="3bc47-532">L'arresto è bloccato fino al completamento di questo evento.</span><span class="sxs-lookup"><span data-stu-id="3bc47-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="3bc47-533">L'host sta eseguendo un arresto normale.</span><span class="sxs-lookup"><span data-stu-id="3bc47-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="3bc47-534">È possibile che le richieste siano ancora in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="3bc47-534">Requests may still be processing.</span></span> <span data-ttu-id="3bc47-535">L'arresto è bloccato fino al completamento di questo evento.</span><span class="sxs-lookup"><span data-stu-id="3bc47-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="3bc47-536">Inserire tramite costruttore il servizio <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> in qualsiasi classe.</span><span class="sxs-lookup"><span data-stu-id="3bc47-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="3bc47-537">L'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa l'inserimento di costruttori in una classe `LifetimeEventsHostedService` (implementazione <xref:Microsoft.Extensions.Hosting.IHostedService>) per registrare gli eventi.</span><span class="sxs-lookup"><span data-stu-id="3bc47-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="3bc47-538">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="3bc47-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="3bc47-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> richiede la chiusura dell'applicazione corrente.</span><span class="sxs-lookup"><span data-stu-id="3bc47-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="3bc47-540">La classe seguente usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> per arrestare normalmente un'app quando viene chiamato il metodo `Shutdown` della classe:</span><span class="sxs-lookup"><span data-stu-id="3bc47-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3bc47-541">I modelli di ASP.NET Core creano un host generico .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="3bc47-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="3bc47-542">Definizione host</span><span class="sxs-lookup"><span data-stu-id="3bc47-542">Host definition</span></span>

<span data-ttu-id="3bc47-543">Un *host* è un oggetto che incapsula le risorse di un'app, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="3bc47-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="3bc47-544">Inserimento di dipendenze (DI)</span><span class="sxs-lookup"><span data-stu-id="3bc47-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="3bc47-545">Registrazione</span><span class="sxs-lookup"><span data-stu-id="3bc47-545">Logging</span></span>
* <span data-ttu-id="3bc47-546">Configurazione</span><span class="sxs-lookup"><span data-stu-id="3bc47-546">Configuration</span></span>
* <span data-ttu-id="3bc47-547">`IHostedService` Implementazioni</span><span class="sxs-lookup"><span data-stu-id="3bc47-547">`IHostedService` implementations</span></span>

<span data-ttu-id="3bc47-548">Quando viene avviato, un host chiama <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> su ogni implementazione di <xref:Microsoft.Extensions.Hosting.IHostedService> registrata nella raccolta di servizi ospitati del contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="3bc47-548">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="3bc47-549">In un'app Web, una delle implementazioni di `IHostedService` è un servizio web che avvia un'[implementazione del server HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="3bc47-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="3bc47-550">Il motivo principale per cui tutte le risorse interdipendenti dell'app sono incluse in un unico oggetto è la gestione del ciclo di vita, vale a dire il controllo sull'avvio dell'app e sull'arresto normale.</span><span class="sxs-lookup"><span data-stu-id="3bc47-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="3bc47-551">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="3bc47-551">Set up a host</span></span>

<span data-ttu-id="3bc47-552">L'host è in genere configurato, compilato ed eseguito da codice nella classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="3bc47-553">Il metodo `Main`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-553">The `Main` method:</span></span>

* <span data-ttu-id="3bc47-554">Chiama un metodo `CreateHostBuilder` per creare e configurare un oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="3bc47-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="3bc47-555">Chiamate `Build` e metodi `Run` nell'oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="3bc47-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="3bc47-556">I modelli Web ASP.NET Core generano il codice seguente per creare un host:</span><span class="sxs-lookup"><span data-stu-id="3bc47-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="3bc47-557">Il codice seguente crea un carico di lavoro non HTTP con un' `IHostedService` implementazione aggiunta al contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="3bc47-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="3bc47-558">Per un carico di lavoro HTTP, il metodo `Main` è lo stesso ma `CreateHostBuilder` chiama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="3bc47-559">Se l'app usa Entity Framework Core, non modificare il nome o la firma del metodo `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="3bc47-560">Gli [strumenti di Entity Framework Core](/ef/core/miscellaneous/cli/) si aspettano di trovare un metodo `CreateHostBuilder` che configura l'host senza eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="3bc47-561">Per altre informazioni, vedere [Creazione DbContext in fase di progettazione](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="3bc47-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="3bc47-562">Impostazioni predefinite del generatore</span><span class="sxs-lookup"><span data-stu-id="3bc47-562">Default builder settings</span></span>

<span data-ttu-id="3bc47-563">Il metodo <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="3bc47-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="3bc47-564">Imposta la [radice del contenuto](xref:fundamentals/index#content-root) sul percorso restituito da <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="3bc47-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="3bc47-565">Carica la configurazione dell'host da:</span><span class="sxs-lookup"><span data-stu-id="3bc47-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="3bc47-566">Variabili di ambiente con prefisso `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="3bc47-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="3bc47-567">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="3bc47-567">Command-line arguments.</span></span>
* <span data-ttu-id="3bc47-568">Carica la configurazione dell'app da:</span><span class="sxs-lookup"><span data-stu-id="3bc47-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="3bc47-569">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="3bc47-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="3bc47-570">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="3bc47-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="3bc47-571">[Segreti del manager](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="3bc47-572">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="3bc47-572">Environment variables.</span></span>
  * <span data-ttu-id="3bc47-573">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="3bc47-573">Command-line arguments.</span></span>
* <span data-ttu-id="3bc47-574">Aggiunge i provider di [log](xref:fundamentals/logging/index) seguenti:</span><span class="sxs-lookup"><span data-stu-id="3bc47-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="3bc47-575">Console</span><span class="sxs-lookup"><span data-stu-id="3bc47-575">Console</span></span>
  * <span data-ttu-id="3bc47-576">Debug</span><span class="sxs-lookup"><span data-stu-id="3bc47-576">Debug</span></span>
  * <span data-ttu-id="3bc47-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="3bc47-577">EventSource</span></span>
  * <span data-ttu-id="3bc47-578">EventLog (solo quando è in esecuzione su Windows)</span><span class="sxs-lookup"><span data-stu-id="3bc47-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="3bc47-579">Abilita la [convalida dell'ambito](xref:fundamentals/dependency-injection#scope-validation) e la [convalida delle dipendenze](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando l'ambiente è lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="3bc47-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="3bc47-580">Il metodo `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="3bc47-581">Carica la configurazione host dalle variabili di ambiente con prefisso `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="3bc47-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="3bc47-582">Imposta il server [Kestrel](xref:fundamentals/servers/kestrel) come server Web e lo configura usando i provider di configurazione dell'host dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="3bc47-583">Per le opzioni predefinite del server Kestrel, vedere <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="3bc47-584">Aggiunge [il middleware dell'applicazione di filtri dell'host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="3bc47-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="3bc47-585">Aggiunge il [middleware delle intestazioni con inoltri](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è uguale a `true` .</span><span class="sxs-lookup"><span data-stu-id="3bc47-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="3bc47-586">Abilita l'integrazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="3bc47-586">Enables IIS integration.</span></span> <span data-ttu-id="3bc47-587">Per le opzioni predefinite di IIS, vedere <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="3bc47-588">Le sezioni [Impostazioni per tutti i tipi di app](#settings-for-all-app-types) e [Impostazioni per le app Web](#settings-for-web-apps) più avanti in questo articolo mostrano come eseguire l'override delle impostazioni predefinite del generatore.</span><span class="sxs-lookup"><span data-stu-id="3bc47-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="3bc47-589">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="3bc47-589">Framework-provided services</span></span>

<span data-ttu-id="3bc47-590">I servizi seguenti vengono registrati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="3bc47-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="3bc47-591">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="3bc47-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="3bc47-592">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="3bc47-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="3bc47-593">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="3bc47-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="3bc47-594">Per ulteriori informazioni sui servizi forniti dal Framework, vedere <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="3bc47-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="3bc47-595">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="3bc47-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="3bc47-596">Inserire il servizio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (in precedenza `IApplicationLifetime`) in qualsiasi classe per gestire le attività post-avvio e di arresto normale.</span><span class="sxs-lookup"><span data-stu-id="3bc47-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="3bc47-597">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi del gestore dell'evento di avvio e di arresto.</span><span class="sxs-lookup"><span data-stu-id="3bc47-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="3bc47-598">L'interfaccia include anche un metodo `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="3bc47-599">L'esempio seguente è un' `IHostedService` implementazione di che registra `IHostApplicationLifetime` gli eventi:</span><span class="sxs-lookup"><span data-stu-id="3bc47-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="3bc47-600">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="3bc47-600">IHostLifetime</span></span>

<span data-ttu-id="3bc47-601">L'implementazione <xref:Microsoft.Extensions.Hosting.IHostLifetime> controlla quando l'host viene avviato e quando si arresta.</span><span class="sxs-lookup"><span data-stu-id="3bc47-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="3bc47-602">Viene usata l'ultima implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="3bc47-602">The last implementation registered is used.</span></span>

<span data-ttu-id="3bc47-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` è l'implementazione `IHostLifetime` predefinita.</span><span class="sxs-lookup"><span data-stu-id="3bc47-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="3bc47-604">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="3bc47-605">Ascolta la <kbd>combinazione di tasti CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM e chiama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> per avviare il processo di arresto.</span><span class="sxs-lookup"><span data-stu-id="3bc47-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="3bc47-606">Sblocca estensioni come [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="3bc47-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="3bc47-607">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="3bc47-607">IHostEnvironment</span></span>

<span data-ttu-id="3bc47-608">Inserire il <xref:Microsoft.Extensions.Hosting.IHostEnvironment> servizio in una classe per ottenere informazioni sulle impostazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3bc47-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="3bc47-609">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="3bc47-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="3bc47-610">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="3bc47-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="3bc47-611">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="3bc47-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="3bc47-612">Le app Web implementano l' `IWebHostEnvironment` interfaccia, che eredita `IHostEnvironment` e aggiunge [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="3bc47-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="3bc47-613">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="3bc47-613">Host configuration</span></span>

<span data-ttu-id="3bc47-614">La configurazione dell'host viene usata per le proprietà dell'implementazione <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="3bc47-615">La configurazione dell'host è disponibile da [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) all'interno di <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="3bc47-616">Dopo `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` viene sostituito con la configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="3bc47-617">Per aggiungere la configurazione dell'host, chiamare <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="3bc47-618">`ConfigureHostConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="3bc47-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="3bc47-619">L'host usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="3bc47-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="3bc47-620">Il provider di variabili di ambiente con prefisso `DOTNET_` e argomenti della riga di comando sono inclusi in `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3bc47-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="3bc47-621">Per le app Web, viene aggiunto il provider di variabili di ambiente con prefisso `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="3bc47-622">Il prefisso viene rimosso quando vengono lette le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="3bc47-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="3bc47-623">Ad esempio, il valore della variabile di ambiente per `ASPNETCORE_ENVIRONMENT` diventa il valore di configurazione dell'host per la chiave `environment`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="3bc47-624">L'esempio seguente crea la configurazione host:</span><span class="sxs-lookup"><span data-stu-id="3bc47-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="3bc47-625">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="3bc47-625">App configuration</span></span>

<span data-ttu-id="3bc47-626">La configurazione dell'app viene creata chiamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="3bc47-627">`ConfigureAppConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="3bc47-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="3bc47-628">L'app usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="3bc47-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="3bc47-629">La configurazione creata da `ConfigureAppConfiguration` è disponibile in [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) per le operazioni successive e come servizio da DI.</span><span class="sxs-lookup"><span data-stu-id="3bc47-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="3bc47-630">La configurazione dell'host viene aggiunta anche alla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="3bc47-631">Per altre informazioni, vedere [Configurazione in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="3bc47-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="3bc47-632">Impostazioni per tutti i tipi di app</span><span class="sxs-lookup"><span data-stu-id="3bc47-632">Settings for all app types</span></span>

<span data-ttu-id="3bc47-633">Questa sezione elenca le impostazioni dell'host che si applicano ai carichi di lavoro HTTP e non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="3bc47-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="3bc47-634">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="3bc47-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="3bc47-635">ApplicationName</span></span>

<span data-ttu-id="3bc47-636">La proprietà [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) viene impostata dalla configurazione dell'host durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="3bc47-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="3bc47-637">**Chiave**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="3bc47-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="3bc47-638">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-638">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-639">**Impostazione predefinita**: il nome dell'assembly che contiene il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="3bc47-640">**Variabile di ambiente**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="3bc47-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="3bc47-641">per impostare questo valore usare la variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="3bc47-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="3bc47-642">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="3bc47-642">ContentRoot</span></span>

<span data-ttu-id="3bc47-643">La proprietà [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina il punto in cui l'host inizia a cercare i file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="3bc47-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="3bc47-644">Se il percorso non esiste, l'host non verrà avviato.</span><span class="sxs-lookup"><span data-stu-id="3bc47-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="3bc47-645">**Chiave**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="3bc47-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="3bc47-646">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-646">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-647">**Impostazione predefinita**: la cartella in cui risiede l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="3bc47-648">**Variabile di ambiente**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="3bc47-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="3bc47-649">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseContentRoot` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="3bc47-650">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="3bc47-650">For more information, see:</span></span>

* [<span data-ttu-id="3bc47-651">Nozioni fondamentali: radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="3bc47-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="3bc47-652">WebRoot</span><span class="sxs-lookup"><span data-stu-id="3bc47-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="3bc47-653">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="3bc47-653">EnvironmentName</span></span>

<span data-ttu-id="3bc47-654">La proprietà [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) può essere impostata su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="3bc47-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="3bc47-655">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="3bc47-656">I valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="3bc47-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="3bc47-657">**Chiave**:`environment`</span><span class="sxs-lookup"><span data-stu-id="3bc47-657">**Key**: `environment`</span></span>  
<span data-ttu-id="3bc47-658">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-658">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-659">**Impostazione predefinita**:`Production`</span><span class="sxs-lookup"><span data-stu-id="3bc47-659">**Default**: `Production`</span></span>  
<span data-ttu-id="3bc47-660">**Variabile di ambiente**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="3bc47-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="3bc47-661">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseEnvironment` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="3bc47-662">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="3bc47-662">ShutdownTimeout</span></span>

<span data-ttu-id="3bc47-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) imposta il timeout per <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="3bc47-664">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="3bc47-664">The default value is five seconds.</span></span>  <span data-ttu-id="3bc47-665">Durante il periodo di timeout, l'host:</span><span class="sxs-lookup"><span data-stu-id="3bc47-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="3bc47-666">attiva [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="3bc47-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="3bc47-667">Tenta di arrestare i servizi ospitati, registrando gli errori per i servizi che non si interrompono.</span><span class="sxs-lookup"><span data-stu-id="3bc47-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="3bc47-668">Se il periodo di timeout scade prima che siano stati arrestati tutti i servizi ospitati, gli eventuali servizi attivi rimanenti vengono interrotti quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="3bc47-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="3bc47-669">I servizi si arrestano anche se non hanno completato l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="3bc47-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="3bc47-670">Se l'arresto dei servizi richiede più tempo, aumentare il valore di timeout.</span><span class="sxs-lookup"><span data-stu-id="3bc47-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="3bc47-671">**Chiave**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="3bc47-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="3bc47-672">**Tipo**:`int`</span><span class="sxs-lookup"><span data-stu-id="3bc47-672">**Type**: `int`</span></span>  
<span data-ttu-id="3bc47-673">**Impostazione predefinita**: 5 secondi</span><span class="sxs-lookup"><span data-stu-id="3bc47-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="3bc47-674">**Variabile di ambiente**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="3bc47-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="3bc47-675">Per impostare questo valore, usare la variabile di ambiente o configurare `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="3bc47-676">Nell'esempio seguente il timeout viene impostato su 20 secondi:</span><span class="sxs-lookup"><span data-stu-id="3bc47-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="3bc47-677">Disabilitare il ricaricamento della configurazione dell'app durante la modifica</span><span class="sxs-lookup"><span data-stu-id="3bc47-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="3bc47-678">Per [impostazione predefinita](xref:fundamentals/configuration/index#default), *appsettings.json* e *appSettings. { L'ambiente}. JSON* viene ricaricato quando il file viene modificato.</span><span class="sxs-lookup"><span data-stu-id="3bc47-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="3bc47-679">Per disabilitare questo comportamento di ricaricamento in ASP.NET Core 5,0 Preview 3 o versione successiva, impostare la `hostBuilder:reloadConfigOnChange` chiave su `false` .</span><span class="sxs-lookup"><span data-stu-id="3bc47-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="3bc47-680">**Chiave**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="3bc47-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="3bc47-681">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="3bc47-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3bc47-682">**Impostazione predefinita**:`true`</span><span class="sxs-lookup"><span data-stu-id="3bc47-682">**Default**: `true`</span></span>  
<span data-ttu-id="3bc47-683">**Argomento della riga di comando**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="3bc47-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="3bc47-684">**Variabile di ambiente**:`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="3bc47-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="3bc47-685">Il separatore dei due punti ( `:` ) non funziona con le chiavi gerarchiche delle variabili di ambiente in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="3bc47-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="3bc47-686">Per altre informazioni, vedere [variabili di ambiente](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="3bc47-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="3bc47-687">Impostazioni per le app Web</span><span class="sxs-lookup"><span data-stu-id="3bc47-687">Settings for web apps</span></span>

<span data-ttu-id="3bc47-688">Alcune impostazioni host si applicano solo ai carichi di lavoro HTTP.</span><span class="sxs-lookup"><span data-stu-id="3bc47-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="3bc47-689">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="3bc47-690">Per queste impostazioni sono disponibili i metodi di estensione su `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="3bc47-691">Gli esempi di codice che illustrano come chiamare i metodi di estensione presumono che `webBuilder` sia un'istanza di `IWebHostBuilder`, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="3bc47-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="3bc47-692">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="3bc47-692">CaptureStartupErrors</span></span>

<span data-ttu-id="3bc47-693">Quando è `false`, gli errori durante l'avvio causano la chiusura dell'host.</span><span class="sxs-lookup"><span data-stu-id="3bc47-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="3bc47-694">Quando è `true`, l'host acquisisce le eccezioni durante l'avvio e tenta di avviare il server.</span><span class="sxs-lookup"><span data-stu-id="3bc47-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="3bc47-695">**Chiave**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="3bc47-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="3bc47-696">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="3bc47-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3bc47-697">**Impostazione predefinita**: il valore predefinito è `false` a meno che l'app non venga eseguita con Kestrel in IIS, in tal caso il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="3bc47-698">**Variabile di ambiente**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="3bc47-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="3bc47-699">Per impostare questo valore, usare la configurazione o chiamare `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="3bc47-700">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="3bc47-700">DetailedErrors</span></span>

<span data-ttu-id="3bc47-701">Quando è abilitata o quando l'ambiente è `Development`, l'app acquisisce gli errori dettagliati.</span><span class="sxs-lookup"><span data-stu-id="3bc47-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="3bc47-702">**Chiave**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="3bc47-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="3bc47-703">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="3bc47-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3bc47-704">**Impostazione predefinita**:`false`</span><span class="sxs-lookup"><span data-stu-id="3bc47-704">**Default**: `false`</span></span>  
<span data-ttu-id="3bc47-705">**Variabile di ambiente**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="3bc47-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="3bc47-706">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="3bc47-707">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="3bc47-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="3bc47-708">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da caricare all'avvio.</span><span class="sxs-lookup"><span data-stu-id="3bc47-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="3bc47-709">Sebbene il valore di configurazione predefinito sia una stringa vuota, gli assembly di avvio dell'hosting includono sempre l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="3bc47-710">Quando vengono specificati, gli assembly di avvio dell'hosting vengono aggiunti all'assembly dell'app per essere caricati quando l'app compila i servizi comuni durante l'avvio.</span><span class="sxs-lookup"><span data-stu-id="3bc47-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="3bc47-711">**Chiave**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="3bc47-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="3bc47-712">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-712">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-713">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="3bc47-713">**Default**: Empty string</span></span>  
<span data-ttu-id="3bc47-714">**Variabile di ambiente**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="3bc47-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="3bc47-715">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="3bc47-716">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="3bc47-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="3bc47-717">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da escludere all'avvio.</span><span class="sxs-lookup"><span data-stu-id="3bc47-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="3bc47-718">**Chiave**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="3bc47-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="3bc47-719">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-719">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-720">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="3bc47-720">**Default**: Empty string</span></span>  
<span data-ttu-id="3bc47-721">**Variabile di ambiente**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="3bc47-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="3bc47-722">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="3bc47-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="3bc47-723">HTTPS_Port</span></span>

<span data-ttu-id="3bc47-724">Porta di reindirizzamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3bc47-724">The HTTPS redirect port.</span></span> <span data-ttu-id="3bc47-725">Usata per [imporre HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="3bc47-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="3bc47-726">**Chiave**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="3bc47-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="3bc47-727">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-727">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-728">**Impostazione predefinita**: un valore predefinito non è impostato.</span><span class="sxs-lookup"><span data-stu-id="3bc47-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="3bc47-729">**Variabile di ambiente**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="3bc47-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="3bc47-730">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="3bc47-731">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="3bc47-731">PreferHostingUrls</span></span>

<span data-ttu-id="3bc47-732">Indica se l'host deve essere in ascolto sugli URL configurati con `IWebHostBuilder` anziché gli URL configurati con l' `IServer` implementazione di.</span><span class="sxs-lookup"><span data-stu-id="3bc47-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="3bc47-733">**Chiave**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="3bc47-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="3bc47-734">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="3bc47-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3bc47-735">**Impostazione predefinita**:`true`</span><span class="sxs-lookup"><span data-stu-id="3bc47-735">**Default**: `true`</span></span>  
<span data-ttu-id="3bc47-736">**Variabile di ambiente**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="3bc47-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="3bc47-737">Per impostare questo valore, usare la variabile di ambiente o chiamare `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="3bc47-738">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="3bc47-738">PreventHostingStartup</span></span>

<span data-ttu-id="3bc47-739">Impedisce il caricamento automatico degli assembly di avvio dell'hosting, inclusi gli assembly di avvio dell'hosting configurati dall'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="3bc47-740">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="3bc47-741">**Chiave**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="3bc47-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="3bc47-742">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="3bc47-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3bc47-743">**Impostazione predefinita**:`false`</span><span class="sxs-lookup"><span data-stu-id="3bc47-743">**Default**: `false`</span></span>  
<span data-ttu-id="3bc47-744">**Variabile di ambiente**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="3bc47-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="3bc47-745">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="3bc47-746">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="3bc47-746">StartupAssembly</span></span>

<span data-ttu-id="3bc47-747">L'assembly per la ricerca della classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="3bc47-748">**Chiave**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="3bc47-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="3bc47-749">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-749">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-750">**Impostazione predefinita**: assembly dell'app</span><span class="sxs-lookup"><span data-stu-id="3bc47-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="3bc47-751">**Variabile di ambiente**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="3bc47-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="3bc47-752">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="3bc47-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="3bc47-753">`UseStartup` può richiedere un nome di assembly (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="3bc47-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="3bc47-754">Se vengono chiamati più metodi `UseStartup`, l'ultimo metodo ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="3bc47-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="3bc47-755">URL</span><span class="sxs-lookup"><span data-stu-id="3bc47-755">URLs</span></span>

<span data-ttu-id="3bc47-756">Elenco delimitato da punto e virgola degli indirizzi IP o gli indirizzi host con le porte e protocolli su cui il server deve eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="3bc47-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="3bc47-757">Ad esempio, `http://localhost:123`</span><span class="sxs-lookup"><span data-stu-id="3bc47-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="3bc47-758">Usare "\*" per indicare che il server deve eseguire l'ascolto per le richieste su tutti gli indirizzi IP o nomi host usando la porta e il protocollo specificati (ad esempio, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="3bc47-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="3bc47-759">Il protocollo (`http://` o `https://`) deve essere incluso con ogni URL.</span><span class="sxs-lookup"><span data-stu-id="3bc47-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="3bc47-760">I formati supportati variano a seconda del server.</span><span class="sxs-lookup"><span data-stu-id="3bc47-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="3bc47-761">**Chiave**:`urls`</span><span class="sxs-lookup"><span data-stu-id="3bc47-761">**Key**: `urls`</span></span>  
<span data-ttu-id="3bc47-762">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-762">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-763">**Impostazione predefinita**: `http://localhost:5000` e`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="3bc47-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="3bc47-764">**Variabile di ambiente**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="3bc47-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="3bc47-765">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="3bc47-766">Kestrel ha una propria API di configurazione degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="3bc47-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="3bc47-767">Per altre informazioni, vedere <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="3bc47-768">WebRoot</span><span class="sxs-lookup"><span data-stu-id="3bc47-768">WebRoot</span></span>

<span data-ttu-id="3bc47-769">La proprietà [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina il percorso relativo degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="3bc47-770">Se il percorso non esiste, viene usato un provider di file no-op.</span><span class="sxs-lookup"><span data-stu-id="3bc47-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="3bc47-771">**Chiave**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="3bc47-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="3bc47-772">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="3bc47-772">**Type**: `string`</span></span>  
<span data-ttu-id="3bc47-773">**Impostazione predefinita**: il valore predefinito è `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="3bc47-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="3bc47-774">Il percorso di *{radice del contenuto}/wwwroot* deve esistere.</span><span class="sxs-lookup"><span data-stu-id="3bc47-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="3bc47-775">**Variabile di ambiente**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="3bc47-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="3bc47-776">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseWebRoot` su `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="3bc47-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="3bc47-777">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="3bc47-777">For more information, see:</span></span>

* [<span data-ttu-id="3bc47-778">Nozioni fondamentali: radice Web</span><span class="sxs-lookup"><span data-stu-id="3bc47-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="3bc47-779">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="3bc47-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="3bc47-780">Gestire la durata dell'host</span><span class="sxs-lookup"><span data-stu-id="3bc47-780">Manage the host lifetime</span></span>

<span data-ttu-id="3bc47-781">Chiamare metodi sull'implementazione <xref:Microsoft.Extensions.Hosting.IHost> incorporata per avviare e arrestare l'app.</span><span class="sxs-lookup"><span data-stu-id="3bc47-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="3bc47-782">Questi metodi influiscono su tutte le implementazioni <xref:Microsoft.Extensions.Hosting.IHostedService> che vengono registrate nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="3bc47-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="3bc47-783">Esegui</span><span class="sxs-lookup"><span data-stu-id="3bc47-783">Run</span></span>

<span data-ttu-id="3bc47-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> esegue l'app e blocca il thread di chiamata fino all'arresto dell'host.</span><span class="sxs-lookup"><span data-stu-id="3bc47-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="3bc47-785">RunAsync</span><span class="sxs-lookup"><span data-stu-id="3bc47-785">RunAsync</span></span>

<span data-ttu-id="3bc47-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> esegue l'app e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="3bc47-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="3bc47-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="3bc47-787">RunConsoleAsync</span></span>

<span data-ttu-id="3bc47-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Abilita il supporto della console, compila e avvia l'host e attende la <kbd>Ctrl</kbd> + chiusura di CTRL<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="3bc47-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="3bc47-789">Avvio</span><span class="sxs-lookup"><span data-stu-id="3bc47-789">Start</span></span>

<span data-ttu-id="3bc47-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> avvia l'host in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="3bc47-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="3bc47-791">StartAsync</span><span class="sxs-lookup"><span data-stu-id="3bc47-791">StartAsync</span></span>

<span data-ttu-id="3bc47-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> avvia l'host e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="3bc47-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="3bc47-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> viene chiamato all'avvio di `StartAsync`, che attende il completamento prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="3bc47-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="3bc47-794">Questo è utile per ritardare l'avvio fino al segnale trasmesso da un evento esterno.</span><span class="sxs-lookup"><span data-stu-id="3bc47-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="3bc47-795">StopAsync</span><span class="sxs-lookup"><span data-stu-id="3bc47-795">StopAsync</span></span>

<span data-ttu-id="3bc47-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> prova ad arrestare l'host entro il timeout specificato.</span><span class="sxs-lookup"><span data-stu-id="3bc47-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="3bc47-797">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="3bc47-797">WaitForShutdown</span></span>

<span data-ttu-id="3bc47-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blocca il thread chiamante finché l'arresto non viene attivato da IHostLifetime, ad esempio tramite <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="3bc47-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="3bc47-799">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="3bc47-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="3bc47-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato l'arresto tramite il token specificato, quindi chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="3bc47-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="3bc47-801">Controllo esterno</span><span class="sxs-lookup"><span data-stu-id="3bc47-801">External control</span></span>

<span data-ttu-id="3bc47-802">Il controllo diretto della durata dell'host può essere ottenuto usando metodi che possono essere chiamati dall'esterno:</span><span class="sxs-lookup"><span data-stu-id="3bc47-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3bc47-803">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3bc47-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
