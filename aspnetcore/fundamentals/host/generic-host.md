---
title: Host generico .NET in ASP.NET Core
author: rick-anderson
description: Usare l'host generico di .NET Core nelle app ASP.NET Core.  L'host generico è responsabile della gestione dell'avvio e della durata delle app.
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
ms.openlocfilehash: d3de81ce7248372279b423da865513ee5db73c79
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762321"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="12a91-104">Host generico .NET in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="12a91-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="12a91-105">I modelli di ASP.NET Core creano un host generico .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="12a91-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="12a91-106">In questo argomento vengono fornite informazioni sull'utilizzo di host generico .NET in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="12a91-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="12a91-107">Per informazioni sull'uso di un host generico .NET nelle app console, vedere [.NET Generic Host](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="12a91-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="12a91-108">Definizione host</span><span class="sxs-lookup"><span data-stu-id="12a91-108">Host definition</span></span>

<span data-ttu-id="12a91-109">Un *host* è un oggetto che incapsula le risorse di un'app, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="12a91-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="12a91-110">Inserimento di dipendenze (DI)</span><span class="sxs-lookup"><span data-stu-id="12a91-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="12a91-111">Registrazione</span><span class="sxs-lookup"><span data-stu-id="12a91-111">Logging</span></span>
* <span data-ttu-id="12a91-112">Configurazione</span><span class="sxs-lookup"><span data-stu-id="12a91-112">Configuration</span></span>
* <span data-ttu-id="12a91-113">`IHostedService` Implementazioni</span><span class="sxs-lookup"><span data-stu-id="12a91-113">`IHostedService` implementations</span></span>

<span data-ttu-id="12a91-114">Quando viene avviato, un host chiama <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> su ogni implementazione di <xref:Microsoft.Extensions.Hosting.IHostedService> registrata nella raccolta di servizi ospitati del contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="12a91-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="12a91-115">In un'app Web, una delle implementazioni di `IHostedService` è un servizio web che avvia un'[implementazione del server HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="12a91-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="12a91-116">Il motivo principale per cui tutte le risorse interdipendenti dell'app sono incluse in un unico oggetto è la gestione del ciclo di vita, vale a dire il controllo sull'avvio dell'app e sull'arresto normale.</span><span class="sxs-lookup"><span data-stu-id="12a91-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="12a91-117">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="12a91-117">Set up a host</span></span>

<span data-ttu-id="12a91-118">L'host è in genere configurato, compilato ed eseguito da codice nella classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="12a91-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="12a91-119">Il metodo `Main`:</span><span class="sxs-lookup"><span data-stu-id="12a91-119">The `Main` method:</span></span>

* <span data-ttu-id="12a91-120">Chiama un metodo `CreateHostBuilder` per creare e configurare un oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="12a91-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="12a91-121">Chiamate `Build` e metodi `Run` nell'oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="12a91-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="12a91-122">I modelli Web ASP.NET Core generano il codice seguente per creare un host:</span><span class="sxs-lookup"><span data-stu-id="12a91-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="12a91-123">Il codice seguente crea un carico di lavoro non HTTP con un' `IHostedService` implementazione aggiunta al contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="12a91-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="12a91-124">Per un carico di lavoro HTTP, il metodo `Main` è lo stesso ma `CreateHostBuilder` chiama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="12a91-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="12a91-125">Se l'app usa Entity Framework Core, non modificare il nome o la firma del metodo `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="12a91-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="12a91-126">Gli [strumenti di Entity Framework Core](/ef/core/miscellaneous/cli/) si aspettano di trovare un metodo `CreateHostBuilder` che configura l'host senza eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="12a91-127">Per altre informazioni, vedere [Creazione DbContext in fase di progettazione](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="12a91-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="12a91-128">Impostazioni predefinite del generatore</span><span class="sxs-lookup"><span data-stu-id="12a91-128">Default builder settings</span></span>

<span data-ttu-id="12a91-129">Il metodo <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="12a91-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="12a91-130">Imposta la [radice del contenuto](xref:fundamentals/index#content-root) sul percorso restituito da <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="12a91-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="12a91-131">Carica la configurazione dell'host da:</span><span class="sxs-lookup"><span data-stu-id="12a91-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="12a91-132">Variabili di ambiente con prefisso `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="12a91-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="12a91-133">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="12a91-133">Command-line arguments.</span></span>
* <span data-ttu-id="12a91-134">Carica la configurazione dell'app da:</span><span class="sxs-lookup"><span data-stu-id="12a91-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="12a91-135">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="12a91-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="12a91-136">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="12a91-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="12a91-137">[Segreti del manager](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="12a91-137">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="12a91-138">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="12a91-138">Environment variables.</span></span>
  * <span data-ttu-id="12a91-139">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="12a91-139">Command-line arguments.</span></span>
