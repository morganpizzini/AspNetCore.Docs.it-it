---
title: Host generico .NET
author: rick-anderson
description: Informazioni sull'host generico .NET Core, che gestisce l'avvio e la durata delle app.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
no-loc:
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: e606812c1c2164a7e4d0926a76d2ff7ada4c9a87
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635385"
---
# <a name="net-generic-host"></a><span data-ttu-id="ae54a-103">Host generico .NET</span><span class="sxs-lookup"><span data-stu-id="ae54a-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="ae54a-104">I modelli di ASP.NET Core creano un host generico .NET Core, <xref:Microsoft.Extensions.Hosting.HostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="ae54a-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="ae54a-105">Definizione host</span><span class="sxs-lookup"><span data-stu-id="ae54a-105">Host definition</span></span>

<span data-ttu-id="ae54a-106">Un *host* è un oggetto che incapsula le risorse di un'app, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ae54a-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="ae54a-107">Inserimento di dipendenze (DI)</span><span class="sxs-lookup"><span data-stu-id="ae54a-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="ae54a-108">Registrazione</span><span class="sxs-lookup"><span data-stu-id="ae54a-108">Logging</span></span>
* <span data-ttu-id="ae54a-109">Configurazione</span><span class="sxs-lookup"><span data-stu-id="ae54a-109">Configuration</span></span>
* <span data-ttu-id="ae54a-110">`IHostedService` Implementazioni</span><span class="sxs-lookup"><span data-stu-id="ae54a-110">`IHostedService` implementations</span></span>

<span data-ttu-id="ae54a-111">Quando viene avviato, un host chiama <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> su ogni implementazione di <xref:Microsoft.Extensions.Hosting.IHostedService> registrata nella raccolta di servizi ospitati del contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="ae54a-111">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="ae54a-112">In un'app Web, una delle implementazioni di `IHostedService` è un servizio web che avvia un'[implementazione del server HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="ae54a-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="ae54a-113">Il motivo principale per cui tutte le risorse interdipendenti dell'app sono incluse in un unico oggetto è la gestione del ciclo di vita, vale a dire il controllo sull'avvio dell'app e sull'arresto normale.</span><span class="sxs-lookup"><span data-stu-id="ae54a-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="ae54a-114">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="ae54a-114">Set up a host</span></span>

<span data-ttu-id="ae54a-115">L'host è in genere configurato, compilato ed eseguito da codice nella classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="ae54a-116">Il metodo `Main`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-116">The `Main` method:</span></span>

* <span data-ttu-id="ae54a-117">Chiama un metodo `CreateHostBuilder` per creare e configurare un oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="ae54a-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="ae54a-118">Chiamate `Build` e metodi `Run` nell'oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="ae54a-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="ae54a-119">I modelli Web ASP.NET Core generano il codice seguente per creare un host generico:</span><span class="sxs-lookup"><span data-stu-id="ae54a-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="ae54a-120">Il codice seguente crea un host generico utilizzando un carico di lavoro non HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae54a-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="ae54a-121">L' `IHostedService` implementazione viene aggiunta al contenitore DI di:</span><span class="sxs-lookup"><span data-stu-id="ae54a-121">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="ae54a-122">Per un carico di lavoro HTTP, il metodo `Main` è lo stesso ma `CreateHostBuilder` chiama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="ae54a-123">Il codice precedente viene generato dai modelli di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae54a-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="ae54a-124">Se l'app usa Entity Framework Core, non modificare il nome o la firma del metodo `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="ae54a-125">Gli [strumenti di Entity Framework Core](/ef/core/miscellaneous/cli/) si aspettano di trovare un metodo `CreateHostBuilder` che configura l'host senza eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="ae54a-126">Per altre informazioni, vedere [Creazione DbContext in fase di progettazione](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="ae54a-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="ae54a-127">Impostazioni predefinite del generatore</span><span class="sxs-lookup"><span data-stu-id="ae54a-127">Default builder settings</span></span>

<span data-ttu-id="ae54a-128">Il metodo <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="ae54a-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="ae54a-129">Imposta la [radice del contenuto](xref:fundamentals/index#content-root) sul percorso restituito da <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="ae54a-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="ae54a-130">Carica la configurazione dell'host da:</span><span class="sxs-lookup"><span data-stu-id="ae54a-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="ae54a-131">Variabili di ambiente con prefisso `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="ae54a-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="ae54a-132">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="ae54a-132">Command-line arguments.</span></span>
* <span data-ttu-id="ae54a-133">Carica la configurazione dell'app da:</span><span class="sxs-lookup"><span data-stu-id="ae54a-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="ae54a-134">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="ae54a-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="ae54a-135">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="ae54a-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="ae54a-136">[Segreti del manager](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="ae54a-137">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="ae54a-137">Environment variables.</span></span>
  * <span data-ttu-id="ae54a-138">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="ae54a-138">Command-line arguments.</span></span>
* <span data-ttu-id="ae54a-139">Aggiunge i provider di [log](xref:fundamentals/logging/index) seguenti:</span><span class="sxs-lookup"><span data-stu-id="ae54a-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="ae54a-140">Console</span><span class="sxs-lookup"><span data-stu-id="ae54a-140">Console</span></span>
  * <span data-ttu-id="ae54a-141">Debug</span><span class="sxs-lookup"><span data-stu-id="ae54a-141">Debug</span></span>
  * <span data-ttu-id="ae54a-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="ae54a-142">EventSource</span></span>
  * <span data-ttu-id="ae54a-143">EventLog (solo quando è in esecuzione su Windows)</span><span class="sxs-lookup"><span data-stu-id="ae54a-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="ae54a-144">Abilita la [convalida dell'ambito](xref:fundamentals/dependency-injection#scope-validation) e la [convalida delle dipendenze](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando l'ambiente è lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="ae54a-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="ae54a-145">Il metodo `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="ae54a-146">Carica la configurazione host dalle variabili di ambiente con prefisso `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="ae54a-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="ae54a-147">Imposta il server [Kestrel](xref:fundamentals/servers/kestrel) come server Web e lo configura usando i provider di configurazione dell'host dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="ae54a-148">Per le opzioni predefinite del server Kestrel, vedere <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="ae54a-149">Aggiunge [il middleware dell'applicazione di filtri dell'host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ae54a-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="ae54a-150">Aggiunge il [middleware delle intestazioni con inoltri](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è uguale a `true` .</span><span class="sxs-lookup"><span data-stu-id="ae54a-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="ae54a-151">Abilita l'integrazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="ae54a-151">Enables IIS integration.</span></span> <span data-ttu-id="ae54a-152">Per le opzioni predefinite di IIS, vedere <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="ae54a-153">Le sezioni [Impostazioni per tutti i tipi di app](#settings-for-all-app-types) e [Impostazioni per le app Web](#settings-for-web-apps) più avanti in questo articolo mostrano come eseguire l'override delle impostazioni predefinite del generatore.</span><span class="sxs-lookup"><span data-stu-id="ae54a-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="ae54a-154">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="ae54a-154">Framework-provided services</span></span>

<span data-ttu-id="ae54a-155">I servizi seguenti vengono registrati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="ae54a-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="ae54a-156">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ae54a-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="ae54a-157">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="ae54a-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="ae54a-158">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="ae54a-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="ae54a-159">Per ulteriori informazioni sui servizi forniti dal Framework, vedere <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="ae54a-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="ae54a-160">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ae54a-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="ae54a-161">Inserire il servizio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (in precedenza `IApplicationLifetime`) in qualsiasi classe per gestire le attività post-avvio e di arresto normale.</span><span class="sxs-lookup"><span data-stu-id="ae54a-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="ae54a-162">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi del gestore dell'evento di avvio e di arresto.</span><span class="sxs-lookup"><span data-stu-id="ae54a-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="ae54a-163">L'interfaccia include anche un metodo `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="ae54a-164">L'esempio seguente è un' `IHostedService` implementazione di che registra `IHostApplicationLifetime` gli eventi:</span><span class="sxs-lookup"><span data-stu-id="ae54a-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="ae54a-165">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="ae54a-165">IHostLifetime</span></span>

<span data-ttu-id="ae54a-166">L'implementazione <xref:Microsoft.Extensions.Hosting.IHostLifetime> controlla quando l'host viene avviato e quando si arresta.</span><span class="sxs-lookup"><span data-stu-id="ae54a-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="ae54a-167">Viene usata l'ultima implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="ae54a-167">The last implementation registered is used.</span></span>

<span data-ttu-id="ae54a-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` è l'implementazione `IHostLifetime` predefinita.</span><span class="sxs-lookup"><span data-stu-id="ae54a-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="ae54a-169">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="ae54a-170">Ascolta la <kbd>combinazione di tasti CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM e chiama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> per avviare il processo di arresto.</span><span class="sxs-lookup"><span data-stu-id="ae54a-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="ae54a-171">Sblocca estensioni come [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="ae54a-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="ae54a-172">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="ae54a-172">IHostEnvironment</span></span>

<span data-ttu-id="ae54a-173">Inserire il <xref:Microsoft.Extensions.Hosting.IHostEnvironment> servizio in una classe per ottenere informazioni sulle impostazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="ae54a-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="ae54a-174">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="ae54a-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="ae54a-175">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="ae54a-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="ae54a-176">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="ae54a-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="ae54a-177">Le app Web implementano l' `IWebHostEnvironment` interfaccia, che eredita `IHostEnvironment` e aggiunge [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="ae54a-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="ae54a-178">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="ae54a-178">Host configuration</span></span>

<span data-ttu-id="ae54a-179">La configurazione dell'host viene usata per le proprietà dell'implementazione <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="ae54a-180">La configurazione dell'host è disponibile da [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) all'interno di <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="ae54a-181">Dopo `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` viene sostituito con la configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="ae54a-182">Per aggiungere la configurazione dell'host, chiamare <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="ae54a-183">`ConfigureHostConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="ae54a-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="ae54a-184">L'host usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="ae54a-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="ae54a-185">Il provider di variabili di ambiente con prefisso `DOTNET_` e argomenti della riga di comando sono inclusi in `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ae54a-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ae54a-186">Per le app Web, viene aggiunto il provider di variabili di ambiente con prefisso `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="ae54a-187">Il prefisso viene rimosso quando vengono lette le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="ae54a-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="ae54a-188">Ad esempio, il valore della variabile di ambiente per `ASPNETCORE_ENVIRONMENT` diventa il valore di configurazione dell'host per la chiave `environment`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="ae54a-189">L'esempio seguente crea la configurazione host:</span><span class="sxs-lookup"><span data-stu-id="ae54a-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="ae54a-190">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="ae54a-190">App configuration</span></span>

<span data-ttu-id="ae54a-191">La configurazione dell'app viene creata chiamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="ae54a-192">`ConfigureAppConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="ae54a-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="ae54a-193">L'app usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="ae54a-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="ae54a-194">La configurazione creata da `ConfigureAppConfiguration` è disponibile in [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) per le operazioni successive e come servizio da DI.</span><span class="sxs-lookup"><span data-stu-id="ae54a-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="ae54a-195">La configurazione dell'host viene aggiunta anche alla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="ae54a-196">Per altre informazioni, vedere [Configurazione in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="ae54a-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="ae54a-197">Impostazioni per tutti i tipi di app</span><span class="sxs-lookup"><span data-stu-id="ae54a-197">Settings for all app types</span></span>

<span data-ttu-id="ae54a-198">Questa sezione elenca le impostazioni dell'host che si applicano ai carichi di lavoro HTTP e non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae54a-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="ae54a-199">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="ae54a-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="ae54a-200">ApplicationName</span></span>

<span data-ttu-id="ae54a-201">La proprietà [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) viene impostata dalla configurazione dell'host durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="ae54a-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="ae54a-202">**Chiave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="ae54a-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="ae54a-203">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-203">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-204">**Impostazione predefinita**: il nome dell'assembly che contiene il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="ae54a-205">**Variabile di ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="ae54a-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="ae54a-206">per impostare questo valore usare la variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="ae54a-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="ae54a-207">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="ae54a-207">ContentRoot</span></span>

<span data-ttu-id="ae54a-208">La proprietà [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina il punto in cui l'host inizia a cercare i file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="ae54a-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="ae54a-209">Se il percorso non esiste, l'host non verrà avviato.</span><span class="sxs-lookup"><span data-stu-id="ae54a-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="ae54a-210">**Chiave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="ae54a-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="ae54a-211">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-211">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-212">**Impostazione predefinita**: la cartella in cui risiede l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="ae54a-213">**Variabile di ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="ae54a-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="ae54a-214">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseContentRoot` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="ae54a-215">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="ae54a-215">For more information, see:</span></span>

* [<span data-ttu-id="ae54a-216">Nozioni fondamentali: radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="ae54a-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="ae54a-217">WebRoot</span><span class="sxs-lookup"><span data-stu-id="ae54a-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="ae54a-218">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="ae54a-218">EnvironmentName</span></span>

<span data-ttu-id="ae54a-219">La proprietà [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) può essere impostata su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="ae54a-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="ae54a-220">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="ae54a-221">I valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="ae54a-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="ae54a-222">**Chiave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="ae54a-222">**Key**: `environment`</span></span>  
<span data-ttu-id="ae54a-223">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-223">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-224">**Impostazione predefinita**: `Production`</span><span class="sxs-lookup"><span data-stu-id="ae54a-224">**Default**: `Production`</span></span>  
<span data-ttu-id="ae54a-225">**Variabile di ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="ae54a-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="ae54a-226">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseEnvironment` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="ae54a-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="ae54a-227">ShutdownTimeout</span></span>

<span data-ttu-id="ae54a-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) imposta il timeout per <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="ae54a-229">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="ae54a-229">The default value is five seconds.</span></span>  <span data-ttu-id="ae54a-230">Durante il periodo di timeout, l'host:</span><span class="sxs-lookup"><span data-stu-id="ae54a-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="ae54a-231">attiva [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="ae54a-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="ae54a-232">Tenta di arrestare i servizi ospitati, registrando gli errori per i servizi che non si interrompono.</span><span class="sxs-lookup"><span data-stu-id="ae54a-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="ae54a-233">Se il periodo di timeout scade prima che siano stati arrestati tutti i servizi ospitati, gli eventuali servizi attivi rimanenti vengono interrotti quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="ae54a-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="ae54a-234">I servizi si arrestano anche se non hanno completato l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="ae54a-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="ae54a-235">Se l'arresto dei servizi richiede più tempo, aumentare il valore di timeout.</span><span class="sxs-lookup"><span data-stu-id="ae54a-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="ae54a-236">**Chiave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="ae54a-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="ae54a-237">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="ae54a-237">**Type**: `int`</span></span>  
<span data-ttu-id="ae54a-238">**Impostazione predefinita**: 5 secondi</span><span class="sxs-lookup"><span data-stu-id="ae54a-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="ae54a-239">**Variabile di ambiente**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="ae54a-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="ae54a-240">Per impostare questo valore, usare la variabile di ambiente o configurare `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="ae54a-241">Nell'esempio seguente il timeout viene impostato su 20 secondi:</span><span class="sxs-lookup"><span data-stu-id="ae54a-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="ae54a-242">Impostazioni per le app Web</span><span class="sxs-lookup"><span data-stu-id="ae54a-242">Settings for web apps</span></span>

<span data-ttu-id="ae54a-243">Alcune impostazioni host si applicano solo ai carichi di lavoro HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae54a-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="ae54a-244">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="ae54a-245">Per queste impostazioni sono disponibili i metodi di estensione su `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="ae54a-246">Gli esempi di codice che illustrano come chiamare i metodi di estensione presumono che `webBuilder` sia un'istanza di `IWebHostBuilder`, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="ae54a-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="ae54a-247">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="ae54a-247">CaptureStartupErrors</span></span>

<span data-ttu-id="ae54a-248">Quando è `false`, gli errori durante l'avvio causano la chiusura dell'host.</span><span class="sxs-lookup"><span data-stu-id="ae54a-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="ae54a-249">Quando è `true`, l'host acquisisce le eccezioni durante l'avvio e tenta di avviare il server.</span><span class="sxs-lookup"><span data-stu-id="ae54a-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="ae54a-250">**Chiave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="ae54a-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="ae54a-251">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="ae54a-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ae54a-252">**Impostazione predefinita**: il valore predefinito è `false` a meno che l'app non venga eseguita con Kestrel in IIS, in tal caso il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="ae54a-253">**Variabile di ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="ae54a-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="ae54a-254">Per impostare questo valore, usare la configurazione o chiamare `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="ae54a-255">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="ae54a-255">DetailedErrors</span></span>

<span data-ttu-id="ae54a-256">Quando è abilitata o quando l'ambiente è `Development`, l'app acquisisce gli errori dettagliati.</span><span class="sxs-lookup"><span data-stu-id="ae54a-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="ae54a-257">**Chiave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="ae54a-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="ae54a-258">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="ae54a-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ae54a-259">**Impostazione predefinita**: `false`</span><span class="sxs-lookup"><span data-stu-id="ae54a-259">**Default**: `false`</span></span>  
<span data-ttu-id="ae54a-260">**Variabile di ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="ae54a-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="ae54a-261">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="ae54a-262">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="ae54a-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="ae54a-263">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da caricare all'avvio.</span><span class="sxs-lookup"><span data-stu-id="ae54a-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="ae54a-264">Sebbene il valore di configurazione predefinito sia una stringa vuota, gli assembly di avvio dell'hosting includono sempre l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="ae54a-265">Quando vengono specificati, gli assembly di avvio dell'hosting vengono aggiunti all'assembly dell'app per essere caricati quando l'app compila i servizi comuni durante l'avvio.</span><span class="sxs-lookup"><span data-stu-id="ae54a-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="ae54a-266">**Chiave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="ae54a-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="ae54a-267">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-267">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-268">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="ae54a-268">**Default**: Empty string</span></span>  
<span data-ttu-id="ae54a-269">**Variabile di ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ae54a-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="ae54a-270">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="ae54a-271">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="ae54a-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="ae54a-272">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da escludere all'avvio.</span><span class="sxs-lookup"><span data-stu-id="ae54a-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="ae54a-273">**Chiave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="ae54a-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="ae54a-274">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-274">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-275">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="ae54a-275">**Default**: Empty string</span></span>  
<span data-ttu-id="ae54a-276">**Variabile di ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ae54a-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="ae54a-277">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="ae54a-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="ae54a-278">HTTPS_Port</span></span>

<span data-ttu-id="ae54a-279">Porta di reindirizzamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ae54a-279">The HTTPS redirect port.</span></span> <span data-ttu-id="ae54a-280">Usata per [imporre HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="ae54a-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="ae54a-281">**Chiave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="ae54a-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="ae54a-282">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-282">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-283">**Impostazione predefinita**: un valore predefinito non è impostato.</span><span class="sxs-lookup"><span data-stu-id="ae54a-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="ae54a-284">**Variabile di ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="ae54a-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="ae54a-285">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="ae54a-286">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="ae54a-286">PreferHostingUrls</span></span>

<span data-ttu-id="ae54a-287">Indica se l'host deve essere in ascolto sugli URL configurati con `IWebHostBuilder` anziché gli URL configurati con l' `IServer` implementazione di.</span><span class="sxs-lookup"><span data-stu-id="ae54a-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="ae54a-288">**Chiave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="ae54a-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="ae54a-289">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="ae54a-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ae54a-290">**Impostazione predefinita**: `true`</span><span class="sxs-lookup"><span data-stu-id="ae54a-290">**Default**: `true`</span></span>  
<span data-ttu-id="ae54a-291">**Variabile di ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="ae54a-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="ae54a-292">Per impostare questo valore, usare la variabile di ambiente o chiamare `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="ae54a-293">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="ae54a-293">PreventHostingStartup</span></span>

<span data-ttu-id="ae54a-294">Impedisce il caricamento automatico degli assembly di avvio dell'hosting, inclusi gli assembly di avvio dell'hosting configurati dall'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="ae54a-295">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="ae54a-296">**Chiave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="ae54a-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="ae54a-297">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="ae54a-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ae54a-298">**Impostazione predefinita**: `false`</span><span class="sxs-lookup"><span data-stu-id="ae54a-298">**Default**: `false`</span></span>  
<span data-ttu-id="ae54a-299">**Variabile di ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="ae54a-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="ae54a-300">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="ae54a-301">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="ae54a-301">StartupAssembly</span></span>

<span data-ttu-id="ae54a-302">L'assembly per la ricerca della classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="ae54a-303">**Chiave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="ae54a-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="ae54a-304">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-304">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-305">**Impostazione predefinita**: assembly dell'app</span><span class="sxs-lookup"><span data-stu-id="ae54a-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="ae54a-306">**Variabile di ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="ae54a-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="ae54a-307">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="ae54a-308">`UseStartup` può richiedere un nome di assembly (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="ae54a-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="ae54a-309">Se vengono chiamati più metodi `UseStartup`, l'ultimo metodo ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="ae54a-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="ae54a-310">URL</span><span class="sxs-lookup"><span data-stu-id="ae54a-310">URLs</span></span>

<span data-ttu-id="ae54a-311">Elenco delimitato da punto e virgola degli indirizzi IP o gli indirizzi host con le porte e protocolli su cui il server deve eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="ae54a-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="ae54a-312">Ad esempio: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="ae54a-313">Usare "\*" per indicare che il server deve eseguire l'ascolto per le richieste su tutti gli indirizzi IP o nomi host usando la porta e il protocollo specificati (ad esempio, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="ae54a-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="ae54a-314">Il protocollo (`http://` o `https://`) deve essere incluso con ogni URL.</span><span class="sxs-lookup"><span data-stu-id="ae54a-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="ae54a-315">I formati supportati variano a seconda del server.</span><span class="sxs-lookup"><span data-stu-id="ae54a-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="ae54a-316">**Chiave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="ae54a-316">**Key**: `urls`</span></span>  
<span data-ttu-id="ae54a-317">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-317">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-318">**Impostazione predefinita**: `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="ae54a-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="ae54a-319">**Variabile di ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="ae54a-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="ae54a-320">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="ae54a-321">Kestrel ha una propria API di configurazione degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="ae54a-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="ae54a-322">Per altre informazioni, vedere <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="ae54a-323">WebRoot</span><span class="sxs-lookup"><span data-stu-id="ae54a-323">WebRoot</span></span>

<span data-ttu-id="ae54a-324">La proprietà [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina il percorso relativo degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="ae54a-325">Se il percorso non esiste, viene usato un provider di file no-op.</span><span class="sxs-lookup"><span data-stu-id="ae54a-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="ae54a-326">**Chiave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="ae54a-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="ae54a-327">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-327">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-328">**Impostazione predefinita**: il valore predefinito è `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="ae54a-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="ae54a-329">Il percorso di *{radice del contenuto}/wwwroot* deve esistere.</span><span class="sxs-lookup"><span data-stu-id="ae54a-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="ae54a-330">**Variabile di ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="ae54a-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="ae54a-331">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseWebRoot` su `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="ae54a-332">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="ae54a-332">For more information, see:</span></span>

* [<span data-ttu-id="ae54a-333">Nozioni fondamentali: radice Web</span><span class="sxs-lookup"><span data-stu-id="ae54a-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="ae54a-334">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="ae54a-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="ae54a-335">Gestire la durata dell'host</span><span class="sxs-lookup"><span data-stu-id="ae54a-335">Manage the host lifetime</span></span>

<span data-ttu-id="ae54a-336">Chiamare metodi sull'implementazione <xref:Microsoft.Extensions.Hosting.IHost> incorporata per avviare e arrestare l'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="ae54a-337">Questi metodi influiscono su tutte le implementazioni <xref:Microsoft.Extensions.Hosting.IHostedService> che vengono registrate nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="ae54a-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="ae54a-338">Esegui</span><span class="sxs-lookup"><span data-stu-id="ae54a-338">Run</span></span>

<span data-ttu-id="ae54a-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> esegue l'app e blocca il thread di chiamata fino all'arresto dell'host.</span><span class="sxs-lookup"><span data-stu-id="ae54a-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="ae54a-340">RunAsync</span><span class="sxs-lookup"><span data-stu-id="ae54a-340">RunAsync</span></span>

<span data-ttu-id="ae54a-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> esegue l'app e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="ae54a-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="ae54a-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="ae54a-342">RunConsoleAsync</span></span>

<span data-ttu-id="ae54a-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Abilita il supporto della console, compila e avvia l'host e attende la <kbd>Ctrl</kbd> + chiusura di CTRL<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="ae54a-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="ae54a-344">Avvio</span><span class="sxs-lookup"><span data-stu-id="ae54a-344">Start</span></span>

<span data-ttu-id="ae54a-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> avvia l'host in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="ae54a-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="ae54a-346">StartAsync</span><span class="sxs-lookup"><span data-stu-id="ae54a-346">StartAsync</span></span>

<span data-ttu-id="ae54a-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> avvia l'host e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="ae54a-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="ae54a-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> viene chiamato all'avvio di `StartAsync`, che attende il completamento prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="ae54a-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="ae54a-349">Questo è utile per ritardare l'avvio fino al segnale trasmesso da un evento esterno.</span><span class="sxs-lookup"><span data-stu-id="ae54a-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="ae54a-350">StopAsync</span><span class="sxs-lookup"><span data-stu-id="ae54a-350">StopAsync</span></span>

<span data-ttu-id="ae54a-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> prova ad arrestare l'host entro il timeout specificato.</span><span class="sxs-lookup"><span data-stu-id="ae54a-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="ae54a-352">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="ae54a-352">WaitForShutdown</span></span>

<span data-ttu-id="ae54a-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blocca il thread chiamante finché l'arresto non viene attivato da IHostLifetime, ad esempio tramite <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="ae54a-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="ae54a-354">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="ae54a-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="ae54a-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato l'arresto tramite il token specificato, quindi chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="ae54a-356">Controllo esterno</span><span class="sxs-lookup"><span data-stu-id="ae54a-356">External control</span></span>

<span data-ttu-id="ae54a-357">Il controllo diretto della durata dell'host può essere ottenuto usando metodi che possono essere chiamati dall'esterno:</span><span class="sxs-lookup"><span data-stu-id="ae54a-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="ae54a-358">Le app ASP.NET Core configurano e avviano un host.</span><span class="sxs-lookup"><span data-stu-id="ae54a-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="ae54a-359">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="ae54a-360">Questo articolo illustra l'host generico di ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), che si usa per le app che non elaborano le richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae54a-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="ae54a-361">Lo scopo dell'host generico è separare la pipeline HTTP dall'API dell'host Web, per abilitare una gamma più ampia di scenari host.</span><span class="sxs-lookup"><span data-stu-id="ae54a-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="ae54a-362">La messaggistica, le attività in background e altri carichi di lavoro non HTTP basati sull'host generico traggono vantaggio dalle funzionalità a montaggio incrociato come la configurazione, l'inserimento di dipendenze (DI) e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="ae54a-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="ae54a-363">L'host generico è una nuova funzionalità introdotta in ASP.NET Core 2.1 e non è adatto agli scenari di hosting Web.</span><span class="sxs-lookup"><span data-stu-id="ae54a-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="ae54a-364">Per gli scenari di hosting Web usare l'[host Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="ae54a-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="ae54a-365">L'host generico sostituirà l'host Web in una versione futura, funzionando come API host principale negli scenari sia HTTP che non HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae54a-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="ae54a-366">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ae54a-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ae54a-367">Quando si esegue l'app di esempio in [Visual Studio Code](https://code.visualstudio.com/), usare un *terminale esterno o integrato*.</span><span class="sxs-lookup"><span data-stu-id="ae54a-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="ae54a-368">Non eseguire l'esempio in un ambiente `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="ae54a-369">Per impostare la console in Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="ae54a-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="ae54a-370">Aprire il file *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="ae54a-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="ae54a-371">Nella configurazione **.NET Core Launch (console)** trovare la voce **console**.</span><span class="sxs-lookup"><span data-stu-id="ae54a-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="ae54a-372">Impostare il valore su `externalTerminal` o `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="ae54a-373">Introduzione</span><span class="sxs-lookup"><span data-stu-id="ae54a-373">Introduction</span></span>

<span data-ttu-id="ae54a-374">La libreria host generico è disponibile nello spazio dei nomi <xref:Microsoft.Extensions.Hosting> e viene specificata dal pacchetto [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="ae54a-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="ae54a-375">Il pacchetto `Microsoft.Extensions.Hosting` è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 o versioni successive).</span><span class="sxs-lookup"><span data-stu-id="ae54a-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="ae54a-376"><xref:Microsoft.Extensions.Hosting.IHostedService> è il punto di ingresso per l'esecuzione del codice.</span><span class="sxs-lookup"><span data-stu-id="ae54a-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="ae54a-377">Ogni implementazione <xref:Microsoft.Extensions.Hosting.IHostedService> viene eseguita nell'ordine di [registrazione del servizio in ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="ae54a-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="ae54a-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> viene chiamato su ogni <xref:Microsoft.Extensions.Hosting.IHostedService> quando viene avviato l'host e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> viene chiamato in ordine di registrazione inverso quando l'host viene chiuso normalmente.</span><span class="sxs-lookup"><span data-stu-id="ae54a-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="ae54a-379">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="ae54a-379">Set up a host</span></span>

<span data-ttu-id="ae54a-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> è il componente principale che le librerie e le app usano per inizializzare, compilare ed eseguire l'host:</span><span class="sxs-lookup"><span data-stu-id="ae54a-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="ae54a-381">Opzioni</span><span class="sxs-lookup"><span data-stu-id="ae54a-381">Options</span></span>

<span data-ttu-id="ae54a-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configura le opzioni per <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="ae54a-383">Timeout di arresto</span><span class="sxs-lookup"><span data-stu-id="ae54a-383">Shutdown timeout</span></span>

<span data-ttu-id="ae54a-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> imposta il timeout per <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="ae54a-385">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="ae54a-385">The default value is five seconds.</span></span>

<span data-ttu-id="ae54a-386">La seguente configurazione di opzione in `Program.Main` aumenta il timeout predefinito di cinque secondi di chiusura a 20 secondi:</span><span class="sxs-lookup"><span data-stu-id="ae54a-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="ae54a-387">Servizi predefiniti</span><span class="sxs-lookup"><span data-stu-id="ae54a-387">Default services</span></span>

<span data-ttu-id="ae54a-388">I servizi seguenti vengono registrati durante l'inizializzazione dell'host:</span><span class="sxs-lookup"><span data-stu-id="ae54a-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="ae54a-389">[Ambiente](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="ae54a-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="ae54a-390">[Configurazione](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="ae54a-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="ae54a-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="ae54a-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="ae54a-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="ae54a-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="ae54a-393">[Opzioni](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="ae54a-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="ae54a-394">[Registrazione](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="ae54a-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="ae54a-395">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="ae54a-395">Host configuration</span></span>

<span data-ttu-id="ae54a-396">La configurazione dell'host viene creata tramite:</span><span class="sxs-lookup"><span data-stu-id="ae54a-396">Host configuration is created by:</span></span>

* <span data-ttu-id="ae54a-397">Chiamata ai metodi di estensione su <xref:Microsoft.Extensions.Hosting.IHostBuilder> per impostare la [radice del contenuto](#content-root) e l'[ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="ae54a-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="ae54a-398">Lettura della configurazione dai provider di configurazione in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="ae54a-399">Metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="ae54a-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="ae54a-400">Chiave applicazione (nome)</span><span class="sxs-lookup"><span data-stu-id="ae54a-400">Application key (name)</span></span>

<span data-ttu-id="ae54a-401">La proprietà [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) viene impostata dalla configurazione dell'host durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="ae54a-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="ae54a-402">Per impostare il valore in modo esplicito, usare [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="ae54a-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="ae54a-403">**Chiave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="ae54a-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="ae54a-404">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-404">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-405">**Impostazione predefinita**: il nome dell'assembly contenente il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="ae54a-406">**Imposta utilizzando**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="ae54a-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="ae54a-407">**Variabile di ambiente**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` è [facoltativo e definito dall'utente](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="ae54a-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="ae54a-408">Radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="ae54a-408">Content root</span></span>

<span data-ttu-id="ae54a-409">Questa impostazione determina la posizione da cui l'host inizia la ricerca dei file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="ae54a-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="ae54a-410">**Chiave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="ae54a-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="ae54a-411">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-411">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-412">**Impostazione predefinita**: il valore predefinito corrisponde alla cartella contenente l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="ae54a-413">**Imposta utilizzando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="ae54a-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="ae54a-414">**Variabile di ambiente**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` è [facoltativo e definito dall'utente](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="ae54a-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="ae54a-415">Se il percorso non esiste, l'host non verrà avviato.</span><span class="sxs-lookup"><span data-stu-id="ae54a-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="ae54a-416">Per altre informazioni, vedere [nozioni fondamentali: radice del contenuto](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="ae54a-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="ae54a-417">Environment</span><span class="sxs-lookup"><span data-stu-id="ae54a-417">Environment</span></span>

<span data-ttu-id="ae54a-418">Imposta l' [ambiente](xref:fundamentals/environments)dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="ae54a-419">**Chiave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="ae54a-419">**Key**: `environment`</span></span>  
<span data-ttu-id="ae54a-420">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-420">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-421">**Impostazione predefinita**: `Production`</span><span class="sxs-lookup"><span data-stu-id="ae54a-421">**Default**: `Production`</span></span>  
<span data-ttu-id="ae54a-422">**Imposta utilizzando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="ae54a-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="ae54a-423">**Variabile di ambiente**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` è [facoltativo e definito dall'utente](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="ae54a-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="ae54a-424">L'ambiente può essere impostato su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="ae54a-424">The environment can be set to any value.</span></span> <span data-ttu-id="ae54a-425">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="ae54a-426">I valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="ae54a-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="ae54a-427">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="ae54a-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="ae54a-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per creare un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfiguration> per l'host.</span><span class="sxs-lookup"><span data-stu-id="ae54a-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="ae54a-429">La configurazione dell'host viene usata per inizializzare l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> da usare nel processo di compilazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="ae54a-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="ae54a-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="ae54a-431">L'host usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="ae54a-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="ae54a-432">Nessun provider è incluso per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="ae54a-432">No providers are included by default.</span></span> <span data-ttu-id="ae54a-433">È necessario specificare in modo esplicito tutti i provider di configurazione richiesti dall'app in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, inclusi:</span><span class="sxs-lookup"><span data-stu-id="ae54a-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="ae54a-434">Configurazione dei file (ad esempio, da un file *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="ae54a-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="ae54a-435">Configurazione delle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="ae54a-435">Environment variable configuration.</span></span>
* <span data-ttu-id="ae54a-436">Configurazione degli argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="ae54a-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="ae54a-437">Tutti gli altri provider di configurazione necessari.</span><span class="sxs-lookup"><span data-stu-id="ae54a-437">Any other required configuration providers.</span></span>

<span data-ttu-id="ae54a-438">La configurazione file dell'host viene abilitata specificando il percorso di base dell'app con `SetBasePath` seguito da una chiamata a uno dei [provider di configurazione dei file](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ae54a-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="ae54a-439">L'app di esempio usa un file JSON, *hostsettings.json*, e chiama <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> per utilizzare le impostazioni di configurazione host del file.</span><span class="sxs-lookup"><span data-stu-id="ae54a-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="ae54a-440">Per aggiungere la [configurazione delle variabili di ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) dell'host, chiamare <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sul generatore di host.</span><span class="sxs-lookup"><span data-stu-id="ae54a-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="ae54a-441">`AddEnvironmentVariables` accetta un prefisso facoltativo definito dall'utente.</span><span class="sxs-lookup"><span data-stu-id="ae54a-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="ae54a-442">L'app di esempio usa un prefisso di `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="ae54a-443">Il prefisso viene rimosso quando vengono lette le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="ae54a-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="ae54a-444">Quando viene configurato l'host dell'app di esempio, il valore della variabile di ambiente per `PREFIX_ENVIRONMENT` diventa il valore di configurazione dell'host per la chiave `environment`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="ae54a-445">Durante lo sviluppo quando si usa [Visual Studio](https://visualstudio.microsoft.com) o si esegue un'app con `dotnet run`, le variabili di ambiente possono essere impostate nel file *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ae54a-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="ae54a-446">Durante lo sviluppo in [Visual Studio Code](https://code.visualstudio.com/), le variabili di ambiente possono essere impostate nel file *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="ae54a-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="ae54a-447">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="ae54a-448">La [configurazione della riga di comando](xref:fundamentals/configuration/index#command-line-configuration-provider) viene aggiunta chiamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="ae54a-449">La configurazione della riga di comando viene aggiunta per ultima per consentire agli argomenti della riga di comando di eseguire l'override della configurazione fornita dai provider di configurazione precedenti.</span><span class="sxs-lookup"><span data-stu-id="ae54a-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="ae54a-450">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ae54a-450">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="ae54a-451">È possibile fornire una configurazione aggiuntiva con le chiavi [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="ae54a-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="ae54a-452">Configurazione di esempio di `HostBuilder` che usa <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="ae54a-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="ae54a-453">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="ae54a-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="ae54a-454">La configurazione dell'app viene creata chiamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sull'implementazione <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="ae54a-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per creare un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfiguration> per l'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="ae54a-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="ae54a-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="ae54a-457">L'app usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="ae54a-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="ae54a-458">La configurazione creata da <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> è disponibile in [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) per le operazioni successive e in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="ae54a-459">La configurazione dell'app riceve automaticamente la configurazione dell'host fornita da [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="ae54a-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="ae54a-460">Configurazione app di esempio che usa <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="ae54a-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="ae54a-461">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="ae54a-461">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="ae54a-462">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="ae54a-462">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="ae54a-463">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="ae54a-463">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="ae54a-464">Per spostare i file delle impostazioni nella directory di output, specificare i file delle impostazioni come [elementi di progetto MSBuild](/visualstudio/msbuild/common-msbuild-project-items) nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="ae54a-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="ae54a-465">L'app di esempio sposta i file delle impostazioni dell'app JSON e *hostsettings.json* con l'elemento `<Content>` seguente:</span><span class="sxs-lookup"><span data-stu-id="ae54a-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="ae54a-466">I metodi di estensione di configurazione, ad esempio <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> e <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, necessitano di altri pacchetti NuGet, ad esempio [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) e [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="ae54a-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="ae54a-467">A meno che l'app non usi il [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), questi pacchetti devono essere aggiunti al progetto oltre al pacchetto [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) di base.</span><span class="sxs-lookup"><span data-stu-id="ae54a-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="ae54a-468">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="ae54a-469">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="ae54a-469">ConfigureServices</span></span>

<span data-ttu-id="ae54a-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> aggiunge servizi al contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ae54a-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="ae54a-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="ae54a-472">Un servizio ospitato è una classe con logica di attività in background che implementa l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="ae54a-473">Per altre informazioni, vedere <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="ae54a-474">L'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa il metodo di estensione `AddHostedService` per aggiungere all'app un servizio per gli eventi di durata (`LifetimeEventsHostedService`) e un'attività in background con timeout (`TimedHostedService`):</span><span class="sxs-lookup"><span data-stu-id="ae54a-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="ae54a-475">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="ae54a-475">ConfigureLogging</span></span>

<span data-ttu-id="ae54a-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> aggiunge un delegato per configurare l'interfaccia <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornita.</span><span class="sxs-lookup"><span data-stu-id="ae54a-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="ae54a-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="ae54a-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="ae54a-478">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="ae54a-478">UseConsoleLifetime</span></span>

<span data-ttu-id="ae54a-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>Ascolta la <kbd>combinazione di tasti CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM e chiama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> per avviare il processo di arresto.</span><span class="sxs-lookup"><span data-stu-id="ae54a-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="ae54a-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> Sblocca le estensioni, ad esempio [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="ae54a-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="ae54a-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` è preregistrata come implementazione di durata predefinita.</span><span class="sxs-lookup"><span data-stu-id="ae54a-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="ae54a-482">Viene usata l'ultima durata registrata.</span><span class="sxs-lookup"><span data-stu-id="ae54a-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="ae54a-483">Configurazione contenitore</span><span class="sxs-lookup"><span data-stu-id="ae54a-483">Container configuration</span></span>

<span data-ttu-id="ae54a-484">Per supportare l'inserimento di altri contenitori, l'host può accettare un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="ae54a-485">La disponibilità di una factory non fa parte della registrazione del contenitore DI, ma è una funzionalità intrinseca dell'host usata per creare il contenitore DI concreto.</span><span class="sxs-lookup"><span data-stu-id="ae54a-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="ae54a-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) esegue l'override della factory predefinita usata per la creazione del provider di servizi dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="ae54a-487">La configurazione del contenitore personalizzato è gestita dal metodo <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="ae54a-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> offre un'esperienza fortemente tipizzata per la configurazione del contenitore sulla base dell'API host sottostante.</span><span class="sxs-lookup"><span data-stu-id="ae54a-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="ae54a-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="ae54a-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="ae54a-490">Creare un contenitore di servizi per l'app:</span><span class="sxs-lookup"><span data-stu-id="ae54a-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="ae54a-491">Specificare una factory per il contenitore di servizi:</span><span class="sxs-lookup"><span data-stu-id="ae54a-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="ae54a-492">Usare la factory e configurare il contenitore di servizi personalizzato per l'app:</span><span class="sxs-lookup"><span data-stu-id="ae54a-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="ae54a-493">Estendibilità</span><span class="sxs-lookup"><span data-stu-id="ae54a-493">Extensibility</span></span>

<span data-ttu-id="ae54a-494">L'estensibilità host viene eseguita con i metodi di estensione su <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="ae54a-495">L'esempio seguente illustra come un metodo di estensione estende un'implementazione <xref:Microsoft.Extensions.Hosting.IHostBuilder> con l'esempio [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) dimostrato in <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="ae54a-496">Un'app stabilisce il metodo di estensione `UseHostedService` per registrare il servizio ospitato passato in `T`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="ae54a-497">Gestire l'host</span><span class="sxs-lookup"><span data-stu-id="ae54a-497">Manage the host</span></span>

<span data-ttu-id="ae54a-498">L'implementazione <xref:Microsoft.Extensions.Hosting.IHost> è responsabile dell'avvio e arresto delle implementazioni <xref:Microsoft.Extensions.Hosting.IHostedService> che sono registrate nel contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="ae54a-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="ae54a-499">Esegui</span><span class="sxs-lookup"><span data-stu-id="ae54a-499">Run</span></span>

<span data-ttu-id="ae54a-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> esegue l'app e blocca il thread di chiamata fino all'arresto dell'host:</span><span class="sxs-lookup"><span data-stu-id="ae54a-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="ae54a-501">RunAsync</span><span class="sxs-lookup"><span data-stu-id="ae54a-501">RunAsync</span></span>

<span data-ttu-id="ae54a-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> esegue l'app e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto:</span><span class="sxs-lookup"><span data-stu-id="ae54a-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="ae54a-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="ae54a-503">RunConsoleAsync</span></span>

<span data-ttu-id="ae54a-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Abilita il supporto della console, compila e avvia l'host e attende la <kbd>Ctrl</kbd> + chiusura di CTRL<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="ae54a-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="ae54a-505">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="ae54a-505">Start and StopAsync</span></span>

<span data-ttu-id="ae54a-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> avvia l'host in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="ae54a-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="ae54a-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> prova ad arrestare l'host entro il timeout specificato.</span><span class="sxs-lookup"><span data-stu-id="ae54a-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="ae54a-508">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="ae54a-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="ae54a-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> avvia l'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="ae54a-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> arresta l'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="ae54a-511">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="ae54a-511">WaitForShutdown</span></span>

<span data-ttu-id="ae54a-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>viene attivato tramite <xref:Microsoft.Extensions.Hosting.IHostLifetime> , ad esempio (in `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` attesa di <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="ae54a-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="ae54a-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="ae54a-514">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="ae54a-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="ae54a-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato l'arresto tramite il token specificato, quindi chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="ae54a-516">Controllo esterno</span><span class="sxs-lookup"><span data-stu-id="ae54a-516">External control</span></span>

<span data-ttu-id="ae54a-517">Il controllo esterno dell'host può essere ottenuto usando metodi che possono essere chiamati dall'esterno:</span><span class="sxs-lookup"><span data-stu-id="ae54a-517">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="ae54a-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> viene chiamato all'avvio di <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, che attende il completamento prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="ae54a-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="ae54a-519">Questo è utile per ritardare l'avvio fino al segnale trasmesso da un evento esterno.</span><span class="sxs-lookup"><span data-stu-id="ae54a-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="ae54a-520">Interfaccia IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="ae54a-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="ae54a-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> include informazioni sull'ambiente di hosting dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="ae54a-522">Usare l'[inserimento di un costruttore](xref:fundamentals/dependency-injection) per ottenere <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> per poterne usare le proprietà e i metodi di estensione:</span><span class="sxs-lookup"><span data-stu-id="ae54a-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="ae54a-523">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="ae54a-524">Interfaccia IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ae54a-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="ae54a-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> consente attività post-avvio e di arresto, incluse le richieste di arresto normale.</span><span class="sxs-lookup"><span data-stu-id="ae54a-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="ae54a-526">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi <xref:System.Action> che definiscono gli eventi di avvio e arresto.</span><span class="sxs-lookup"><span data-stu-id="ae54a-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="ae54a-527">Token di annullamento</span><span class="sxs-lookup"><span data-stu-id="ae54a-527">Cancellation Token</span></span> | <span data-ttu-id="ae54a-528">Attivato quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="ae54a-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="ae54a-529">L'host è stato completamente avviato.</span><span class="sxs-lookup"><span data-stu-id="ae54a-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="ae54a-530">L'host sta completando un arresto normale.</span><span class="sxs-lookup"><span data-stu-id="ae54a-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="ae54a-531">Tutte le richieste devono essere elaborate.</span><span class="sxs-lookup"><span data-stu-id="ae54a-531">All requests should be processed.</span></span> <span data-ttu-id="ae54a-532">L'arresto è bloccato fino al completamento di questo evento.</span><span class="sxs-lookup"><span data-stu-id="ae54a-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="ae54a-533">L'host sta eseguendo un arresto normale.</span><span class="sxs-lookup"><span data-stu-id="ae54a-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="ae54a-534">È possibile che le richieste siano ancora in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="ae54a-534">Requests may still be processing.</span></span> <span data-ttu-id="ae54a-535">L'arresto è bloccato fino al completamento di questo evento.</span><span class="sxs-lookup"><span data-stu-id="ae54a-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="ae54a-536">Inserire tramite costruttore il servizio <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> in qualsiasi classe.</span><span class="sxs-lookup"><span data-stu-id="ae54a-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="ae54a-537">L'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa l'inserimento di costruttori in una classe `LifetimeEventsHostedService` (implementazione <xref:Microsoft.Extensions.Hosting.IHostedService>) per registrare gli eventi.</span><span class="sxs-lookup"><span data-stu-id="ae54a-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="ae54a-538">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae54a-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="ae54a-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> richiede la chiusura dell'applicazione corrente.</span><span class="sxs-lookup"><span data-stu-id="ae54a-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="ae54a-540">La classe seguente usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> per arrestare normalmente un'app quando viene chiamato il metodo `Shutdown` della classe:</span><span class="sxs-lookup"><span data-stu-id="ae54a-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

<span data-ttu-id="ae54a-541">I modelli di ASP.NET Core creano un host generico .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="ae54a-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="ae54a-542">Definizione host</span><span class="sxs-lookup"><span data-stu-id="ae54a-542">Host definition</span></span>

<span data-ttu-id="ae54a-543">Un *host* è un oggetto che incapsula le risorse di un'app, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ae54a-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="ae54a-544">Inserimento di dipendenze (DI)</span><span class="sxs-lookup"><span data-stu-id="ae54a-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="ae54a-545">Registrazione</span><span class="sxs-lookup"><span data-stu-id="ae54a-545">Logging</span></span>
* <span data-ttu-id="ae54a-546">Configurazione</span><span class="sxs-lookup"><span data-stu-id="ae54a-546">Configuration</span></span>
* <span data-ttu-id="ae54a-547">`IHostedService` Implementazioni</span><span class="sxs-lookup"><span data-stu-id="ae54a-547">`IHostedService` implementations</span></span>

<span data-ttu-id="ae54a-548">Quando viene avviato, un host chiama <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> su ogni implementazione di <xref:Microsoft.Extensions.Hosting.IHostedService> registrata nella raccolta di servizi ospitati del contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="ae54a-548">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="ae54a-549">In un'app Web, una delle implementazioni di `IHostedService` è un servizio web che avvia un'[implementazione del server HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="ae54a-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="ae54a-550">Il motivo principale per cui tutte le risorse interdipendenti dell'app sono incluse in un unico oggetto è la gestione del ciclo di vita, vale a dire il controllo sull'avvio dell'app e sull'arresto normale.</span><span class="sxs-lookup"><span data-stu-id="ae54a-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="ae54a-551">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="ae54a-551">Set up a host</span></span>

<span data-ttu-id="ae54a-552">L'host è in genere configurato, compilato ed eseguito da codice nella classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="ae54a-553">Il metodo `Main`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-553">The `Main` method:</span></span>

* <span data-ttu-id="ae54a-554">Chiama un metodo `CreateHostBuilder` per creare e configurare un oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="ae54a-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="ae54a-555">Chiamate `Build` e metodi `Run` nell'oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="ae54a-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="ae54a-556">I modelli Web ASP.NET Core generano il codice seguente per creare un host:</span><span class="sxs-lookup"><span data-stu-id="ae54a-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="ae54a-557">Il codice seguente crea un carico di lavoro non HTTP con un' `IHostedService` implementazione aggiunta al contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="ae54a-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="ae54a-558">Per un carico di lavoro HTTP, il metodo `Main` è lo stesso ma `CreateHostBuilder` chiama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="ae54a-559">Se l'app usa Entity Framework Core, non modificare il nome o la firma del metodo `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="ae54a-560">Gli [strumenti di Entity Framework Core](/ef/core/miscellaneous/cli/) si aspettano di trovare un metodo `CreateHostBuilder` che configura l'host senza eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="ae54a-561">Per altre informazioni, vedere [Creazione DbContext in fase di progettazione](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="ae54a-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="ae54a-562">Impostazioni predefinite del generatore</span><span class="sxs-lookup"><span data-stu-id="ae54a-562">Default builder settings</span></span>

<span data-ttu-id="ae54a-563">Il metodo <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="ae54a-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="ae54a-564">Imposta la [radice del contenuto](xref:fundamentals/index#content-root) sul percorso restituito da <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="ae54a-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="ae54a-565">Carica la configurazione dell'host da:</span><span class="sxs-lookup"><span data-stu-id="ae54a-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="ae54a-566">Variabili di ambiente con prefisso `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="ae54a-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="ae54a-567">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="ae54a-567">Command-line arguments.</span></span>
* <span data-ttu-id="ae54a-568">Carica la configurazione dell'app da:</span><span class="sxs-lookup"><span data-stu-id="ae54a-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="ae54a-569">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="ae54a-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="ae54a-570">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="ae54a-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="ae54a-571">[Segreti del manager](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="ae54a-572">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="ae54a-572">Environment variables.</span></span>
  * <span data-ttu-id="ae54a-573">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="ae54a-573">Command-line arguments.</span></span>
* <span data-ttu-id="ae54a-574">Aggiunge i provider di [log](xref:fundamentals/logging/index) seguenti:</span><span class="sxs-lookup"><span data-stu-id="ae54a-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="ae54a-575">Console</span><span class="sxs-lookup"><span data-stu-id="ae54a-575">Console</span></span>
  * <span data-ttu-id="ae54a-576">Debug</span><span class="sxs-lookup"><span data-stu-id="ae54a-576">Debug</span></span>
  * <span data-ttu-id="ae54a-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="ae54a-577">EventSource</span></span>
  * <span data-ttu-id="ae54a-578">EventLog (solo quando è in esecuzione su Windows)</span><span class="sxs-lookup"><span data-stu-id="ae54a-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="ae54a-579">Abilita la [convalida dell'ambito](xref:fundamentals/dependency-injection#scope-validation) e la [convalida delle dipendenze](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando l'ambiente è lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="ae54a-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="ae54a-580">Il metodo `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="ae54a-581">Carica la configurazione host dalle variabili di ambiente con prefisso `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="ae54a-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="ae54a-582">Imposta il server [Kestrel](xref:fundamentals/servers/kestrel) come server Web e lo configura usando i provider di configurazione dell'host dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="ae54a-583">Per le opzioni predefinite del server Kestrel, vedere <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="ae54a-584">Aggiunge [il middleware dell'applicazione di filtri dell'host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ae54a-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="ae54a-585">Aggiunge il [middleware delle intestazioni con inoltri](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è uguale a `true` .</span><span class="sxs-lookup"><span data-stu-id="ae54a-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="ae54a-586">Abilita l'integrazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="ae54a-586">Enables IIS integration.</span></span> <span data-ttu-id="ae54a-587">Per le opzioni predefinite di IIS, vedere <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="ae54a-588">Le sezioni [Impostazioni per tutti i tipi di app](#settings-for-all-app-types) e [Impostazioni per le app Web](#settings-for-web-apps) più avanti in questo articolo mostrano come eseguire l'override delle impostazioni predefinite del generatore.</span><span class="sxs-lookup"><span data-stu-id="ae54a-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="ae54a-589">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="ae54a-589">Framework-provided services</span></span>

<span data-ttu-id="ae54a-590">I servizi seguenti vengono registrati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="ae54a-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="ae54a-591">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ae54a-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="ae54a-592">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="ae54a-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="ae54a-593">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="ae54a-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="ae54a-594">Per ulteriori informazioni sui servizi forniti dal Framework, vedere <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="ae54a-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="ae54a-595">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ae54a-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="ae54a-596">Inserire il servizio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (in precedenza `IApplicationLifetime`) in qualsiasi classe per gestire le attività post-avvio e di arresto normale.</span><span class="sxs-lookup"><span data-stu-id="ae54a-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="ae54a-597">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi del gestore dell'evento di avvio e di arresto.</span><span class="sxs-lookup"><span data-stu-id="ae54a-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="ae54a-598">L'interfaccia include anche un metodo `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="ae54a-599">L'esempio seguente è un' `IHostedService` implementazione di che registra `IHostApplicationLifetime` gli eventi:</span><span class="sxs-lookup"><span data-stu-id="ae54a-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="ae54a-600">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="ae54a-600">IHostLifetime</span></span>

<span data-ttu-id="ae54a-601">L'implementazione <xref:Microsoft.Extensions.Hosting.IHostLifetime> controlla quando l'host viene avviato e quando si arresta.</span><span class="sxs-lookup"><span data-stu-id="ae54a-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="ae54a-602">Viene usata l'ultima implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="ae54a-602">The last implementation registered is used.</span></span>

<span data-ttu-id="ae54a-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` è l'implementazione `IHostLifetime` predefinita.</span><span class="sxs-lookup"><span data-stu-id="ae54a-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="ae54a-604">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="ae54a-605">Ascolta la <kbd>combinazione di tasti CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM e chiama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> per avviare il processo di arresto.</span><span class="sxs-lookup"><span data-stu-id="ae54a-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="ae54a-606">Sblocca estensioni come [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="ae54a-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="ae54a-607">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="ae54a-607">IHostEnvironment</span></span>

<span data-ttu-id="ae54a-608">Inserire il <xref:Microsoft.Extensions.Hosting.IHostEnvironment> servizio in una classe per ottenere informazioni sulle impostazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="ae54a-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="ae54a-609">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="ae54a-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="ae54a-610">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="ae54a-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="ae54a-611">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="ae54a-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="ae54a-612">Le app Web implementano l' `IWebHostEnvironment` interfaccia, che eredita `IHostEnvironment` e aggiunge [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="ae54a-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="ae54a-613">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="ae54a-613">Host configuration</span></span>

<span data-ttu-id="ae54a-614">La configurazione dell'host viene usata per le proprietà dell'implementazione <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="ae54a-615">La configurazione dell'host è disponibile da [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) all'interno di <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="ae54a-616">Dopo `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` viene sostituito con la configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="ae54a-617">Per aggiungere la configurazione dell'host, chiamare <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="ae54a-618">`ConfigureHostConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="ae54a-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="ae54a-619">L'host usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="ae54a-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="ae54a-620">Il provider di variabili di ambiente con prefisso `DOTNET_` e argomenti della riga di comando sono inclusi in `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ae54a-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ae54a-621">Per le app Web, viene aggiunto il provider di variabili di ambiente con prefisso `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="ae54a-622">Il prefisso viene rimosso quando vengono lette le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="ae54a-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="ae54a-623">Ad esempio, il valore della variabile di ambiente per `ASPNETCORE_ENVIRONMENT` diventa il valore di configurazione dell'host per la chiave `environment`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="ae54a-624">L'esempio seguente crea la configurazione host:</span><span class="sxs-lookup"><span data-stu-id="ae54a-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="ae54a-625">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="ae54a-625">App configuration</span></span>

<span data-ttu-id="ae54a-626">La configurazione dell'app viene creata chiamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="ae54a-627">`ConfigureAppConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="ae54a-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="ae54a-628">L'app usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="ae54a-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="ae54a-629">La configurazione creata da `ConfigureAppConfiguration` è disponibile in [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) per le operazioni successive e come servizio da DI.</span><span class="sxs-lookup"><span data-stu-id="ae54a-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="ae54a-630">La configurazione dell'host viene aggiunta anche alla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="ae54a-631">Per altre informazioni, vedere [Configurazione in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="ae54a-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="ae54a-632">Impostazioni per tutti i tipi di app</span><span class="sxs-lookup"><span data-stu-id="ae54a-632">Settings for all app types</span></span>

<span data-ttu-id="ae54a-633">Questa sezione elenca le impostazioni dell'host che si applicano ai carichi di lavoro HTTP e non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae54a-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="ae54a-634">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="ae54a-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="ae54a-635">ApplicationName</span></span>

<span data-ttu-id="ae54a-636">La proprietà [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) viene impostata dalla configurazione dell'host durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="ae54a-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="ae54a-637">**Chiave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="ae54a-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="ae54a-638">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-638">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-639">**Impostazione predefinita**: il nome dell'assembly che contiene il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="ae54a-640">**Variabile di ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="ae54a-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="ae54a-641">per impostare questo valore usare la variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="ae54a-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="ae54a-642">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="ae54a-642">ContentRoot</span></span>

<span data-ttu-id="ae54a-643">La proprietà [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina il punto in cui l'host inizia a cercare i file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="ae54a-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="ae54a-644">Se il percorso non esiste, l'host non verrà avviato.</span><span class="sxs-lookup"><span data-stu-id="ae54a-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="ae54a-645">**Chiave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="ae54a-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="ae54a-646">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-646">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-647">**Impostazione predefinita**: la cartella in cui risiede l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="ae54a-648">**Variabile di ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="ae54a-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="ae54a-649">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseContentRoot` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="ae54a-650">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="ae54a-650">For more information, see:</span></span>

* [<span data-ttu-id="ae54a-651">Nozioni fondamentali: radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="ae54a-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="ae54a-652">WebRoot</span><span class="sxs-lookup"><span data-stu-id="ae54a-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="ae54a-653">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="ae54a-653">EnvironmentName</span></span>

<span data-ttu-id="ae54a-654">La proprietà [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) può essere impostata su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="ae54a-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="ae54a-655">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="ae54a-656">I valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="ae54a-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="ae54a-657">**Chiave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="ae54a-657">**Key**: `environment`</span></span>  
<span data-ttu-id="ae54a-658">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-658">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-659">**Impostazione predefinita**: `Production`</span><span class="sxs-lookup"><span data-stu-id="ae54a-659">**Default**: `Production`</span></span>  
<span data-ttu-id="ae54a-660">**Variabile di ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="ae54a-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="ae54a-661">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseEnvironment` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="ae54a-662">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="ae54a-662">ShutdownTimeout</span></span>

<span data-ttu-id="ae54a-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) imposta il timeout per <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="ae54a-664">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="ae54a-664">The default value is five seconds.</span></span>  <span data-ttu-id="ae54a-665">Durante il periodo di timeout, l'host:</span><span class="sxs-lookup"><span data-stu-id="ae54a-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="ae54a-666">attiva [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="ae54a-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="ae54a-667">Tenta di arrestare i servizi ospitati, registrando gli errori per i servizi che non si interrompono.</span><span class="sxs-lookup"><span data-stu-id="ae54a-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="ae54a-668">Se il periodo di timeout scade prima che siano stati arrestati tutti i servizi ospitati, gli eventuali servizi attivi rimanenti vengono interrotti quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="ae54a-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="ae54a-669">I servizi si arrestano anche se non hanno completato l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="ae54a-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="ae54a-670">Se l'arresto dei servizi richiede più tempo, aumentare il valore di timeout.</span><span class="sxs-lookup"><span data-stu-id="ae54a-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="ae54a-671">**Chiave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="ae54a-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="ae54a-672">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="ae54a-672">**Type**: `int`</span></span>  
<span data-ttu-id="ae54a-673">**Impostazione predefinita**: 5 secondi</span><span class="sxs-lookup"><span data-stu-id="ae54a-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="ae54a-674">**Variabile di ambiente**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="ae54a-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="ae54a-675">Per impostare questo valore, usare la variabile di ambiente o configurare `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="ae54a-676">Nell'esempio seguente il timeout viene impostato su 20 secondi:</span><span class="sxs-lookup"><span data-stu-id="ae54a-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="ae54a-677">Disabilitare il ricaricamento della configurazione dell'app durante la modifica</span><span class="sxs-lookup"><span data-stu-id="ae54a-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="ae54a-678">Per [impostazione predefinita](xref:fundamentals/configuration/index#default), *appsettings.json* e *appSettings. { L'ambiente}. JSON* viene ricaricato quando il file viene modificato.</span><span class="sxs-lookup"><span data-stu-id="ae54a-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="ae54a-679">Per disabilitare questo comportamento di ricaricamento in ASP.NET Core 5,0 Preview 3 o versione successiva, impostare la `hostBuilder:reloadConfigOnChange` chiave su `false` .</span><span class="sxs-lookup"><span data-stu-id="ae54a-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="ae54a-680">**Chiave**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="ae54a-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="ae54a-681">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="ae54a-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ae54a-682">**Impostazione predefinita**: `true`</span><span class="sxs-lookup"><span data-stu-id="ae54a-682">**Default**: `true`</span></span>  
<span data-ttu-id="ae54a-683">**Argomento della riga di comando**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="ae54a-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="ae54a-684">**Variabile di ambiente**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="ae54a-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="ae54a-685">Il separatore dei due punti ( `:` ) non funziona con le chiavi gerarchiche delle variabili di ambiente in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="ae54a-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="ae54a-686">Per altre informazioni, vedere [variabili di ambiente](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="ae54a-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="ae54a-687">Impostazioni per le app Web</span><span class="sxs-lookup"><span data-stu-id="ae54a-687">Settings for web apps</span></span>

<span data-ttu-id="ae54a-688">Alcune impostazioni host si applicano solo ai carichi di lavoro HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae54a-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="ae54a-689">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="ae54a-690">Per queste impostazioni sono disponibili i metodi di estensione su `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="ae54a-691">Gli esempi di codice che illustrano come chiamare i metodi di estensione presumono che `webBuilder` sia un'istanza di `IWebHostBuilder`, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="ae54a-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="ae54a-692">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="ae54a-692">CaptureStartupErrors</span></span>

<span data-ttu-id="ae54a-693">Quando è `false`, gli errori durante l'avvio causano la chiusura dell'host.</span><span class="sxs-lookup"><span data-stu-id="ae54a-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="ae54a-694">Quando è `true`, l'host acquisisce le eccezioni durante l'avvio e tenta di avviare il server.</span><span class="sxs-lookup"><span data-stu-id="ae54a-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="ae54a-695">**Chiave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="ae54a-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="ae54a-696">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="ae54a-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ae54a-697">**Impostazione predefinita**: il valore predefinito è `false` a meno che l'app non venga eseguita con Kestrel in IIS, in tal caso il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="ae54a-698">**Variabile di ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="ae54a-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="ae54a-699">Per impostare questo valore, usare la configurazione o chiamare `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="ae54a-700">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="ae54a-700">DetailedErrors</span></span>

<span data-ttu-id="ae54a-701">Quando è abilitata o quando l'ambiente è `Development`, l'app acquisisce gli errori dettagliati.</span><span class="sxs-lookup"><span data-stu-id="ae54a-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="ae54a-702">**Chiave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="ae54a-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="ae54a-703">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="ae54a-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ae54a-704">**Impostazione predefinita**: `false`</span><span class="sxs-lookup"><span data-stu-id="ae54a-704">**Default**: `false`</span></span>  
<span data-ttu-id="ae54a-705">**Variabile di ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="ae54a-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="ae54a-706">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="ae54a-707">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="ae54a-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="ae54a-708">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da caricare all'avvio.</span><span class="sxs-lookup"><span data-stu-id="ae54a-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="ae54a-709">Sebbene il valore di configurazione predefinito sia una stringa vuota, gli assembly di avvio dell'hosting includono sempre l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="ae54a-710">Quando vengono specificati, gli assembly di avvio dell'hosting vengono aggiunti all'assembly dell'app per essere caricati quando l'app compila i servizi comuni durante l'avvio.</span><span class="sxs-lookup"><span data-stu-id="ae54a-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="ae54a-711">**Chiave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="ae54a-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="ae54a-712">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-712">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-713">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="ae54a-713">**Default**: Empty string</span></span>  
<span data-ttu-id="ae54a-714">**Variabile di ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ae54a-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="ae54a-715">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="ae54a-716">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="ae54a-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="ae54a-717">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da escludere all'avvio.</span><span class="sxs-lookup"><span data-stu-id="ae54a-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="ae54a-718">**Chiave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="ae54a-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="ae54a-719">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-719">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-720">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="ae54a-720">**Default**: Empty string</span></span>  
<span data-ttu-id="ae54a-721">**Variabile di ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ae54a-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="ae54a-722">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="ae54a-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="ae54a-723">HTTPS_Port</span></span>

<span data-ttu-id="ae54a-724">Porta di reindirizzamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ae54a-724">The HTTPS redirect port.</span></span> <span data-ttu-id="ae54a-725">Usata per [imporre HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="ae54a-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="ae54a-726">**Chiave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="ae54a-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="ae54a-727">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-727">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-728">**Impostazione predefinita**: un valore predefinito non è impostato.</span><span class="sxs-lookup"><span data-stu-id="ae54a-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="ae54a-729">**Variabile di ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="ae54a-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="ae54a-730">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="ae54a-731">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="ae54a-731">PreferHostingUrls</span></span>

<span data-ttu-id="ae54a-732">Indica se l'host deve essere in ascolto sugli URL configurati con `IWebHostBuilder` anziché gli URL configurati con l' `IServer` implementazione di.</span><span class="sxs-lookup"><span data-stu-id="ae54a-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="ae54a-733">**Chiave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="ae54a-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="ae54a-734">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="ae54a-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ae54a-735">**Impostazione predefinita**: `true`</span><span class="sxs-lookup"><span data-stu-id="ae54a-735">**Default**: `true`</span></span>  
<span data-ttu-id="ae54a-736">**Variabile di ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="ae54a-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="ae54a-737">Per impostare questo valore, usare la variabile di ambiente o chiamare `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="ae54a-738">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="ae54a-738">PreventHostingStartup</span></span>

<span data-ttu-id="ae54a-739">Impedisce il caricamento automatico degli assembly di avvio dell'hosting, inclusi gli assembly di avvio dell'hosting configurati dall'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="ae54a-740">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="ae54a-741">**Chiave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="ae54a-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="ae54a-742">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="ae54a-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ae54a-743">**Impostazione predefinita**: `false`</span><span class="sxs-lookup"><span data-stu-id="ae54a-743">**Default**: `false`</span></span>  
<span data-ttu-id="ae54a-744">**Variabile di ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="ae54a-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="ae54a-745">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="ae54a-746">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="ae54a-746">StartupAssembly</span></span>

<span data-ttu-id="ae54a-747">L'assembly per la ricerca della classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="ae54a-748">**Chiave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="ae54a-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="ae54a-749">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-749">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-750">**Impostazione predefinita**: assembly dell'app</span><span class="sxs-lookup"><span data-stu-id="ae54a-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="ae54a-751">**Variabile di ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="ae54a-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="ae54a-752">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="ae54a-753">`UseStartup` può richiedere un nome di assembly (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="ae54a-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="ae54a-754">Se vengono chiamati più metodi `UseStartup`, l'ultimo metodo ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="ae54a-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="ae54a-755">URL</span><span class="sxs-lookup"><span data-stu-id="ae54a-755">URLs</span></span>

<span data-ttu-id="ae54a-756">Elenco delimitato da punto e virgola degli indirizzi IP o gli indirizzi host con le porte e protocolli su cui il server deve eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="ae54a-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="ae54a-757">Ad esempio: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="ae54a-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="ae54a-758">Usare "\*" per indicare che il server deve eseguire l'ascolto per le richieste su tutti gli indirizzi IP o nomi host usando la porta e il protocollo specificati (ad esempio, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="ae54a-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="ae54a-759">Il protocollo (`http://` o `https://`) deve essere incluso con ogni URL.</span><span class="sxs-lookup"><span data-stu-id="ae54a-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="ae54a-760">I formati supportati variano a seconda del server.</span><span class="sxs-lookup"><span data-stu-id="ae54a-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="ae54a-761">**Chiave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="ae54a-761">**Key**: `urls`</span></span>  
<span data-ttu-id="ae54a-762">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-762">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-763">**Impostazione predefinita**: `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="ae54a-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="ae54a-764">**Variabile di ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="ae54a-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="ae54a-765">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="ae54a-766">Kestrel ha una propria API di configurazione degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="ae54a-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="ae54a-767">Per altre informazioni, vedere <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="ae54a-768">WebRoot</span><span class="sxs-lookup"><span data-stu-id="ae54a-768">WebRoot</span></span>

<span data-ttu-id="ae54a-769">La proprietà [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina il percorso relativo degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="ae54a-770">Se il percorso non esiste, viene usato un provider di file no-op.</span><span class="sxs-lookup"><span data-stu-id="ae54a-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="ae54a-771">**Chiave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="ae54a-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="ae54a-772">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="ae54a-772">**Type**: `string`</span></span>  
<span data-ttu-id="ae54a-773">**Impostazione predefinita**: il valore predefinito è `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="ae54a-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="ae54a-774">Il percorso di *{radice del contenuto}/wwwroot* deve esistere.</span><span class="sxs-lookup"><span data-stu-id="ae54a-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="ae54a-775">**Variabile di ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="ae54a-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="ae54a-776">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseWebRoot` su `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ae54a-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="ae54a-777">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="ae54a-777">For more information, see:</span></span>

* [<span data-ttu-id="ae54a-778">Nozioni fondamentali: radice Web</span><span class="sxs-lookup"><span data-stu-id="ae54a-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="ae54a-779">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="ae54a-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="ae54a-780">Gestire la durata dell'host</span><span class="sxs-lookup"><span data-stu-id="ae54a-780">Manage the host lifetime</span></span>

<span data-ttu-id="ae54a-781">Chiamare metodi sull'implementazione <xref:Microsoft.Extensions.Hosting.IHost> incorporata per avviare e arrestare l'app.</span><span class="sxs-lookup"><span data-stu-id="ae54a-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="ae54a-782">Questi metodi influiscono su tutte le implementazioni <xref:Microsoft.Extensions.Hosting.IHostedService> che vengono registrate nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="ae54a-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="ae54a-783">Esegui</span><span class="sxs-lookup"><span data-stu-id="ae54a-783">Run</span></span>

<span data-ttu-id="ae54a-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> esegue l'app e blocca il thread di chiamata fino all'arresto dell'host.</span><span class="sxs-lookup"><span data-stu-id="ae54a-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="ae54a-785">RunAsync</span><span class="sxs-lookup"><span data-stu-id="ae54a-785">RunAsync</span></span>

<span data-ttu-id="ae54a-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> esegue l'app e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="ae54a-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="ae54a-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="ae54a-787">RunConsoleAsync</span></span>

<span data-ttu-id="ae54a-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Abilita il supporto della console, compila e avvia l'host e attende la <kbd>Ctrl</kbd> + chiusura di CTRL<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="ae54a-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="ae54a-789">Avvio</span><span class="sxs-lookup"><span data-stu-id="ae54a-789">Start</span></span>

<span data-ttu-id="ae54a-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> avvia l'host in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="ae54a-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="ae54a-791">StartAsync</span><span class="sxs-lookup"><span data-stu-id="ae54a-791">StartAsync</span></span>

<span data-ttu-id="ae54a-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> avvia l'host e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="ae54a-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="ae54a-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> viene chiamato all'avvio di `StartAsync`, che attende il completamento prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="ae54a-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="ae54a-794">Questo è utile per ritardare l'avvio fino al segnale trasmesso da un evento esterno.</span><span class="sxs-lookup"><span data-stu-id="ae54a-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="ae54a-795">StopAsync</span><span class="sxs-lookup"><span data-stu-id="ae54a-795">StopAsync</span></span>

<span data-ttu-id="ae54a-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> prova ad arrestare l'host entro il timeout specificato.</span><span class="sxs-lookup"><span data-stu-id="ae54a-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="ae54a-797">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="ae54a-797">WaitForShutdown</span></span>

<span data-ttu-id="ae54a-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blocca il thread chiamante finché l'arresto non viene attivato da IHostLifetime, ad esempio tramite <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="ae54a-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="ae54a-799">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="ae54a-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="ae54a-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato l'arresto tramite il token specificato, quindi chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ae54a-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="ae54a-801">Controllo esterno</span><span class="sxs-lookup"><span data-stu-id="ae54a-801">External control</span></span>

<span data-ttu-id="ae54a-802">Il controllo diretto della durata dell'host può essere ottenuto usando metodi che possono essere chiamati dall'esterno:</span><span class="sxs-lookup"><span data-stu-id="ae54a-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="ae54a-803">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="ae54a-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