* <span data-ttu-id="12a91-140">Aggiunge i provider di [log](xref:fundamentals/logging/index) seguenti:</span><span class="sxs-lookup"><span data-stu-id="12a91-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="12a91-141">Console</span><span class="sxs-lookup"><span data-stu-id="12a91-141">Console</span></span>
  * <span data-ttu-id="12a91-142">Debug</span><span class="sxs-lookup"><span data-stu-id="12a91-142">Debug</span></span>
  * <span data-ttu-id="12a91-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="12a91-143">EventSource</span></span>
  * <span data-ttu-id="12a91-144">EventLog (solo quando è in esecuzione su Windows)</span><span class="sxs-lookup"><span data-stu-id="12a91-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="12a91-145">Abilita la [convalida dell'ambito](xref:fundamentals/dependency-injection#scope-validation) e la [convalida delle dipendenze](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando l'ambiente è lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="12a91-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="12a91-146">Il metodo `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="12a91-146">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="12a91-147">Carica la configurazione host dalle variabili di ambiente con prefisso `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="12a91-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="12a91-148">Imposta il server [Kestrel](xref:fundamentals/servers/kestrel) come server Web e lo configura usando i provider di configurazione dell'host dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="12a91-149">Per le opzioni predefinite del server Kestrel, vedere <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="12a91-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="12a91-150">Aggiunge [il middleware dell'applicazione di filtri dell'host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="12a91-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="12a91-151">Aggiunge il [middleware delle intestazioni con inoltri](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è uguale a `true` .</span><span class="sxs-lookup"><span data-stu-id="12a91-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="12a91-152">Abilita l'integrazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="12a91-152">Enables IIS integration.</span></span> <span data-ttu-id="12a91-153">Per le opzioni predefinite di IIS, vedere <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="12a91-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="12a91-154">Le sezioni [Impostazioni per tutti i tipi di app](#settings-for-all-app-types) e [Impostazioni per le app Web](#settings-for-web-apps) più avanti in questo articolo mostrano come eseguire l'override delle impostazioni predefinite del generatore.</span><span class="sxs-lookup"><span data-stu-id="12a91-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="12a91-155">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="12a91-155">Framework-provided services</span></span>

<span data-ttu-id="12a91-156">I servizi seguenti vengono registrati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="12a91-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="12a91-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="12a91-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="12a91-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="12a91-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="12a91-159">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="12a91-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="12a91-160">Per ulteriori informazioni sui servizi forniti dal Framework, vedere <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="12a91-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="12a91-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="12a91-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="12a91-162">Inserire il servizio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (in precedenza `IApplicationLifetime`) in qualsiasi classe per gestire le attività post-avvio e di arresto normale.</span><span class="sxs-lookup"><span data-stu-id="12a91-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="12a91-163">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi del gestore dell'evento di avvio e di arresto.</span><span class="sxs-lookup"><span data-stu-id="12a91-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="12a91-164">L'interfaccia include anche un metodo `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="12a91-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="12a91-165">L'esempio seguente è un' `IHostedService` implementazione di che registra `IHostApplicationLifetime` gli eventi:</span><span class="sxs-lookup"><span data-stu-id="12a91-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="12a91-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="12a91-166">IHostLifetime</span></span>

<span data-ttu-id="12a91-167">L'implementazione <xref:Microsoft.Extensions.Hosting.IHostLifetime> controlla quando l'host viene avviato e quando si arresta.</span><span class="sxs-lookup"><span data-stu-id="12a91-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="12a91-168">Viene usata l'ultima implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="12a91-168">The last implementation registered is used.</span></span>

<span data-ttu-id="12a91-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` è l'implementazione `IHostLifetime` predefinita.</span><span class="sxs-lookup"><span data-stu-id="12a91-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="12a91-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="12a91-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="12a91-171">Ascolta la <kbd>combinazione di tasti CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM e chiama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> per avviare il processo di arresto.</span><span class="sxs-lookup"><span data-stu-id="12a91-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="12a91-172">Sblocca estensioni come [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="12a91-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="12a91-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="12a91-173">IHostEnvironment</span></span>

<span data-ttu-id="12a91-174">Inserire il <xref:Microsoft.Extensions.Hosting.IHostEnvironment> servizio in una classe per ottenere informazioni sulle impostazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="12a91-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="12a91-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="12a91-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="12a91-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="12a91-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="12a91-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="12a91-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="12a91-178">Le app Web implementano l' `IWebHostEnvironment` interfaccia, che eredita `IHostEnvironment` e aggiunge [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="12a91-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="12a91-179">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="12a91-179">Host configuration</span></span>

<span data-ttu-id="12a91-180">La configurazione dell'host viene usata per le proprietà dell'implementazione <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="12a91-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="12a91-181">La configurazione dell'host è disponibile da [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) all'interno di <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="12a91-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="12a91-182">Dopo `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` viene sostituito con la configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="12a91-183">Per aggiungere la configurazione dell'host, chiamare <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="12a91-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="12a91-184">`ConfigureHostConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="12a91-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="12a91-185">L'host usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="12a91-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="12a91-186">Il provider di variabili di ambiente con prefisso `DOTNET_` e argomenti della riga di comando sono inclusi in `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="12a91-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="12a91-187">Per le app Web, viene aggiunto il provider di variabili di ambiente con prefisso `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="12a91-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="12a91-188">Il prefisso viene rimosso quando vengono lette le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="12a91-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="12a91-189">Ad esempio, il valore della variabile di ambiente per `ASPNETCORE_ENVIRONMENT` diventa il valore di configurazione dell'host per la chiave `environment`.</span><span class="sxs-lookup"><span data-stu-id="12a91-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="12a91-190">L'esempio seguente crea la configurazione host:</span><span class="sxs-lookup"><span data-stu-id="12a91-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="12a91-191">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="12a91-191">App configuration</span></span>

<span data-ttu-id="12a91-192">La configurazione dell'app viene creata chiamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="12a91-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="12a91-193">`ConfigureAppConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="12a91-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="12a91-194">L'app usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="12a91-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="12a91-195">La configurazione creata da `ConfigureAppConfiguration` è disponibile in [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) per le operazioni successive e come servizio da DI.</span><span class="sxs-lookup"><span data-stu-id="12a91-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="12a91-196">La configurazione dell'host viene aggiunta anche alla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="12a91-197">Per altre informazioni, vedere [Configurazione in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="12a91-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="12a91-198">Impostazioni per tutti i tipi di app</span><span class="sxs-lookup"><span data-stu-id="12a91-198">Settings for all app types</span></span>

<span data-ttu-id="12a91-199">Questa sezione elenca le impostazioni dell'host che si applicano ai carichi di lavoro HTTP e non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="12a91-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="12a91-200">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="12a91-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="12a91-201">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="12a91-201">ApplicationName</span></span>

<span data-ttu-id="12a91-202">La proprietà [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) viene impostata dalla configurazione dell'host durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="12a91-202">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="12a91-203">**Chiave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="12a91-203">**Key**: `applicationName`</span></span>  
<span data-ttu-id="12a91-204">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-204">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-205">**Impostazione predefinita**: il nome dell'assembly che contiene il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-205">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="12a91-206">**Variabile di ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="12a91-206">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="12a91-207">per impostare questo valore usare la variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="12a91-207">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="12a91-208">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="12a91-208">ContentRoot</span></span>

<span data-ttu-id="12a91-209">La proprietà [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina il punto in cui l'host inizia a cercare i file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="12a91-209">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="12a91-210">Se il percorso non esiste, l'host non verrà avviato.</span><span class="sxs-lookup"><span data-stu-id="12a91-210">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="12a91-211">**Chiave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="12a91-211">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="12a91-212">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-212">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-213">**Impostazione predefinita**: la cartella in cui risiede l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-213">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="12a91-214">**Variabile di ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="12a91-214">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="12a91-215">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseContentRoot` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="12a91-215">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="12a91-216">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="12a91-216">For more information, see:</span></span>

* [<span data-ttu-id="12a91-217">Nozioni fondamentali: radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="12a91-217">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="12a91-218">WebRoot</span><span class="sxs-lookup"><span data-stu-id="12a91-218">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="12a91-219">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="12a91-219">EnvironmentName</span></span>

<span data-ttu-id="12a91-220">La proprietà [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) può essere impostata su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="12a91-220">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="12a91-221">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="12a91-221">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="12a91-222">I valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="12a91-222">Values aren't case-sensitive.</span></span>

<span data-ttu-id="12a91-223">**Chiave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="12a91-223">**Key**: `environment`</span></span>  
<span data-ttu-id="12a91-224">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-224">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-225">**Impostazione predefinita**: `Production`</span><span class="sxs-lookup"><span data-stu-id="12a91-225">**Default**: `Production`</span></span>  
<span data-ttu-id="12a91-226">**Variabile di ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="12a91-226">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="12a91-227">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseEnvironment` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="12a91-227">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="12a91-228">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="12a91-228">ShutdownTimeout</span></span>

<span data-ttu-id="12a91-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) imposta il timeout per <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="12a91-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="12a91-230">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="12a91-230">The default value is five seconds.</span></span>  <span data-ttu-id="12a91-231">Durante il periodo di timeout, l'host:</span><span class="sxs-lookup"><span data-stu-id="12a91-231">During the timeout period, the host:</span></span>

* <span data-ttu-id="12a91-232">attiva [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="12a91-232">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="12a91-233">Tenta di arrestare i servizi ospitati, registrando gli errori per i servizi che non si interrompono.</span><span class="sxs-lookup"><span data-stu-id="12a91-233">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="12a91-234">Se il periodo di timeout scade prima che siano stati arrestati tutti i servizi ospitati, gli eventuali servizi attivi rimanenti vengono interrotti quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="12a91-234">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="12a91-235">I servizi si arrestano anche se non hanno completato l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="12a91-235">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="12a91-236">Se l'arresto dei servizi richiede più tempo, aumentare il valore di timeout.</span><span class="sxs-lookup"><span data-stu-id="12a91-236">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="12a91-237">**Chiave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="12a91-237">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="12a91-238">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="12a91-238">**Type**: `int`</span></span>  
<span data-ttu-id="12a91-239">**Impostazione predefinita**: 5 secondi</span><span class="sxs-lookup"><span data-stu-id="12a91-239">**Default**: 5 seconds</span></span>  
<span data-ttu-id="12a91-240">**Variabile di ambiente**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="12a91-240">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="12a91-241">Per impostare questo valore, usare la variabile di ambiente o configurare `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="12a91-241">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="12a91-242">Nell'esempio seguente il timeout viene impostato su 20 secondi:</span><span class="sxs-lookup"><span data-stu-id="12a91-242">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="12a91-243">Disabilitare il ricaricamento della configurazione dell'app durante la modifica</span><span class="sxs-lookup"><span data-stu-id="12a91-243">Disable app configuration reload on change</span></span>

<span data-ttu-id="12a91-244">Per [impostazione predefinita](xref:fundamentals/configuration/index#default), *appsettings.json* e *appSettings. { L'ambiente}. JSON* viene ricaricato quando il file viene modificato.</span><span class="sxs-lookup"><span data-stu-id="12a91-244">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="12a91-245">Per disabilitare questo comportamento di ricaricamento in ASP.NET Core 5,0 o versione successiva, impostare la `hostBuilder:reloadConfigOnChange` chiave su `false` .</span><span class="sxs-lookup"><span data-stu-id="12a91-245">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="12a91-246">**Chiave**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="12a91-246">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="12a91-247">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="12a91-247">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="12a91-248">**Impostazione predefinita**: `true`</span><span class="sxs-lookup"><span data-stu-id="12a91-248">**Default**: `true`</span></span>  
<span data-ttu-id="12a91-249">**Argomento della riga di comando**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="12a91-249">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="12a91-250">**Variabile di ambiente**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="12a91-250">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="12a91-251">Il separatore dei due punti ( `:` ) non funziona con le chiavi gerarchiche delle variabili di ambiente in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="12a91-251">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="12a91-252">Per altre informazioni, vedere [variabili di ambiente](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="12a91-252">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="12a91-253">Impostazioni per le app Web</span><span class="sxs-lookup"><span data-stu-id="12a91-253">Settings for web apps</span></span>

<span data-ttu-id="12a91-254">Alcune impostazioni host si applicano solo ai carichi di lavoro HTTP.</span><span class="sxs-lookup"><span data-stu-id="12a91-254">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="12a91-255">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="12a91-255">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="12a91-256">Per queste impostazioni sono disponibili i metodi di estensione su `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="12a91-256">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="12a91-257">Gli esempi di codice che illustrano come chiamare i metodi di estensione presumono che `webBuilder` sia un'istanza di `IWebHostBuilder`, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="12a91-257">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="12a91-258">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="12a91-258">CaptureStartupErrors</span></span>

<span data-ttu-id="12a91-259">Quando è `false`, gli errori durante l'avvio causano la chiusura dell'host.</span><span class="sxs-lookup"><span data-stu-id="12a91-259">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="12a91-260">Quando è `true`, l'host acquisisce le eccezioni durante l'avvio e tenta di avviare il server.</span><span class="sxs-lookup"><span data-stu-id="12a91-260">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="12a91-261">**Chiave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="12a91-261">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="12a91-262">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="12a91-262">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="12a91-263">**Impostazione predefinita**: il valore predefinito è `false` a meno che l'app non venga eseguita con Kestrel in IIS, in tal caso il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="12a91-263">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="12a91-264">**Variabile di ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="12a91-264">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="12a91-265">Per impostare questo valore, usare la configurazione o chiamare `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="12a91-265">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="12a91-266">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="12a91-266">DetailedErrors</span></span>

<span data-ttu-id="12a91-267">Quando è abilitata o quando l'ambiente è `Development`, l'app acquisisce gli errori dettagliati.</span><span class="sxs-lookup"><span data-stu-id="12a91-267">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="12a91-268">**Chiave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="12a91-268">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="12a91-269">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="12a91-269">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="12a91-270">**Impostazione predefinita**: `false`</span><span class="sxs-lookup"><span data-stu-id="12a91-270">**Default**: `false`</span></span>  
<span data-ttu-id="12a91-271">**Variabile di ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="12a91-271">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="12a91-272">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12a91-272">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="12a91-273">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="12a91-273">HostingStartupAssemblies</span></span>

<span data-ttu-id="12a91-274">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da caricare all'avvio.</span><span class="sxs-lookup"><span data-stu-id="12a91-274">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="12a91-275">Sebbene il valore di configurazione predefinito sia una stringa vuota, gli assembly di avvio dell'hosting includono sempre l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-275">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="12a91-276">Quando vengono specificati, gli assembly di avvio dell'hosting vengono aggiunti all'assembly dell'app per essere caricati quando l'app compila i servizi comuni durante l'avvio.</span><span class="sxs-lookup"><span data-stu-id="12a91-276">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="12a91-277">**Chiave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="12a91-277">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="12a91-278">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-278">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-279">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="12a91-279">**Default**: Empty string</span></span>  
<span data-ttu-id="12a91-280">**Variabile di ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="12a91-280">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="12a91-281">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12a91-281">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="12a91-282">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="12a91-282">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="12a91-283">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da escludere all'avvio.</span><span class="sxs-lookup"><span data-stu-id="12a91-283">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="12a91-284">**Chiave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="12a91-284">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="12a91-285">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-285">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-286">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="12a91-286">**Default**: Empty string</span></span>  
<span data-ttu-id="12a91-287">**Variabile di ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="12a91-287">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="12a91-288">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12a91-288">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="12a91-289">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="12a91-289">HTTPS_Port</span></span>

<span data-ttu-id="12a91-290">Porta di reindirizzamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="12a91-290">The HTTPS redirect port.</span></span> <span data-ttu-id="12a91-291">Usata per [imporre HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="12a91-291">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="12a91-292">**Chiave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="12a91-292">**Key**: `https_port`</span></span>  
<span data-ttu-id="12a91-293">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-293">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-294">**Impostazione predefinita**: un valore predefinito non è impostato.</span><span class="sxs-lookup"><span data-stu-id="12a91-294">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="12a91-295">**Variabile di ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="12a91-295">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="12a91-296">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12a91-296">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="12a91-297">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="12a91-297">PreferHostingUrls</span></span>

<span data-ttu-id="12a91-298">Indica se l'host deve essere in ascolto sugli URL configurati con `IWebHostBuilder` anziché gli URL configurati con l' `IServer` implementazione di.</span><span class="sxs-lookup"><span data-stu-id="12a91-298">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="12a91-299">**Chiave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="12a91-299">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="12a91-300">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="12a91-300">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="12a91-301">**Impostazione predefinita**: `true`</span><span class="sxs-lookup"><span data-stu-id="12a91-301">**Default**: `true`</span></span>  
<span data-ttu-id="12a91-302">**Variabile di ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="12a91-302">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="12a91-303">Per impostare questo valore, usare la variabile di ambiente o chiamare `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="12a91-303">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="12a91-304">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="12a91-304">PreventHostingStartup</span></span>

<span data-ttu-id="12a91-305">Impedisce il caricamento automatico degli assembly di avvio dell'hosting, inclusi gli assembly di avvio dell'hosting configurati dall'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-305">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="12a91-306">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="12a91-306">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="12a91-307">**Chiave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="12a91-307">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="12a91-308">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="12a91-308">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="12a91-309">**Impostazione predefinita**: `false`</span><span class="sxs-lookup"><span data-stu-id="12a91-309">**Default**: `false`</span></span>  
<span data-ttu-id="12a91-310">**Variabile di ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="12a91-310">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="12a91-311">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12a91-311">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="12a91-312">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="12a91-312">StartupAssembly</span></span>

<span data-ttu-id="12a91-313">L'assembly per la ricerca della classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="12a91-313">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="12a91-314">**Chiave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="12a91-314">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="12a91-315">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-315">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-316">**Impostazione predefinita**: assembly dell'app</span><span class="sxs-lookup"><span data-stu-id="12a91-316">**Default**: The app's assembly</span></span>  
<span data-ttu-id="12a91-317">**Variabile di ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="12a91-317">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="12a91-318">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="12a91-318">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="12a91-319">`UseStartup` può richiedere un nome di assembly (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="12a91-319">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="12a91-320">Se vengono chiamati più metodi `UseStartup`, l'ultimo metodo ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="12a91-320">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="12a91-321">URL</span><span class="sxs-lookup"><span data-stu-id="12a91-321">URLs</span></span>

<span data-ttu-id="12a91-322">Elenco delimitato da punto e virgola degli indirizzi IP o gli indirizzi host con le porte e protocolli su cui il server deve eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="12a91-322">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="12a91-323">Ad esempio: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="12a91-323">For example, `http://localhost:123`.</span></span> <span data-ttu-id="12a91-324">Usare "\*" per indicare che il server deve eseguire l'ascolto per le richieste su tutti gli indirizzi IP o nomi host usando la porta e il protocollo specificati (ad esempio, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="12a91-324">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="12a91-325">Il protocollo (`http://` o `https://`) deve essere incluso con ogni URL.</span><span class="sxs-lookup"><span data-stu-id="12a91-325">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="12a91-326">I formati supportati variano a seconda del server.</span><span class="sxs-lookup"><span data-stu-id="12a91-326">Supported formats vary among servers.</span></span>

<span data-ttu-id="12a91-327">**Chiave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="12a91-327">**Key**: `urls`</span></span>  
<span data-ttu-id="12a91-328">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-328">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-329">**Impostazione predefinita**: `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="12a91-329">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="12a91-330">**Variabile di ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="12a91-330">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="12a91-331">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="12a91-331">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="12a91-332">Kestrel ha una propria API di configurazione degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="12a91-332">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="12a91-333">Per altre informazioni, vedere <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="12a91-333">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="12a91-334">WebRoot</span><span class="sxs-lookup"><span data-stu-id="12a91-334">WebRoot</span></span>

<span data-ttu-id="12a91-335">La proprietà [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina il percorso relativo degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-335">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="12a91-336">Se il percorso non esiste, viene usato un provider di file no-op.</span><span class="sxs-lookup"><span data-stu-id="12a91-336">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="12a91-337">**Chiave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="12a91-337">**Key**: `webroot`</span></span>  
<span data-ttu-id="12a91-338">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-338">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-339">**Impostazione predefinita**: il valore predefinito è `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="12a91-339">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="12a91-340">Il percorso di *{radice del contenuto}/wwwroot* deve esistere.</span><span class="sxs-lookup"><span data-stu-id="12a91-340">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="12a91-341">**Variabile di ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="12a91-341">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="12a91-342">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseWebRoot` su `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="12a91-342">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="12a91-343">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="12a91-343">For more information, see:</span></span>

* [<span data-ttu-id="12a91-344">Nozioni fondamentali: radice Web</span><span class="sxs-lookup"><span data-stu-id="12a91-344">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="12a91-345">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="12a91-345">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="12a91-346">Gestire la durata dell'host</span><span class="sxs-lookup"><span data-stu-id="12a91-346">Manage the host lifetime</span></span>

<span data-ttu-id="12a91-347">Chiamare metodi sull'implementazione <xref:Microsoft.Extensions.Hosting.IHost> incorporata per avviare e arrestare l'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-347">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="12a91-348">Questi metodi influiscono su tutte le implementazioni <xref:Microsoft.Extensions.Hosting.IHostedService> che vengono registrate nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="12a91-348">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="12a91-349">Esegui</span><span class="sxs-lookup"><span data-stu-id="12a91-349">Run</span></span>

<span data-ttu-id="12a91-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> esegue l'app e blocca il thread di chiamata fino all'arresto dell'host.</span><span class="sxs-lookup"><span data-stu-id="12a91-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="12a91-351">RunAsync</span><span class="sxs-lookup"><span data-stu-id="12a91-351">RunAsync</span></span>

<span data-ttu-id="12a91-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> esegue l'app e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="12a91-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="12a91-353">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="12a91-353">RunConsoleAsync</span></span>

<span data-ttu-id="12a91-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Abilita il supporto della console, compila e avvia l'host e attende la <kbd>Ctrl</kbd> + chiusura di CTRL<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="12a91-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="12a91-355">Avvio</span><span class="sxs-lookup"><span data-stu-id="12a91-355">Start</span></span>

<span data-ttu-id="12a91-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> avvia l'host in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="12a91-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="12a91-357">StartAsync</span><span class="sxs-lookup"><span data-stu-id="12a91-357">StartAsync</span></span>

<span data-ttu-id="12a91-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> avvia l'host e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="12a91-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="12a91-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> viene chiamato all'avvio di `StartAsync`, che attende il completamento prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="12a91-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="12a91-360">Questo è utile per ritardare l'avvio fino al segnale trasmesso da un evento esterno.</span><span class="sxs-lookup"><span data-stu-id="12a91-360">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="12a91-361">StopAsync</span><span class="sxs-lookup"><span data-stu-id="12a91-361">StopAsync</span></span>

<span data-ttu-id="12a91-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> prova ad arrestare l'host entro il timeout specificato.</span><span class="sxs-lookup"><span data-stu-id="12a91-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="12a91-363">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="12a91-363">WaitForShutdown</span></span>

<span data-ttu-id="12a91-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blocca il thread chiamante finché l'arresto non viene attivato da IHostLifetime, ad esempio tramite <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="12a91-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="12a91-365">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="12a91-365">WaitForShutdownAsync</span></span>

<span data-ttu-id="12a91-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato l'arresto tramite il token specificato, quindi chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="12a91-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="12a91-367">Controllo esterno</span><span class="sxs-lookup"><span data-stu-id="12a91-367">External control</span></span>

<span data-ttu-id="12a91-368">Il controllo diretto della durata dell'host può essere ottenuto usando metodi che possono essere chiamati dall'esterno:</span><span class="sxs-lookup"><span data-stu-id="12a91-368">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="12a91-369">I modelli di ASP.NET Core creano un host generico .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="12a91-369">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="12a91-370">In questo argomento vengono fornite informazioni sull'utilizzo di host generico .NET in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="12a91-370">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="12a91-371">Per informazioni sull'uso di un host generico .NET nelle app console, vedere [.NET Generic Host](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="12a91-371">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="12a91-372">Definizione host</span><span class="sxs-lookup"><span data-stu-id="12a91-372">Host definition</span></span>

<span data-ttu-id="12a91-373">Un *host* è un oggetto che incapsula le risorse di un'app, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="12a91-373">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="12a91-374">Inserimento di dipendenze (DI)</span><span class="sxs-lookup"><span data-stu-id="12a91-374">Dependency injection (DI)</span></span>
* <span data-ttu-id="12a91-375">Registrazione</span><span class="sxs-lookup"><span data-stu-id="12a91-375">Logging</span></span>
* <span data-ttu-id="12a91-376">Configurazione</span><span class="sxs-lookup"><span data-stu-id="12a91-376">Configuration</span></span>
* <span data-ttu-id="12a91-377">`IHostedService` Implementazioni</span><span class="sxs-lookup"><span data-stu-id="12a91-377">`IHostedService` implementations</span></span>

<span data-ttu-id="12a91-378">Quando viene avviato, un host chiama <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> su ogni implementazione di <xref:Microsoft.Extensions.Hosting.IHostedService> registrata nella raccolta di servizi ospitati del contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="12a91-378">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="12a91-379">In un'app Web, una delle implementazioni di `IHostedService` è un servizio web che avvia un'[implementazione del server HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="12a91-379">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="12a91-380">Il motivo principale per cui tutte le risorse interdipendenti dell'app sono incluse in un unico oggetto è la gestione del ciclo di vita, vale a dire il controllo sull'avvio dell'app e sull'arresto normale.</span><span class="sxs-lookup"><span data-stu-id="12a91-380">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="12a91-381">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="12a91-381">Set up a host</span></span>

<span data-ttu-id="12a91-382">L'host è in genere configurato, compilato ed eseguito da codice nella classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="12a91-382">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="12a91-383">Il metodo `Main`:</span><span class="sxs-lookup"><span data-stu-id="12a91-383">The `Main` method:</span></span>

* <span data-ttu-id="12a91-384">Chiama un metodo `CreateHostBuilder` per creare e configurare un oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="12a91-384">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="12a91-385">Chiamate `Build` e metodi `Run` nell'oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="12a91-385">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="12a91-386">I modelli Web ASP.NET Core generano il codice seguente per creare un host generico:</span><span class="sxs-lookup"><span data-stu-id="12a91-386">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="12a91-387">Il codice seguente crea un host generico utilizzando un carico di lavoro non HTTP.</span><span class="sxs-lookup"><span data-stu-id="12a91-387">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="12a91-388">L' `IHostedService` implementazione viene aggiunta al contenitore DI di:</span><span class="sxs-lookup"><span data-stu-id="12a91-388">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="12a91-389">Per un carico di lavoro HTTP, il metodo `Main` è lo stesso ma `CreateHostBuilder` chiama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="12a91-389">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="12a91-390">Il codice precedente viene generato dai modelli di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="12a91-390">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="12a91-391">Se l'app usa Entity Framework Core, non modificare il nome o la firma del metodo `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="12a91-391">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="12a91-392">Gli [strumenti di Entity Framework Core](/ef/core/miscellaneous/cli/) si aspettano di trovare un metodo `CreateHostBuilder` che configura l'host senza eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-392">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="12a91-393">Per altre informazioni, vedere [Creazione DbContext in fase di progettazione](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="12a91-393">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="12a91-394">Impostazioni predefinite del generatore</span><span class="sxs-lookup"><span data-stu-id="12a91-394">Default builder settings</span></span>

<span data-ttu-id="12a91-395">Il metodo <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>:</span><span class="sxs-lookup"><span data-stu-id="12a91-395">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="12a91-396">Imposta la [radice del contenuto](xref:fundamentals/index#content-root) sul percorso restituito da <xref:System.IO.Directory.GetCurrentDirectory%2A> .</span><span class="sxs-lookup"><span data-stu-id="12a91-396">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="12a91-397">Carica la configurazione dell'host da:</span><span class="sxs-lookup"><span data-stu-id="12a91-397">Loads host configuration from:</span></span>
  * <span data-ttu-id="12a91-398">Variabili di ambiente con prefisso `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="12a91-398">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="12a91-399">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="12a91-399">Command-line arguments.</span></span>
* <span data-ttu-id="12a91-400">Carica la configurazione dell'app da:</span><span class="sxs-lookup"><span data-stu-id="12a91-400">Loads app configuration from:</span></span>
  * <span data-ttu-id="12a91-401">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="12a91-401">*appsettings.json*.</span></span>
  * <span data-ttu-id="12a91-402">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="12a91-402">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="12a91-403">[Segreti del manager](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="12a91-403">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="12a91-404">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="12a91-404">Environment variables.</span></span>
  * <span data-ttu-id="12a91-405">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="12a91-405">Command-line arguments.</span></span>
* <span data-ttu-id="12a91-406">Aggiunge i provider di [log](xref:fundamentals/logging/index) seguenti:</span><span class="sxs-lookup"><span data-stu-id="12a91-406">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="12a91-407">Console</span><span class="sxs-lookup"><span data-stu-id="12a91-407">Console</span></span>
  * <span data-ttu-id="12a91-408">Debug</span><span class="sxs-lookup"><span data-stu-id="12a91-408">Debug</span></span>
  * <span data-ttu-id="12a91-409">EventSource</span><span class="sxs-lookup"><span data-stu-id="12a91-409">EventSource</span></span>
  * <span data-ttu-id="12a91-410">EventLog (solo quando è in esecuzione su Windows)</span><span class="sxs-lookup"><span data-stu-id="12a91-410">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="12a91-411">Abilita la [convalida dell'ambito](xref:fundamentals/dependency-injection#scope-validation) e la [convalida delle dipendenze](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando l'ambiente è lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="12a91-411">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="12a91-412">Il metodo `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="12a91-412">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="12a91-413">Carica la configurazione host dalle variabili di ambiente con prefisso `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="12a91-413">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="12a91-414">Imposta il server [Kestrel](xref:fundamentals/servers/kestrel) come server Web e lo configura usando i provider di configurazione dell'host dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-414">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="12a91-415">Per le opzioni predefinite del server Kestrel, vedere <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="12a91-415">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="12a91-416">Aggiunge [il middleware dell'applicazione di filtri dell'host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="12a91-416">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="12a91-417">Aggiunge il [middleware delle intestazioni con inoltri](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è uguale a `true` .</span><span class="sxs-lookup"><span data-stu-id="12a91-417">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="12a91-418">Abilita l'integrazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="12a91-418">Enables IIS integration.</span></span> <span data-ttu-id="12a91-419">Per le opzioni predefinite di IIS, vedere <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="12a91-419">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="12a91-420">Le sezioni [Impostazioni per tutti i tipi di app](#settings-for-all-app-types) e [Impostazioni per le app Web](#settings-for-web-apps) più avanti in questo articolo mostrano come eseguire l'override delle impostazioni predefinite del generatore.</span><span class="sxs-lookup"><span data-stu-id="12a91-420">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="12a91-421">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="12a91-421">Framework-provided services</span></span>

<span data-ttu-id="12a91-422">I servizi seguenti vengono registrati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="12a91-422">The following services are registered automatically:</span></span>

* [<span data-ttu-id="12a91-423">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="12a91-423">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="12a91-424">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="12a91-424">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="12a91-425">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="12a91-425">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="12a91-426">Per ulteriori informazioni sui servizi forniti dal Framework, vedere <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="12a91-426">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="12a91-427">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="12a91-427">IHostApplicationLifetime</span></span>

<span data-ttu-id="12a91-428">Inserire il servizio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (in precedenza `IApplicationLifetime`) in qualsiasi classe per gestire le attività post-avvio e di arresto normale.</span><span class="sxs-lookup"><span data-stu-id="12a91-428">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="12a91-429">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi del gestore dell'evento di avvio e di arresto.</span><span class="sxs-lookup"><span data-stu-id="12a91-429">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="12a91-430">L'interfaccia include anche un metodo `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="12a91-430">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="12a91-431">L'esempio seguente è un' `IHostedService` implementazione di che registra `IHostApplicationLifetime` gli eventi:</span><span class="sxs-lookup"><span data-stu-id="12a91-431">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="12a91-432">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="12a91-432">IHostLifetime</span></span>

<span data-ttu-id="12a91-433">L'implementazione <xref:Microsoft.Extensions.Hosting.IHostLifetime> controlla quando l'host viene avviato e quando si arresta.</span><span class="sxs-lookup"><span data-stu-id="12a91-433">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="12a91-434">Viene usata l'ultima implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="12a91-434">The last implementation registered is used.</span></span>

<span data-ttu-id="12a91-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` è l'implementazione `IHostLifetime` predefinita.</span><span class="sxs-lookup"><span data-stu-id="12a91-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="12a91-436">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="12a91-436">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="12a91-437">Ascolta la <kbd>combinazione di tasti CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM e chiama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> per avviare il processo di arresto.</span><span class="sxs-lookup"><span data-stu-id="12a91-437">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="12a91-438">Sblocca estensioni come [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="12a91-438">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="12a91-439">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="12a91-439">IHostEnvironment</span></span>

<span data-ttu-id="12a91-440">Inserire il <xref:Microsoft.Extensions.Hosting.IHostEnvironment> servizio in una classe per ottenere informazioni sulle impostazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="12a91-440">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="12a91-441">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="12a91-441">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="12a91-442">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="12a91-442">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="12a91-443">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="12a91-443">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="12a91-444">Le app Web implementano l' `IWebHostEnvironment` interfaccia, che eredita `IHostEnvironment` e aggiunge [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="12a91-444">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="12a91-445">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="12a91-445">Host configuration</span></span>

<span data-ttu-id="12a91-446">La configurazione dell'host viene usata per le proprietà dell'implementazione <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="12a91-446">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="12a91-447">La configurazione dell'host è disponibile da [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) all'interno di <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span><span class="sxs-lookup"><span data-stu-id="12a91-447">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="12a91-448">Dopo `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` viene sostituito con la configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-448">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="12a91-449">Per aggiungere la configurazione dell'host, chiamare <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="12a91-449">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="12a91-450">`ConfigureHostConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="12a91-450">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="12a91-451">L'host usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="12a91-451">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="12a91-452">Il provider di variabili di ambiente con prefisso `DOTNET_` e argomenti della riga di comando sono inclusi in `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="12a91-452">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="12a91-453">Per le app Web, viene aggiunto il provider di variabili di ambiente con prefisso `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="12a91-453">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="12a91-454">Il prefisso viene rimosso quando vengono lette le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="12a91-454">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="12a91-455">Ad esempio, il valore della variabile di ambiente per `ASPNETCORE_ENVIRONMENT` diventa il valore di configurazione dell'host per la chiave `environment`.</span><span class="sxs-lookup"><span data-stu-id="12a91-455">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="12a91-456">L'esempio seguente crea la configurazione host:</span><span class="sxs-lookup"><span data-stu-id="12a91-456">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="12a91-457">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="12a91-457">App configuration</span></span>

<span data-ttu-id="12a91-458">La configurazione dell'app viene creata chiamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="12a91-458">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="12a91-459">`ConfigureAppConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="12a91-459">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="12a91-460">L'app usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="12a91-460">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="12a91-461">La configurazione creata da `ConfigureAppConfiguration` è disponibile in [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) per le operazioni successive e come servizio da DI.</span><span class="sxs-lookup"><span data-stu-id="12a91-461">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="12a91-462">La configurazione dell'host viene aggiunta anche alla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-462">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="12a91-463">Per altre informazioni, vedere [Configurazione in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="12a91-463">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="12a91-464">Impostazioni per tutti i tipi di app</span><span class="sxs-lookup"><span data-stu-id="12a91-464">Settings for all app types</span></span>

<span data-ttu-id="12a91-465">Questa sezione elenca le impostazioni dell'host che si applicano ai carichi di lavoro HTTP e non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="12a91-465">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="12a91-466">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="12a91-466">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="12a91-467">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="12a91-467">ApplicationName</span></span>

<span data-ttu-id="12a91-468">La proprietà [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) viene impostata dalla configurazione dell'host durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="12a91-468">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="12a91-469">**Chiave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="12a91-469">**Key**: `applicationName`</span></span>  
<span data-ttu-id="12a91-470">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-470">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-471">**Impostazione predefinita**: il nome dell'assembly che contiene il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-471">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="12a91-472">**Variabile di ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="12a91-472">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="12a91-473">per impostare questo valore usare la variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="12a91-473">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="12a91-474">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="12a91-474">ContentRoot</span></span>

<span data-ttu-id="12a91-475">La proprietà [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina il punto in cui l'host inizia a cercare i file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="12a91-475">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="12a91-476">Se il percorso non esiste, l'host non verrà avviato.</span><span class="sxs-lookup"><span data-stu-id="12a91-476">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="12a91-477">**Chiave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="12a91-477">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="12a91-478">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-478">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-479">**Impostazione predefinita**: la cartella in cui risiede l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-479">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="12a91-480">**Variabile di ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="12a91-480">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="12a91-481">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseContentRoot` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="12a91-481">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="12a91-482">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="12a91-482">For more information, see:</span></span>

* [<span data-ttu-id="12a91-483">Nozioni fondamentali: radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="12a91-483">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="12a91-484">WebRoot</span><span class="sxs-lookup"><span data-stu-id="12a91-484">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="12a91-485">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="12a91-485">EnvironmentName</span></span>

<span data-ttu-id="12a91-486">La proprietà [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) può essere impostata su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="12a91-486">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="12a91-487">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="12a91-487">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="12a91-488">I valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="12a91-488">Values aren't case-sensitive.</span></span>

<span data-ttu-id="12a91-489">**Chiave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="12a91-489">**Key**: `environment`</span></span>  
<span data-ttu-id="12a91-490">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-490">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-491">**Impostazione predefinita**: `Production`</span><span class="sxs-lookup"><span data-stu-id="12a91-491">**Default**: `Production`</span></span>  
<span data-ttu-id="12a91-492">**Variabile di ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="12a91-492">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="12a91-493">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseEnvironment` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="12a91-493">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="12a91-494">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="12a91-494">ShutdownTimeout</span></span>

<span data-ttu-id="12a91-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) imposta il timeout per <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="12a91-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="12a91-496">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="12a91-496">The default value is five seconds.</span></span>  <span data-ttu-id="12a91-497">Durante il periodo di timeout, l'host:</span><span class="sxs-lookup"><span data-stu-id="12a91-497">During the timeout period, the host:</span></span>

* <span data-ttu-id="12a91-498">attiva [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="12a91-498">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="12a91-499">Tenta di arrestare i servizi ospitati, registrando gli errori per i servizi che non si interrompono.</span><span class="sxs-lookup"><span data-stu-id="12a91-499">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="12a91-500">Se il periodo di timeout scade prima che siano stati arrestati tutti i servizi ospitati, gli eventuali servizi attivi rimanenti vengono interrotti quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="12a91-500">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="12a91-501">I servizi si arrestano anche se non hanno completato l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="12a91-501">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="12a91-502">Se l'arresto dei servizi richiede più tempo, aumentare il valore di timeout.</span><span class="sxs-lookup"><span data-stu-id="12a91-502">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="12a91-503">**Chiave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="12a91-503">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="12a91-504">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="12a91-504">**Type**: `int`</span></span>  
<span data-ttu-id="12a91-505">**Impostazione predefinita**: 5 secondi</span><span class="sxs-lookup"><span data-stu-id="12a91-505">**Default**: 5 seconds</span></span>  
<span data-ttu-id="12a91-506">**Variabile di ambiente**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="12a91-506">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="12a91-507">Per impostare questo valore, usare la variabile di ambiente o configurare `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="12a91-507">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="12a91-508">Nell'esempio seguente il timeout viene impostato su 20 secondi:</span><span class="sxs-lookup"><span data-stu-id="12a91-508">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="12a91-509">Impostazioni per le app Web</span><span class="sxs-lookup"><span data-stu-id="12a91-509">Settings for web apps</span></span>

<span data-ttu-id="12a91-510">Alcune impostazioni host si applicano solo ai carichi di lavoro HTTP.</span><span class="sxs-lookup"><span data-stu-id="12a91-510">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="12a91-511">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="12a91-511">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="12a91-512">Per queste impostazioni sono disponibili i metodi di estensione su `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="12a91-512">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="12a91-513">Gli esempi di codice che illustrano come chiamare i metodi di estensione presumono che `webBuilder` sia un'istanza di `IWebHostBuilder`, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="12a91-513">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="12a91-514">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="12a91-514">CaptureStartupErrors</span></span>

<span data-ttu-id="12a91-515">Quando è `false`, gli errori durante l'avvio causano la chiusura dell'host.</span><span class="sxs-lookup"><span data-stu-id="12a91-515">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="12a91-516">Quando è `true`, l'host acquisisce le eccezioni durante l'avvio e tenta di avviare il server.</span><span class="sxs-lookup"><span data-stu-id="12a91-516">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="12a91-517">**Chiave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="12a91-517">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="12a91-518">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="12a91-518">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="12a91-519">**Impostazione predefinita**: il valore predefinito è `false` a meno che l'app non venga eseguita con Kestrel in IIS, in tal caso il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="12a91-519">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="12a91-520">**Variabile di ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="12a91-520">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="12a91-521">Per impostare questo valore, usare la configurazione o chiamare `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="12a91-521">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="12a91-522">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="12a91-522">DetailedErrors</span></span>

<span data-ttu-id="12a91-523">Quando è abilitata o quando l'ambiente è `Development`, l'app acquisisce gli errori dettagliati.</span><span class="sxs-lookup"><span data-stu-id="12a91-523">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="12a91-524">**Chiave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="12a91-524">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="12a91-525">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="12a91-525">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="12a91-526">**Impostazione predefinita**: `false`</span><span class="sxs-lookup"><span data-stu-id="12a91-526">**Default**: `false`</span></span>  
<span data-ttu-id="12a91-527">**Variabile di ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="12a91-527">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="12a91-528">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12a91-528">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="12a91-529">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="12a91-529">HostingStartupAssemblies</span></span>

<span data-ttu-id="12a91-530">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da caricare all'avvio.</span><span class="sxs-lookup"><span data-stu-id="12a91-530">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="12a91-531">Sebbene il valore di configurazione predefinito sia una stringa vuota, gli assembly di avvio dell'hosting includono sempre l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-531">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="12a91-532">Quando vengono specificati, gli assembly di avvio dell'hosting vengono aggiunti all'assembly dell'app per essere caricati quando l'app compila i servizi comuni durante l'avvio.</span><span class="sxs-lookup"><span data-stu-id="12a91-532">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="12a91-533">**Chiave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="12a91-533">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="12a91-534">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-534">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-535">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="12a91-535">**Default**: Empty string</span></span>  
<span data-ttu-id="12a91-536">**Variabile di ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="12a91-536">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="12a91-537">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12a91-537">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="12a91-538">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="12a91-538">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="12a91-539">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da escludere all'avvio.</span><span class="sxs-lookup"><span data-stu-id="12a91-539">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="12a91-540">**Chiave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="12a91-540">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="12a91-541">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-541">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-542">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="12a91-542">**Default**: Empty string</span></span>  
<span data-ttu-id="12a91-543">**Variabile di ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="12a91-543">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="12a91-544">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12a91-544">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="12a91-545">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="12a91-545">HTTPS_Port</span></span>

<span data-ttu-id="12a91-546">Porta di reindirizzamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="12a91-546">The HTTPS redirect port.</span></span> <span data-ttu-id="12a91-547">Usata per [imporre HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="12a91-547">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="12a91-548">**Chiave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="12a91-548">**Key**: `https_port`</span></span>  
<span data-ttu-id="12a91-549">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-549">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-550">**Impostazione predefinita**: un valore predefinito non è impostato.</span><span class="sxs-lookup"><span data-stu-id="12a91-550">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="12a91-551">**Variabile di ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="12a91-551">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="12a91-552">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12a91-552">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="12a91-553">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="12a91-553">PreferHostingUrls</span></span>

<span data-ttu-id="12a91-554">Indica se l'host deve essere in ascolto sugli URL configurati con `IWebHostBuilder` anziché gli URL configurati con l' `IServer` implementazione di.</span><span class="sxs-lookup"><span data-stu-id="12a91-554">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="12a91-555">**Chiave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="12a91-555">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="12a91-556">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="12a91-556">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="12a91-557">**Impostazione predefinita**: `true`</span><span class="sxs-lookup"><span data-stu-id="12a91-557">**Default**: `true`</span></span>  
<span data-ttu-id="12a91-558">**Variabile di ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="12a91-558">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="12a91-559">Per impostare questo valore, usare la variabile di ambiente o chiamare `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="12a91-559">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="12a91-560">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="12a91-560">PreventHostingStartup</span></span>

<span data-ttu-id="12a91-561">Impedisce il caricamento automatico degli assembly di avvio dell'hosting, inclusi gli assembly di avvio dell'hosting configurati dall'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-561">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="12a91-562">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="12a91-562">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="12a91-563">**Chiave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="12a91-563">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="12a91-564">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="12a91-564">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="12a91-565">**Impostazione predefinita**: `false`</span><span class="sxs-lookup"><span data-stu-id="12a91-565">**Default**: `false`</span></span>  
<span data-ttu-id="12a91-566">**Variabile di ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="12a91-566">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="12a91-567">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="12a91-567">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="12a91-568">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="12a91-568">StartupAssembly</span></span>

<span data-ttu-id="12a91-569">L'assembly per la ricerca della classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="12a91-569">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="12a91-570">**Chiave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="12a91-570">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="12a91-571">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-571">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-572">**Impostazione predefinita**: assembly dell'app</span><span class="sxs-lookup"><span data-stu-id="12a91-572">**Default**: The app's assembly</span></span>  
<span data-ttu-id="12a91-573">**Variabile di ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="12a91-573">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="12a91-574">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="12a91-574">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="12a91-575">`UseStartup` può richiedere un nome di assembly (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="12a91-575">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="12a91-576">Se vengono chiamati più metodi `UseStartup`, l'ultimo metodo ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="12a91-576">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="12a91-577">URL</span><span class="sxs-lookup"><span data-stu-id="12a91-577">URLs</span></span>

<span data-ttu-id="12a91-578">Elenco delimitato da punto e virgola degli indirizzi IP o gli indirizzi host con le porte e protocolli su cui il server deve eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="12a91-578">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="12a91-579">Ad esempio: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="12a91-579">For example, `http://localhost:123`.</span></span> <span data-ttu-id="12a91-580">Usare "\*" per indicare che il server deve eseguire l'ascolto per le richieste su tutti gli indirizzi IP o nomi host usando la porta e il protocollo specificati (ad esempio, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="12a91-580">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="12a91-581">Il protocollo (`http://` o `https://`) deve essere incluso con ogni URL.</span><span class="sxs-lookup"><span data-stu-id="12a91-581">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="12a91-582">I formati supportati variano a seconda del server.</span><span class="sxs-lookup"><span data-stu-id="12a91-582">Supported formats vary among servers.</span></span>

<span data-ttu-id="12a91-583">**Chiave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="12a91-583">**Key**: `urls`</span></span>  
<span data-ttu-id="12a91-584">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-584">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-585">**Impostazione predefinita**: `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="12a91-585">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="12a91-586">**Variabile di ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="12a91-586">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="12a91-587">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="12a91-587">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="12a91-588">Kestrel ha una propria API di configurazione degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="12a91-588">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="12a91-589">Per altre informazioni, vedere <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="12a91-589">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="12a91-590">WebRoot</span><span class="sxs-lookup"><span data-stu-id="12a91-590">WebRoot</span></span>

<span data-ttu-id="12a91-591">La proprietà [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina il percorso relativo degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-591">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="12a91-592">Se il percorso non esiste, viene usato un provider di file no-op.</span><span class="sxs-lookup"><span data-stu-id="12a91-592">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="12a91-593">**Chiave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="12a91-593">**Key**: `webroot`</span></span>  
<span data-ttu-id="12a91-594">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-594">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-595">**Impostazione predefinita**: il valore predefinito è `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="12a91-595">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="12a91-596">Il percorso di *{radice del contenuto}/wwwroot* deve esistere.</span><span class="sxs-lookup"><span data-stu-id="12a91-596">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="12a91-597">**Variabile di ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="12a91-597">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="12a91-598">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseWebRoot` su `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="12a91-598">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="12a91-599">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="12a91-599">For more information, see:</span></span>

* [<span data-ttu-id="12a91-600">Nozioni fondamentali: radice Web</span><span class="sxs-lookup"><span data-stu-id="12a91-600">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="12a91-601">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="12a91-601">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="12a91-602">Gestire la durata dell'host</span><span class="sxs-lookup"><span data-stu-id="12a91-602">Manage the host lifetime</span></span>

<span data-ttu-id="12a91-603">Chiamare metodi sull'implementazione <xref:Microsoft.Extensions.Hosting.IHost> incorporata per avviare e arrestare l'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-603">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="12a91-604">Questi metodi influiscono su tutte le implementazioni <xref:Microsoft.Extensions.Hosting.IHostedService> che vengono registrate nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="12a91-604">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="12a91-605">Esegui</span><span class="sxs-lookup"><span data-stu-id="12a91-605">Run</span></span>

<span data-ttu-id="12a91-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> esegue l'app e blocca il thread di chiamata fino all'arresto dell'host.</span><span class="sxs-lookup"><span data-stu-id="12a91-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="12a91-607">RunAsync</span><span class="sxs-lookup"><span data-stu-id="12a91-607">RunAsync</span></span>

<span data-ttu-id="12a91-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> esegue l'app e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="12a91-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="12a91-609">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="12a91-609">RunConsoleAsync</span></span>

<span data-ttu-id="12a91-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Abilita il supporto della console, compila e avvia l'host e attende la <kbd>Ctrl</kbd> + chiusura di CTRL<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="12a91-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="12a91-611">Avvio</span><span class="sxs-lookup"><span data-stu-id="12a91-611">Start</span></span>

<span data-ttu-id="12a91-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> avvia l'host in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="12a91-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="12a91-613">StartAsync</span><span class="sxs-lookup"><span data-stu-id="12a91-613">StartAsync</span></span>

<span data-ttu-id="12a91-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> avvia l'host e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="12a91-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="12a91-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> viene chiamato all'avvio di `StartAsync`, che attende il completamento prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="12a91-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="12a91-616">Questo è utile per ritardare l'avvio fino al segnale trasmesso da un evento esterno.</span><span class="sxs-lookup"><span data-stu-id="12a91-616">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="12a91-617">StopAsync</span><span class="sxs-lookup"><span data-stu-id="12a91-617">StopAsync</span></span>

<span data-ttu-id="12a91-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> prova ad arrestare l'host entro il timeout specificato.</span><span class="sxs-lookup"><span data-stu-id="12a91-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="12a91-619">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="12a91-619">WaitForShutdown</span></span>

<span data-ttu-id="12a91-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blocca il thread chiamante finché l'arresto non viene attivato da IHostLifetime, ad esempio tramite <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="12a91-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="12a91-621">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="12a91-621">WaitForShutdownAsync</span></span>

<span data-ttu-id="12a91-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato l'arresto tramite il token specificato, quindi chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="12a91-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="12a91-623">Controllo esterno</span><span class="sxs-lookup"><span data-stu-id="12a91-623">External control</span></span>

<span data-ttu-id="12a91-624">Il controllo diretto della durata dell'host può essere ottenuto usando metodi che possono essere chiamati dall'esterno:</span><span class="sxs-lookup"><span data-stu-id="12a91-624">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="12a91-625">Le app ASP.NET Core configurano e avviano un host.</span><span class="sxs-lookup"><span data-stu-id="12a91-625">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="12a91-626">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="12a91-626">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="12a91-627">Questo articolo illustra l'host generico di ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), che si usa per le app che non elaborano le richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="12a91-627">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="12a91-628">Lo scopo dell'host generico è separare la pipeline HTTP dall'API dell'host Web, per abilitare una gamma più ampia di scenari host.</span><span class="sxs-lookup"><span data-stu-id="12a91-628">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="12a91-629">La messaggistica, le attività in background e altri carichi di lavoro non HTTP basati sull'host generico traggono vantaggio dalle funzionalità a montaggio incrociato come la configurazione, l'inserimento di dipendenze (DI) e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="12a91-629">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="12a91-630">L'host generico è una nuova funzionalità introdotta in ASP.NET Core 2.1 e non è adatto agli scenari di hosting Web.</span><span class="sxs-lookup"><span data-stu-id="12a91-630">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="12a91-631">Per gli scenari di hosting Web usare l'[host Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="12a91-631">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="12a91-632">L'host generico sostituirà l'host Web in una versione futura, funzionando come API host principale negli scenari sia HTTP che non HTTP.</span><span class="sxs-lookup"><span data-stu-id="12a91-632">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="12a91-633">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="12a91-633">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="12a91-634">Quando si esegue l'app di esempio in [Visual Studio Code](https://code.visualstudio.com/), usare un *terminale esterno o integrato*.</span><span class="sxs-lookup"><span data-stu-id="12a91-634">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="12a91-635">Non eseguire l'esempio in un ambiente `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="12a91-635">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="12a91-636">Per impostare la console in Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="12a91-636">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="12a91-637">Aprire il file *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="12a91-637">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="12a91-638">Nella configurazione **.NET Core Launch (console)** trovare la voce **console**.</span><span class="sxs-lookup"><span data-stu-id="12a91-638">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="12a91-639">Impostare il valore su `externalTerminal` o `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="12a91-639">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="12a91-640">Introduzione</span><span class="sxs-lookup"><span data-stu-id="12a91-640">Introduction</span></span>

<span data-ttu-id="12a91-641">La libreria host generico è disponibile nello spazio dei nomi <xref:Microsoft.Extensions.Hosting> e viene specificata dal pacchetto [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="12a91-641">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="12a91-642">Il pacchetto `Microsoft.Extensions.Hosting` è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 o versioni successive).</span><span class="sxs-lookup"><span data-stu-id="12a91-642">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="12a91-643"><xref:Microsoft.Extensions.Hosting.IHostedService> è il punto di ingresso per l'esecuzione del codice.</span><span class="sxs-lookup"><span data-stu-id="12a91-643"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="12a91-644">Ogni implementazione <xref:Microsoft.Extensions.Hosting.IHostedService> viene eseguita nell'ordine di [registrazione del servizio in ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="12a91-644">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="12a91-645"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> viene chiamato su ogni <xref:Microsoft.Extensions.Hosting.IHostedService> quando viene avviato l'host e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> viene chiamato in ordine di registrazione inverso quando l'host viene chiuso normalmente.</span><span class="sxs-lookup"><span data-stu-id="12a91-645"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="12a91-646">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="12a91-646">Set up a host</span></span>

<span data-ttu-id="12a91-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> è il componente principale che le librerie e le app usano per inizializzare, compilare ed eseguire l'host:</span><span class="sxs-lookup"><span data-stu-id="12a91-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="12a91-648">Opzioni</span><span class="sxs-lookup"><span data-stu-id="12a91-648">Options</span></span>

<span data-ttu-id="12a91-649"><xref:Microsoft.Extensions.Hosting.HostOptions> configura le opzioni per <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="12a91-649"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="12a91-650">Timeout di arresto</span><span class="sxs-lookup"><span data-stu-id="12a91-650">Shutdown timeout</span></span>

<span data-ttu-id="12a91-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> imposta il timeout per <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="12a91-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="12a91-652">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="12a91-652">The default value is five seconds.</span></span>

<span data-ttu-id="12a91-653">La seguente configurazione di opzione in `Program.Main` aumenta il timeout predefinito di cinque secondi di chiusura a 20 secondi:</span><span class="sxs-lookup"><span data-stu-id="12a91-653">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="12a91-654">Servizi predefiniti</span><span class="sxs-lookup"><span data-stu-id="12a91-654">Default services</span></span>

<span data-ttu-id="12a91-655">I servizi seguenti vengono registrati durante l'inizializzazione dell'host:</span><span class="sxs-lookup"><span data-stu-id="12a91-655">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="12a91-656">[Ambiente](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="12a91-656">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="12a91-657">[Configurazione](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="12a91-657">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="12a91-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="12a91-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="12a91-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="12a91-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="12a91-660">[Opzioni](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="12a91-660">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="12a91-661">[Registrazione](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="12a91-661">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="12a91-662">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="12a91-662">Host configuration</span></span>

<span data-ttu-id="12a91-663">La configurazione dell'host viene creata tramite:</span><span class="sxs-lookup"><span data-stu-id="12a91-663">Host configuration is created by:</span></span>

* <span data-ttu-id="12a91-664">Chiamata ai metodi di estensione su <xref:Microsoft.Extensions.Hosting.IHostBuilder> per impostare la [radice del contenuto](#content-root) e l'[ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="12a91-664">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="12a91-665">Lettura della configurazione dai provider di configurazione in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="12a91-665">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="12a91-666">Metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="12a91-666">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="12a91-667">Chiave applicazione (nome)</span><span class="sxs-lookup"><span data-stu-id="12a91-667">Application key (name)</span></span>

<span data-ttu-id="12a91-668">La proprietà [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) viene impostata dalla configurazione dell'host durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="12a91-668">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="12a91-669">Per impostare il valore in modo esplicito, usare [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="12a91-669">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="12a91-670">**Chiave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="12a91-670">**Key**: `applicationName`</span></span>  
<span data-ttu-id="12a91-671">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-671">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-672">**Impostazione predefinita**: il nome dell'assembly contenente il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-672">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="12a91-673">**Imposta utilizzando**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="12a91-673">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="12a91-674">**Variabile di ambiente**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` è [facoltativo e definito dall'utente](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="12a91-674">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="12a91-675">Radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="12a91-675">Content root</span></span>

<span data-ttu-id="12a91-676">Questa impostazione determina la posizione da cui l'host inizia la ricerca dei file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="12a91-676">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="12a91-677">**Chiave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="12a91-677">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="12a91-678">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-678">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-679">**Impostazione predefinita**: il valore predefinito corrisponde alla cartella contenente l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-679">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="12a91-680">**Imposta utilizzando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="12a91-680">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="12a91-681">**Variabile di ambiente**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` è [facoltativo e definito dall'utente](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="12a91-681">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="12a91-682">Se il percorso non esiste, l'host non verrà avviato.</span><span class="sxs-lookup"><span data-stu-id="12a91-682">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="12a91-683">Per altre informazioni, vedere [nozioni fondamentali: radice del contenuto](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="12a91-683">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="12a91-684">Ambiente</span><span class="sxs-lookup"><span data-stu-id="12a91-684">Environment</span></span>

<span data-ttu-id="12a91-685">Imposta l' [ambiente](xref:fundamentals/environments)dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-685">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="12a91-686">**Chiave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="12a91-686">**Key**: `environment`</span></span>  
<span data-ttu-id="12a91-687">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="12a91-687">**Type**: `string`</span></span>  
<span data-ttu-id="12a91-688">**Impostazione predefinita**: `Production`</span><span class="sxs-lookup"><span data-stu-id="12a91-688">**Default**: `Production`</span></span>  
<span data-ttu-id="12a91-689">**Imposta utilizzando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="12a91-689">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="12a91-690">**Variabile di ambiente**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` è [facoltativo e definito dall'utente](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="12a91-690">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="12a91-691">L'ambiente può essere impostato su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="12a91-691">The environment can be set to any value.</span></span> <span data-ttu-id="12a91-692">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="12a91-692">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="12a91-693">I valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="12a91-693">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="12a91-694">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="12a91-694">ConfigureHostConfiguration</span></span>

<span data-ttu-id="12a91-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per creare un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfiguration> per l'host.</span><span class="sxs-lookup"><span data-stu-id="12a91-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="12a91-696">La configurazione dell'host viene usata per inizializzare l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> da usare nel processo di compilazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-696">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="12a91-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="12a91-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="12a91-698">L'host usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="12a91-698">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="12a91-699">Nessun provider è incluso per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="12a91-699">No providers are included by default.</span></span> <span data-ttu-id="12a91-700">È necessario specificare in modo esplicito tutti i provider di configurazione richiesti dall'app in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, inclusi:</span><span class="sxs-lookup"><span data-stu-id="12a91-700">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="12a91-701">Configurazione dei file (ad esempio, da un file *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="12a91-701">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="12a91-702">Configurazione delle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="12a91-702">Environment variable configuration.</span></span>
* <span data-ttu-id="12a91-703">Configurazione degli argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="12a91-703">Command-line argument configuration.</span></span>
* <span data-ttu-id="12a91-704">Tutti gli altri provider di configurazione necessari.</span><span class="sxs-lookup"><span data-stu-id="12a91-704">Any other required configuration providers.</span></span>

<span data-ttu-id="12a91-705">La configurazione file dell'host viene abilitata specificando il percorso di base dell'app con `SetBasePath` seguito da una chiamata a uno dei [provider di configurazione dei file](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="12a91-705">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="12a91-706">L'app di esempio usa un file JSON, *hostsettings.json*, e chiama <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> per utilizzare le impostazioni di configurazione host del file.</span><span class="sxs-lookup"><span data-stu-id="12a91-706">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="12a91-707">Per aggiungere la [configurazione delle variabili di ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) dell'host, chiamare <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sul generatore di host.</span><span class="sxs-lookup"><span data-stu-id="12a91-707">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="12a91-708">`AddEnvironmentVariables` accetta un prefisso facoltativo definito dall'utente.</span><span class="sxs-lookup"><span data-stu-id="12a91-708">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="12a91-709">L'app di esempio usa un prefisso di `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="12a91-709">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="12a91-710">Il prefisso viene rimosso quando vengono lette le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="12a91-710">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="12a91-711">Quando viene configurato l'host dell'app di esempio, il valore della variabile di ambiente per `PREFIX_ENVIRONMENT` diventa il valore di configurazione dell'host per la chiave `environment`.</span><span class="sxs-lookup"><span data-stu-id="12a91-711">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="12a91-712">Durante lo sviluppo quando si usa [Visual Studio](https://visualstudio.microsoft.com) o si esegue un'app con `dotnet run`, le variabili di ambiente possono essere impostate nel file *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="12a91-712">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="12a91-713">Durante lo sviluppo in [Visual Studio Code](https://code.visualstudio.com/), le variabili di ambiente possono essere impostate nel file *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="12a91-713">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="12a91-714">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="12a91-714">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="12a91-715">La [configurazione della riga di comando](xref:fundamentals/configuration/index#command-line-configuration-provider) viene aggiunta chiamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="12a91-715">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="12a91-716">La configurazione della riga di comando viene aggiunta per ultima per consentire agli argomenti della riga di comando di eseguire l'override della configurazione fornita dai provider di configurazione precedenti.</span><span class="sxs-lookup"><span data-stu-id="12a91-716">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="12a91-717">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="12a91-717">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="12a91-718">È possibile fornire una configurazione aggiuntiva con le chiavi [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="12a91-718">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="12a91-719">Configurazione di esempio di `HostBuilder` che usa <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="12a91-719">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="12a91-720">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="12a91-720">ConfigureAppConfiguration</span></span>

<span data-ttu-id="12a91-721">La configurazione dell'app viene creata chiamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sull'implementazione <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="12a91-721">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="12a91-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per creare un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfiguration> per l'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="12a91-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="12a91-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="12a91-724">L'app usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="12a91-724">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="12a91-725">La configurazione creata da <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> è disponibile in [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) per le operazioni successive e in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="12a91-725">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="12a91-726">La configurazione dell'app riceve automaticamente la configurazione dell'host fornita da [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="12a91-726">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="12a91-727">Configurazione app di esempio che usa <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="12a91-727">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="12a91-728">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="12a91-728">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="12a91-729">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="12a91-729">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="12a91-730">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="12a91-730">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="12a91-731">Per spostare i file delle impostazioni nella directory di output, specificare i file delle impostazioni come [elementi di progetto MSBuild](/visualstudio/msbuild/common-msbuild-project-items) nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="12a91-731">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="12a91-732">L'app di esempio sposta i file delle impostazioni dell'app JSON e *hostsettings.json* con l'elemento `<Content>` seguente:</span><span class="sxs-lookup"><span data-stu-id="12a91-732">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="12a91-733">I metodi di estensione di configurazione, ad esempio <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> e <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, necessitano di altri pacchetti NuGet, ad esempio [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) e [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="12a91-733">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="12a91-734">A meno che l'app non usi il [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), questi pacchetti devono essere aggiunti al progetto oltre al pacchetto [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) di base.</span><span class="sxs-lookup"><span data-stu-id="12a91-734">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="12a91-735">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="12a91-735">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="12a91-736">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="12a91-736">ConfigureServices</span></span>

<span data-ttu-id="12a91-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> aggiunge servizi al contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="12a91-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="12a91-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="12a91-739">Un servizio ospitato è una classe con logica di attività in background che implementa l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="12a91-739">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="12a91-740">Per altre informazioni, vedere <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="12a91-740">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="12a91-741">L'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa il metodo di estensione `AddHostedService` per aggiungere all'app un servizio per gli eventi di durata (`LifetimeEventsHostedService`) e un'attività in background con timeout (`TimedHostedService`):</span><span class="sxs-lookup"><span data-stu-id="12a91-741">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="12a91-742">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="12a91-742">ConfigureLogging</span></span>

<span data-ttu-id="12a91-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> aggiunge un delegato per configurare l'interfaccia <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornita.</span><span class="sxs-lookup"><span data-stu-id="12a91-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="12a91-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="12a91-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="12a91-745">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="12a91-745">UseConsoleLifetime</span></span>

<span data-ttu-id="12a91-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>Ascolta la <kbd>combinazione di tasti CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM e chiama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> per avviare il processo di arresto.</span><span class="sxs-lookup"><span data-stu-id="12a91-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="12a91-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> Sblocca le estensioni, ad esempio [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="12a91-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="12a91-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` è preregistrata come implementazione di durata predefinita.</span><span class="sxs-lookup"><span data-stu-id="12a91-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="12a91-749">Viene usata l'ultima durata registrata.</span><span class="sxs-lookup"><span data-stu-id="12a91-749">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="12a91-750">Configurazione contenitore</span><span class="sxs-lookup"><span data-stu-id="12a91-750">Container configuration</span></span>

<span data-ttu-id="12a91-751">Per supportare l'inserimento di altri contenitori, l'host può accettare un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="12a91-751">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="12a91-752">La disponibilità di una factory non fa parte della registrazione del contenitore DI, ma è una funzionalità intrinseca dell'host usata per creare il contenitore DI concreto.</span><span class="sxs-lookup"><span data-stu-id="12a91-752">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="12a91-753">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) esegue l'override della factory predefinita usata per la creazione del provider di servizi dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-753">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="12a91-754">La configurazione del contenitore personalizzato è gestita dal metodo <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="12a91-754">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="12a91-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> offre un'esperienza fortemente tipizzata per la configurazione del contenitore sulla base dell'API host sottostante.</span><span class="sxs-lookup"><span data-stu-id="12a91-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="12a91-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="12a91-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="12a91-757">Creare un contenitore di servizi per l'app:</span><span class="sxs-lookup"><span data-stu-id="12a91-757">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="12a91-758">Specificare una factory per il contenitore di servizi:</span><span class="sxs-lookup"><span data-stu-id="12a91-758">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="12a91-759">Usare la factory e configurare il contenitore di servizi personalizzato per l'app:</span><span class="sxs-lookup"><span data-stu-id="12a91-759">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="12a91-760">Estendibilità</span><span class="sxs-lookup"><span data-stu-id="12a91-760">Extensibility</span></span>

<span data-ttu-id="12a91-761">L'estensibilità host viene eseguita con i metodi di estensione su <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="12a91-761">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="12a91-762">L'esempio seguente illustra come un metodo di estensione estende un'implementazione <xref:Microsoft.Extensions.Hosting.IHostBuilder> con l'esempio [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) dimostrato in <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="12a91-762">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="12a91-763">Un'app stabilisce il metodo di estensione `UseHostedService` per registrare il servizio ospitato passato in `T`:</span><span class="sxs-lookup"><span data-stu-id="12a91-763">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="12a91-764">Gestire l'host</span><span class="sxs-lookup"><span data-stu-id="12a91-764">Manage the host</span></span>

<span data-ttu-id="12a91-765">L'implementazione <xref:Microsoft.Extensions.Hosting.IHost> è responsabile dell'avvio e arresto delle implementazioni <xref:Microsoft.Extensions.Hosting.IHostedService> che sono registrate nel contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="12a91-765">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="12a91-766">Esegui</span><span class="sxs-lookup"><span data-stu-id="12a91-766">Run</span></span>

<span data-ttu-id="12a91-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> esegue l'app e blocca il thread di chiamata fino all'arresto dell'host:</span><span class="sxs-lookup"><span data-stu-id="12a91-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="12a91-768">RunAsync</span><span class="sxs-lookup"><span data-stu-id="12a91-768">RunAsync</span></span>

<span data-ttu-id="12a91-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> esegue l'app e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto:</span><span class="sxs-lookup"><span data-stu-id="12a91-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="12a91-770">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="12a91-770">RunConsoleAsync</span></span>

<span data-ttu-id="12a91-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Abilita il supporto della console, compila e avvia l'host e attende la <kbd>Ctrl</kbd> + chiusura di CTRL<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="12a91-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="12a91-772">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="12a91-772">Start and StopAsync</span></span>

<span data-ttu-id="12a91-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> avvia l'host in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="12a91-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="12a91-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> prova ad arrestare l'host entro il timeout specificato.</span><span class="sxs-lookup"><span data-stu-id="12a91-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="12a91-775">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="12a91-775">StartAsync and StopAsync</span></span>

<span data-ttu-id="12a91-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> avvia l'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="12a91-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> arresta l'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="12a91-778">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="12a91-778">WaitForShutdown</span></span>

<span data-ttu-id="12a91-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>viene attivato tramite <xref:Microsoft.Extensions.Hosting.IHostLifetime> , ad esempio (in `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` attesa di <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="12a91-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="12a91-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="12a91-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="12a91-781">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="12a91-781">WaitForShutdownAsync</span></span>

<span data-ttu-id="12a91-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato l'arresto tramite il token specificato, quindi chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="12a91-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="12a91-783">Controllo esterno</span><span class="sxs-lookup"><span data-stu-id="12a91-783">External control</span></span>

<span data-ttu-id="12a91-784">Il controllo esterno dell'host può essere ottenuto usando metodi che possono essere chiamati dall'esterno:</span><span class="sxs-lookup"><span data-stu-id="12a91-784">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="12a91-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> viene chiamato all'avvio di <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, che attende il completamento prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="12a91-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="12a91-786">Questo è utile per ritardare l'avvio fino al segnale trasmesso da un evento esterno.</span><span class="sxs-lookup"><span data-stu-id="12a91-786">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="12a91-787">Interfaccia IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="12a91-787">IHostingEnvironment interface</span></span>

<span data-ttu-id="12a91-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> include informazioni sull'ambiente di hosting dell'app.</span><span class="sxs-lookup"><span data-stu-id="12a91-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="12a91-789">Usare l'[inserimento di un costruttore](xref:fundamentals/dependency-injection) per ottenere <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> per poterne usare le proprietà e i metodi di estensione:</span><span class="sxs-lookup"><span data-stu-id="12a91-789">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="12a91-790">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="12a91-790">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="12a91-791">Interfaccia IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="12a91-791">IApplicationLifetime interface</span></span>

<span data-ttu-id="12a91-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> consente attività post-avvio e di arresto, incluse le richieste di arresto normale.</span><span class="sxs-lookup"><span data-stu-id="12a91-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="12a91-793">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi <xref:System.Action> che definiscono gli eventi di avvio e arresto.</span><span class="sxs-lookup"><span data-stu-id="12a91-793">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="12a91-794">Token di annullamento</span><span class="sxs-lookup"><span data-stu-id="12a91-794">Cancellation Token</span></span> | <span data-ttu-id="12a91-795">Attivato quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="12a91-795">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="12a91-796">L'host è stato completamente avviato.</span><span class="sxs-lookup"><span data-stu-id="12a91-796">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="12a91-797">L'host sta completando un arresto normale.</span><span class="sxs-lookup"><span data-stu-id="12a91-797">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="12a91-798">Tutte le richieste devono essere elaborate.</span><span class="sxs-lookup"><span data-stu-id="12a91-798">All requests should be processed.</span></span> <span data-ttu-id="12a91-799">L'arresto è bloccato fino al completamento di questo evento.</span><span class="sxs-lookup"><span data-stu-id="12a91-799">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="12a91-800">L'host sta eseguendo un arresto normale.</span><span class="sxs-lookup"><span data-stu-id="12a91-800">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="12a91-801">È possibile che le richieste siano ancora in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="12a91-801">Requests may still be processing.</span></span> <span data-ttu-id="12a91-802">L'arresto è bloccato fino al completamento di questo evento.</span><span class="sxs-lookup"><span data-stu-id="12a91-802">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="12a91-803">Inserire tramite costruttore il servizio <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> in qualsiasi classe.</span><span class="sxs-lookup"><span data-stu-id="12a91-803">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="12a91-804">L'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa l'inserimento di costruttori in una classe `LifetimeEventsHostedService` (implementazione <xref:Microsoft.Extensions.Hosting.IHostedService>) per registrare gli eventi.</span><span class="sxs-lookup"><span data-stu-id="12a91-804">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="12a91-805">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="12a91-805">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="12a91-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> richiede la chiusura dell'applicazione corrente.</span><span class="sxs-lookup"><span data-stu-id="12a91-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="12a91-807">La classe seguente usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> per arrestare normalmente un'app quando viene chiamato il metodo `Shutdown` della classe:</span><span class="sxs-lookup"><span data-stu-id="12a91-807">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="12a91-808">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="12a91-808">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
