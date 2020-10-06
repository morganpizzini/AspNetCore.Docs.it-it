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
ms.openlocfilehash: 59cfae9ff619f8de894686c4b773d66e5cbe10ad
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754736"
---
# <a name="net-generic-host"></a><span data-ttu-id="91cf8-103">Host generico .NET</span><span class="sxs-lookup"><span data-stu-id="91cf8-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="91cf8-104">I modelli di ASP.NET Core creano un host generico .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="91cf8-104">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="91cf8-105">Definizione host</span><span class="sxs-lookup"><span data-stu-id="91cf8-105">Host definition</span></span>

<span data-ttu-id="91cf8-106">Un *host* è un oggetto che incapsula le risorse di un'app, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="91cf8-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="91cf8-107">Inserimento di dipendenze (DI)</span><span class="sxs-lookup"><span data-stu-id="91cf8-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="91cf8-108">Registrazione</span><span class="sxs-lookup"><span data-stu-id="91cf8-108">Logging</span></span>
* <span data-ttu-id="91cf8-109">Configurazione</span><span class="sxs-lookup"><span data-stu-id="91cf8-109">Configuration</span></span>
* <span data-ttu-id="91cf8-110">`IHostedService` Implementazioni</span><span class="sxs-lookup"><span data-stu-id="91cf8-110">`IHostedService` implementations</span></span>

<span data-ttu-id="91cf8-111">Quando viene avviato, un host chiama <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> su ogni implementazione di <xref:Microsoft.Extensions.Hosting.IHostedService> registrata nella raccolta di servizi ospitati del contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="91cf8-111">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="91cf8-112">In un'app Web, una delle implementazioni di `IHostedService` è un servizio web che avvia un'[implementazione del server HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="91cf8-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="91cf8-113">Il motivo principale per cui tutte le risorse interdipendenti dell'app sono incluse in un unico oggetto è la gestione del ciclo di vita, vale a dire il controllo sull'avvio dell'app e sull'arresto normale.</span><span class="sxs-lookup"><span data-stu-id="91cf8-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="91cf8-114">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="91cf8-114">Set up a host</span></span>

<span data-ttu-id="91cf8-115">L'host è in genere configurato, compilato ed eseguito da codice nella classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="91cf8-116">Il metodo `Main`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-116">The `Main` method:</span></span>

* <span data-ttu-id="91cf8-117">Chiama un metodo `CreateHostBuilder` per creare e configurare un oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="91cf8-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="91cf8-118">Chiamate `Build` e metodi `Run` nell'oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="91cf8-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="91cf8-119">I modelli Web ASP.NET Core generano il codice seguente per creare un host:</span><span class="sxs-lookup"><span data-stu-id="91cf8-119">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="91cf8-120">Il codice seguente crea un carico di lavoro non HTTP con un' `IHostedService` implementazione aggiunta al contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="91cf8-120">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="91cf8-121">Per un carico di lavoro HTTP, il metodo `Main` è lo stesso ma `CreateHostBuilder` chiama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-121">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="91cf8-122">Se l'app usa Entity Framework Core, non modificare il nome o la firma del metodo `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-122">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="91cf8-123">Gli [strumenti di Entity Framework Core](/ef/core/miscellaneous/cli/) si aspettano di trovare un metodo `CreateHostBuilder` che configura l'host senza eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-123">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="91cf8-124">Per altre informazioni, vedere [Creazione DbContext in fase di progettazione](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="91cf8-124">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="91cf8-125">Impostazioni predefinite del generatore</span><span class="sxs-lookup"><span data-stu-id="91cf8-125">Default builder settings</span></span>

<span data-ttu-id="91cf8-126">Il metodo <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="91cf8-126">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="91cf8-127">Imposta la [radice del contenuto](xref:fundamentals/index#content-root) sul percorso restituito da <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="91cf8-127">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="91cf8-128">Carica la configurazione dell'host da:</span><span class="sxs-lookup"><span data-stu-id="91cf8-128">Loads host configuration from:</span></span>
  * <span data-ttu-id="91cf8-129">Variabili di ambiente con prefisso `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="91cf8-129">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="91cf8-130">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="91cf8-130">Command-line arguments.</span></span>
* <span data-ttu-id="91cf8-131">Carica la configurazione dell'app da:</span><span class="sxs-lookup"><span data-stu-id="91cf8-131">Loads app configuration from:</span></span>
  * <span data-ttu-id="91cf8-132">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="91cf8-132">*appsettings.json*.</span></span>
  * <span data-ttu-id="91cf8-133">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="91cf8-133">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="91cf8-134">[Segreti del manager](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-134">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="91cf8-135">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="91cf8-135">Environment variables.</span></span>
  * <span data-ttu-id="91cf8-136">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="91cf8-136">Command-line arguments.</span></span>
* <span data-ttu-id="91cf8-137">Aggiunge i provider di [log](xref:fundamentals/logging/index) seguenti:</span><span class="sxs-lookup"><span data-stu-id="91cf8-137">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="91cf8-138">Console</span><span class="sxs-lookup"><span data-stu-id="91cf8-138">Console</span></span>
  * <span data-ttu-id="91cf8-139">Debug</span><span class="sxs-lookup"><span data-stu-id="91cf8-139">Debug</span></span>
  * <span data-ttu-id="91cf8-140">EventSource</span><span class="sxs-lookup"><span data-stu-id="91cf8-140">EventSource</span></span>
  * <span data-ttu-id="91cf8-141">EventLog (solo quando è in esecuzione su Windows)</span><span class="sxs-lookup"><span data-stu-id="91cf8-141">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="91cf8-142">Abilita la [convalida dell'ambito](xref:fundamentals/dependency-injection#scope-validation) e la [convalida delle dipendenze](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando l'ambiente è lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="91cf8-142">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="91cf8-143">Il metodo `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-143">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="91cf8-144">Carica la configurazione host dalle variabili di ambiente con prefisso `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="91cf8-144">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="91cf8-145">Imposta il server [Kestrel](xref:fundamentals/servers/kestrel) come server Web e lo configura usando i provider di configurazione dell'host dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-145">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="91cf8-146">Per le opzioni predefinite del server Kestrel, vedere <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-146">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="91cf8-147">Aggiunge [il middleware dell'applicazione di filtri dell'host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="91cf8-147">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="91cf8-148">Aggiunge il [middleware delle intestazioni con inoltri](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è uguale a `true` .</span><span class="sxs-lookup"><span data-stu-id="91cf8-148">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="91cf8-149">Abilita l'integrazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="91cf8-149">Enables IIS integration.</span></span> <span data-ttu-id="91cf8-150">Per le opzioni predefinite di IIS, vedere <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-150">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="91cf8-151">Le sezioni [Impostazioni per tutti i tipi di app](#settings-for-all-app-types) e [Impostazioni per le app Web](#settings-for-web-apps) più avanti in questo articolo mostrano come eseguire l'override delle impostazioni predefinite del generatore.</span><span class="sxs-lookup"><span data-stu-id="91cf8-151">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="91cf8-152">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="91cf8-152">Framework-provided services</span></span>

<span data-ttu-id="91cf8-153">I servizi seguenti vengono registrati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="91cf8-153">The following services are registered automatically:</span></span>

* [<span data-ttu-id="91cf8-154">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="91cf8-154">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="91cf8-155">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="91cf8-155">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="91cf8-156">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="91cf8-156">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="91cf8-157">Per ulteriori informazioni sui servizi forniti dal Framework, vedere <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="91cf8-157">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="91cf8-158">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="91cf8-158">IHostApplicationLifetime</span></span>

<span data-ttu-id="91cf8-159">Inserire il servizio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (in precedenza `IApplicationLifetime`) in qualsiasi classe per gestire le attività post-avvio e di arresto normale.</span><span class="sxs-lookup"><span data-stu-id="91cf8-159">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="91cf8-160">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi del gestore dell'evento di avvio e di arresto.</span><span class="sxs-lookup"><span data-stu-id="91cf8-160">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="91cf8-161">L'interfaccia include anche un metodo `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-161">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="91cf8-162">L'esempio seguente è un' `IHostedService` implementazione di che registra `IHostApplicationLifetime` gli eventi:</span><span class="sxs-lookup"><span data-stu-id="91cf8-162">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="91cf8-163">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="91cf8-163">IHostLifetime</span></span>

<span data-ttu-id="91cf8-164">L'implementazione <xref:Microsoft.Extensions.Hosting.IHostLifetime> controlla quando l'host viene avviato e quando si arresta.</span><span class="sxs-lookup"><span data-stu-id="91cf8-164">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="91cf8-165">Viene usata l'ultima implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="91cf8-165">The last implementation registered is used.</span></span>

<span data-ttu-id="91cf8-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` è l'implementazione `IHostLifetime` predefinita.</span><span class="sxs-lookup"><span data-stu-id="91cf8-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="91cf8-167">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-167">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="91cf8-168">Ascolta la <kbd>combinazione di tasti CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM e chiama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> per avviare il processo di arresto.</span><span class="sxs-lookup"><span data-stu-id="91cf8-168">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="91cf8-169">Sblocca estensioni come [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="91cf8-169">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="91cf8-170">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="91cf8-170">IHostEnvironment</span></span>

<span data-ttu-id="91cf8-171">Inserire il <xref:Microsoft.Extensions.Hosting.IHostEnvironment> servizio in una classe per ottenere informazioni sulle impostazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="91cf8-171">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="91cf8-172">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="91cf8-172">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="91cf8-173">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="91cf8-173">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="91cf8-174">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="91cf8-174">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="91cf8-175">Le app Web implementano l' `IWebHostEnvironment` interfaccia, che eredita `IHostEnvironment` e aggiunge [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="91cf8-175">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="91cf8-176">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="91cf8-176">Host configuration</span></span>

<span data-ttu-id="91cf8-177">La configurazione dell'host viene usata per le proprietà dell'implementazione <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-177">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="91cf8-178">La configurazione dell'host è disponibile da [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) all'interno di <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-178">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="91cf8-179">Dopo `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` viene sostituito con la configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-179">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="91cf8-180">Per aggiungere la configurazione dell'host, chiamare <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-180">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="91cf8-181">`ConfigureHostConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="91cf8-181">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="91cf8-182">L'host usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="91cf8-182">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="91cf8-183">Il provider di variabili di ambiente con prefisso `DOTNET_` e argomenti della riga di comando sono inclusi in `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="91cf8-183">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="91cf8-184">Per le app Web, viene aggiunto il provider di variabili di ambiente con prefisso `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-184">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="91cf8-185">Il prefisso viene rimosso quando vengono lette le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="91cf8-185">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="91cf8-186">Ad esempio, il valore della variabile di ambiente per `ASPNETCORE_ENVIRONMENT` diventa il valore di configurazione dell'host per la chiave `environment`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-186">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="91cf8-187">L'esempio seguente crea la configurazione host:</span><span class="sxs-lookup"><span data-stu-id="91cf8-187">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="91cf8-188">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="91cf8-188">App configuration</span></span>

<span data-ttu-id="91cf8-189">La configurazione dell'app viene creata chiamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-189">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="91cf8-190">`ConfigureAppConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="91cf8-190">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="91cf8-191">L'app usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="91cf8-191">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="91cf8-192">La configurazione creata da `ConfigureAppConfiguration` è disponibile in [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) per le operazioni successive e come servizio da DI.</span><span class="sxs-lookup"><span data-stu-id="91cf8-192">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="91cf8-193">La configurazione dell'host viene aggiunta anche alla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-193">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="91cf8-194">Per altre informazioni, vedere [Configurazione in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="91cf8-194">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="91cf8-195">Impostazioni per tutti i tipi di app</span><span class="sxs-lookup"><span data-stu-id="91cf8-195">Settings for all app types</span></span>

<span data-ttu-id="91cf8-196">Questa sezione elenca le impostazioni dell'host che si applicano ai carichi di lavoro HTTP e non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="91cf8-196">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="91cf8-197">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-197">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="91cf8-198">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="91cf8-198">ApplicationName</span></span>

<span data-ttu-id="91cf8-199">La proprietà [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) viene impostata dalla configurazione dell'host durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="91cf8-199">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="91cf8-200">**Chiave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="91cf8-200">**Key**: `applicationName`</span></span>  
<span data-ttu-id="91cf8-201">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-201">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-202">**Impostazione predefinita**: il nome dell'assembly che contiene il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-202">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="91cf8-203">**Variabile di ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="91cf8-203">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="91cf8-204">per impostare questo valore usare la variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="91cf8-204">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="91cf8-205">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="91cf8-205">ContentRoot</span></span>

<span data-ttu-id="91cf8-206">La proprietà [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina il punto in cui l'host inizia a cercare i file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="91cf8-206">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="91cf8-207">Se il percorso non esiste, l'host non verrà avviato.</span><span class="sxs-lookup"><span data-stu-id="91cf8-207">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="91cf8-208">**Chiave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="91cf8-208">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="91cf8-209">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-209">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-210">**Impostazione predefinita**: la cartella in cui risiede l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-210">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="91cf8-211">**Variabile di ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="91cf8-211">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="91cf8-212">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseContentRoot` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-212">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="91cf8-213">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="91cf8-213">For more information, see:</span></span>

* [<span data-ttu-id="91cf8-214">Nozioni fondamentali: radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="91cf8-214">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="91cf8-215">WebRoot</span><span class="sxs-lookup"><span data-stu-id="91cf8-215">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="91cf8-216">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="91cf8-216">EnvironmentName</span></span>

<span data-ttu-id="91cf8-217">La proprietà [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) può essere impostata su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="91cf8-217">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="91cf8-218">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-218">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="91cf8-219">I valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="91cf8-219">Values aren't case-sensitive.</span></span>

<span data-ttu-id="91cf8-220">**Chiave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="91cf8-220">**Key**: `environment`</span></span>  
<span data-ttu-id="91cf8-221">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-221">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-222">**Impostazione predefinita**: `Production`</span><span class="sxs-lookup"><span data-stu-id="91cf8-222">**Default**: `Production`</span></span>  
<span data-ttu-id="91cf8-223">**Variabile di ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="91cf8-223">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="91cf8-224">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseEnvironment` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-224">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="91cf8-225">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="91cf8-225">ShutdownTimeout</span></span>

<span data-ttu-id="91cf8-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) imposta il timeout per <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="91cf8-227">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="91cf8-227">The default value is five seconds.</span></span>  <span data-ttu-id="91cf8-228">Durante il periodo di timeout, l'host:</span><span class="sxs-lookup"><span data-stu-id="91cf8-228">During the timeout period, the host:</span></span>

* <span data-ttu-id="91cf8-229">attiva [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="91cf8-229">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="91cf8-230">Tenta di arrestare i servizi ospitati, registrando gli errori per i servizi che non si interrompono.</span><span class="sxs-lookup"><span data-stu-id="91cf8-230">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="91cf8-231">Se il periodo di timeout scade prima che siano stati arrestati tutti i servizi ospitati, gli eventuali servizi attivi rimanenti vengono interrotti quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="91cf8-231">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="91cf8-232">I servizi si arrestano anche se non hanno completato l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="91cf8-232">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="91cf8-233">Se l'arresto dei servizi richiede più tempo, aumentare il valore di timeout.</span><span class="sxs-lookup"><span data-stu-id="91cf8-233">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="91cf8-234">**Chiave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="91cf8-234">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="91cf8-235">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="91cf8-235">**Type**: `int`</span></span>  
<span data-ttu-id="91cf8-236">**Impostazione predefinita**: 5 secondi</span><span class="sxs-lookup"><span data-stu-id="91cf8-236">**Default**: 5 seconds</span></span>  
<span data-ttu-id="91cf8-237">**Variabile di ambiente**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="91cf8-237">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="91cf8-238">Per impostare questo valore, usare la variabile di ambiente o configurare `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-238">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="91cf8-239">Nell'esempio seguente il timeout viene impostato su 20 secondi:</span><span class="sxs-lookup"><span data-stu-id="91cf8-239">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="91cf8-240">Disabilitare il ricaricamento della configurazione dell'app durante la modifica</span><span class="sxs-lookup"><span data-stu-id="91cf8-240">Disable app configuration reload on change</span></span>

<span data-ttu-id="91cf8-241">Per [impostazione predefinita](xref:fundamentals/configuration/index#default), *appsettings.json* e *appSettings. { L'ambiente}. JSON* viene ricaricato quando il file viene modificato.</span><span class="sxs-lookup"><span data-stu-id="91cf8-241">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="91cf8-242">Per disabilitare questo comportamento di ricaricamento in ASP.NET Core 5,0 o versione successiva, impostare la `hostBuilder:reloadConfigOnChange` chiave su `false` .</span><span class="sxs-lookup"><span data-stu-id="91cf8-242">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="91cf8-243">**Chiave**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="91cf8-243">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="91cf8-244">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="91cf8-244">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="91cf8-245">**Impostazione predefinita**: `true`</span><span class="sxs-lookup"><span data-stu-id="91cf8-245">**Default**: `true`</span></span>  
<span data-ttu-id="91cf8-246">**Argomento della riga di comando**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="91cf8-246">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="91cf8-247">**Variabile di ambiente**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="91cf8-247">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="91cf8-248">Il separatore dei due punti ( `:` ) non funziona con le chiavi gerarchiche delle variabili di ambiente in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="91cf8-248">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="91cf8-249">Per altre informazioni, vedere [variabili di ambiente](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="91cf8-249">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="91cf8-250">Impostazioni per le app Web</span><span class="sxs-lookup"><span data-stu-id="91cf8-250">Settings for web apps</span></span>

<span data-ttu-id="91cf8-251">Alcune impostazioni host si applicano solo ai carichi di lavoro HTTP.</span><span class="sxs-lookup"><span data-stu-id="91cf8-251">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="91cf8-252">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-252">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="91cf8-253">Per queste impostazioni sono disponibili i metodi di estensione su `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-253">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="91cf8-254">Gli esempi di codice che illustrano come chiamare i metodi di estensione presumono che `webBuilder` sia un'istanza di `IWebHostBuilder`, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="91cf8-254">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="91cf8-255">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="91cf8-255">CaptureStartupErrors</span></span>

<span data-ttu-id="91cf8-256">Quando è `false`, gli errori durante l'avvio causano la chiusura dell'host.</span><span class="sxs-lookup"><span data-stu-id="91cf8-256">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="91cf8-257">Quando è `true`, l'host acquisisce le eccezioni durante l'avvio e tenta di avviare il server.</span><span class="sxs-lookup"><span data-stu-id="91cf8-257">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="91cf8-258">**Chiave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="91cf8-258">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="91cf8-259">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="91cf8-259">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="91cf8-260">**Impostazione predefinita**: il valore predefinito è `false` a meno che l'app non venga eseguita con Kestrel in IIS, in tal caso il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-260">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="91cf8-261">**Variabile di ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="91cf8-261">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="91cf8-262">Per impostare questo valore, usare la configurazione o chiamare `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-262">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="91cf8-263">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="91cf8-263">DetailedErrors</span></span>

<span data-ttu-id="91cf8-264">Quando è abilitata o quando l'ambiente è `Development`, l'app acquisisce gli errori dettagliati.</span><span class="sxs-lookup"><span data-stu-id="91cf8-264">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="91cf8-265">**Chiave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="91cf8-265">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="91cf8-266">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="91cf8-266">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="91cf8-267">**Impostazione predefinita**: `false`</span><span class="sxs-lookup"><span data-stu-id="91cf8-267">**Default**: `false`</span></span>  
<span data-ttu-id="91cf8-268">**Variabile di ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="91cf8-268">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="91cf8-269">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-269">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="91cf8-270">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="91cf8-270">HostingStartupAssemblies</span></span>

<span data-ttu-id="91cf8-271">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da caricare all'avvio.</span><span class="sxs-lookup"><span data-stu-id="91cf8-271">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="91cf8-272">Sebbene il valore di configurazione predefinito sia una stringa vuota, gli assembly di avvio dell'hosting includono sempre l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-272">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="91cf8-273">Quando vengono specificati, gli assembly di avvio dell'hosting vengono aggiunti all'assembly dell'app per essere caricati quando l'app compila i servizi comuni durante l'avvio.</span><span class="sxs-lookup"><span data-stu-id="91cf8-273">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="91cf8-274">**Chiave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="91cf8-274">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="91cf8-275">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-275">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-276">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="91cf8-276">**Default**: Empty string</span></span>  
<span data-ttu-id="91cf8-277">**Variabile di ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="91cf8-277">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="91cf8-278">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-278">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="91cf8-279">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="91cf8-279">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="91cf8-280">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da escludere all'avvio.</span><span class="sxs-lookup"><span data-stu-id="91cf8-280">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="91cf8-281">**Chiave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="91cf8-281">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="91cf8-282">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-282">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-283">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="91cf8-283">**Default**: Empty string</span></span>  
<span data-ttu-id="91cf8-284">**Variabile di ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="91cf8-284">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="91cf8-285">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="91cf8-286">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="91cf8-286">HTTPS_Port</span></span>

<span data-ttu-id="91cf8-287">Porta di reindirizzamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="91cf8-287">The HTTPS redirect port.</span></span> <span data-ttu-id="91cf8-288">Usata per [imporre HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="91cf8-288">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="91cf8-289">**Chiave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="91cf8-289">**Key**: `https_port`</span></span>  
<span data-ttu-id="91cf8-290">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-290">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-291">**Impostazione predefinita**: un valore predefinito non è impostato.</span><span class="sxs-lookup"><span data-stu-id="91cf8-291">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="91cf8-292">**Variabile di ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="91cf8-292">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="91cf8-293">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-293">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="91cf8-294">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="91cf8-294">PreferHostingUrls</span></span>

<span data-ttu-id="91cf8-295">Indica se l'host deve essere in ascolto sugli URL configurati con `IWebHostBuilder` anziché gli URL configurati con l' `IServer` implementazione di.</span><span class="sxs-lookup"><span data-stu-id="91cf8-295">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="91cf8-296">**Chiave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="91cf8-296">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="91cf8-297">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="91cf8-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="91cf8-298">**Impostazione predefinita**: `true`</span><span class="sxs-lookup"><span data-stu-id="91cf8-298">**Default**: `true`</span></span>  
<span data-ttu-id="91cf8-299">**Variabile di ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="91cf8-299">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="91cf8-300">Per impostare questo valore, usare la variabile di ambiente o chiamare `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-300">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="91cf8-301">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="91cf8-301">PreventHostingStartup</span></span>

<span data-ttu-id="91cf8-302">Impedisce il caricamento automatico degli assembly di avvio dell'hosting, inclusi gli assembly di avvio dell'hosting configurati dall'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-302">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="91cf8-303">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-303">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="91cf8-304">**Chiave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="91cf8-304">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="91cf8-305">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="91cf8-305">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="91cf8-306">**Impostazione predefinita**: `false`</span><span class="sxs-lookup"><span data-stu-id="91cf8-306">**Default**: `false`</span></span>  
<span data-ttu-id="91cf8-307">**Variabile di ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="91cf8-307">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="91cf8-308">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-308">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="91cf8-309">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="91cf8-309">StartupAssembly</span></span>

<span data-ttu-id="91cf8-310">L'assembly per la ricerca della classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-310">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="91cf8-311">**Chiave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="91cf8-311">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="91cf8-312">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-312">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-313">**Impostazione predefinita**: assembly dell'app</span><span class="sxs-lookup"><span data-stu-id="91cf8-313">**Default**: The app's assembly</span></span>  
<span data-ttu-id="91cf8-314">**Variabile di ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="91cf8-314">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="91cf8-315">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-315">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="91cf8-316">`UseStartup` può richiedere un nome di assembly (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="91cf8-316">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="91cf8-317">Se vengono chiamati più metodi `UseStartup`, l'ultimo metodo ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="91cf8-317">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="91cf8-318">URL</span><span class="sxs-lookup"><span data-stu-id="91cf8-318">URLs</span></span>

<span data-ttu-id="91cf8-319">Elenco delimitato da punto e virgola degli indirizzi IP o gli indirizzi host con le porte e protocolli su cui il server deve eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="91cf8-319">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="91cf8-320">Ad esempio: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-320">For example, `http://localhost:123`.</span></span> <span data-ttu-id="91cf8-321">Usare "\*" per indicare che il server deve eseguire l'ascolto per le richieste su tutti gli indirizzi IP o nomi host usando la porta e il protocollo specificati (ad esempio, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="91cf8-321">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="91cf8-322">Il protocollo (`http://` o `https://`) deve essere incluso con ogni URL.</span><span class="sxs-lookup"><span data-stu-id="91cf8-322">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="91cf8-323">I formati supportati variano a seconda del server.</span><span class="sxs-lookup"><span data-stu-id="91cf8-323">Supported formats vary among servers.</span></span>

<span data-ttu-id="91cf8-324">**Chiave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="91cf8-324">**Key**: `urls`</span></span>  
<span data-ttu-id="91cf8-325">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-325">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-326">**Impostazione predefinita**: `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="91cf8-326">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="91cf8-327">**Variabile di ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="91cf8-327">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="91cf8-328">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-328">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="91cf8-329">Kestrel ha una propria API di configurazione degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="91cf8-329">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="91cf8-330">Per altre informazioni, vedere <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-330">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="91cf8-331">WebRoot</span><span class="sxs-lookup"><span data-stu-id="91cf8-331">WebRoot</span></span>

<span data-ttu-id="91cf8-332">La proprietà [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina il percorso relativo degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-332">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="91cf8-333">Se il percorso non esiste, viene usato un provider di file no-op.</span><span class="sxs-lookup"><span data-stu-id="91cf8-333">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="91cf8-334">**Chiave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="91cf8-334">**Key**: `webroot`</span></span>  
<span data-ttu-id="91cf8-335">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-335">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-336">**Impostazione predefinita**: il valore predefinito è `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="91cf8-336">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="91cf8-337">Il percorso di *{radice del contenuto}/wwwroot* deve esistere.</span><span class="sxs-lookup"><span data-stu-id="91cf8-337">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="91cf8-338">**Variabile di ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="91cf8-338">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="91cf8-339">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseWebRoot` su `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-339">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="91cf8-340">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="91cf8-340">For more information, see:</span></span>

* [<span data-ttu-id="91cf8-341">Nozioni fondamentali: radice Web</span><span class="sxs-lookup"><span data-stu-id="91cf8-341">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="91cf8-342">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="91cf8-342">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="91cf8-343">Gestire la durata dell'host</span><span class="sxs-lookup"><span data-stu-id="91cf8-343">Manage the host lifetime</span></span>

<span data-ttu-id="91cf8-344">Chiamare metodi sull'implementazione <xref:Microsoft.Extensions.Hosting.IHost> incorporata per avviare e arrestare l'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-344">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="91cf8-345">Questi metodi influiscono su tutte le implementazioni <xref:Microsoft.Extensions.Hosting.IHostedService> che vengono registrate nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="91cf8-345">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="91cf8-346">Esegui</span><span class="sxs-lookup"><span data-stu-id="91cf8-346">Run</span></span>

<span data-ttu-id="91cf8-347"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> esegue l'app e blocca il thread di chiamata fino all'arresto dell'host.</span><span class="sxs-lookup"><span data-stu-id="91cf8-347"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="91cf8-348">RunAsync</span><span class="sxs-lookup"><span data-stu-id="91cf8-348">RunAsync</span></span>

<span data-ttu-id="91cf8-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> esegue l'app e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="91cf8-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="91cf8-350">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="91cf8-350">RunConsoleAsync</span></span>

<span data-ttu-id="91cf8-351"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Abilita il supporto della console, compila e avvia l'host e attende la <kbd>Ctrl</kbd> + chiusura di CTRL<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="91cf8-351"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="91cf8-352">Avvio</span><span class="sxs-lookup"><span data-stu-id="91cf8-352">Start</span></span>

<span data-ttu-id="91cf8-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> avvia l'host in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="91cf8-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="91cf8-354">StartAsync</span><span class="sxs-lookup"><span data-stu-id="91cf8-354">StartAsync</span></span>

<span data-ttu-id="91cf8-355"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> avvia l'host e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="91cf8-355"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="91cf8-356"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> viene chiamato all'avvio di `StartAsync`, che attende il completamento prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="91cf8-356"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="91cf8-357">Questo è utile per ritardare l'avvio fino al segnale trasmesso da un evento esterno.</span><span class="sxs-lookup"><span data-stu-id="91cf8-357">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="91cf8-358">StopAsync</span><span class="sxs-lookup"><span data-stu-id="91cf8-358">StopAsync</span></span>

<span data-ttu-id="91cf8-359"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> prova ad arrestare l'host entro il timeout specificato.</span><span class="sxs-lookup"><span data-stu-id="91cf8-359"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="91cf8-360">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="91cf8-360">WaitForShutdown</span></span>

<span data-ttu-id="91cf8-361"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blocca il thread chiamante finché l'arresto non viene attivato da IHostLifetime, ad esempio tramite <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="91cf8-361"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="91cf8-362">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="91cf8-362">WaitForShutdownAsync</span></span>

<span data-ttu-id="91cf8-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato l'arresto tramite il token specificato, quindi chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="91cf8-364">Controllo esterno</span><span class="sxs-lookup"><span data-stu-id="91cf8-364">External control</span></span>

<span data-ttu-id="91cf8-365">Il controllo diretto della durata dell'host può essere ottenuto usando metodi che possono essere chiamati dall'esterno:</span><span class="sxs-lookup"><span data-stu-id="91cf8-365">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="91cf8-366">I modelli di ASP.NET Core creano un host generico .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="91cf8-366">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="91cf8-367">Definizione host</span><span class="sxs-lookup"><span data-stu-id="91cf8-367">Host definition</span></span>

<span data-ttu-id="91cf8-368">Un *host* è un oggetto che incapsula le risorse di un'app, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="91cf8-368">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="91cf8-369">Inserimento di dipendenze (DI)</span><span class="sxs-lookup"><span data-stu-id="91cf8-369">Dependency injection (DI)</span></span>
* <span data-ttu-id="91cf8-370">Registrazione</span><span class="sxs-lookup"><span data-stu-id="91cf8-370">Logging</span></span>
* <span data-ttu-id="91cf8-371">Configurazione</span><span class="sxs-lookup"><span data-stu-id="91cf8-371">Configuration</span></span>
* <span data-ttu-id="91cf8-372">`IHostedService` Implementazioni</span><span class="sxs-lookup"><span data-stu-id="91cf8-372">`IHostedService` implementations</span></span>

<span data-ttu-id="91cf8-373">Quando viene avviato, un host chiama <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> su ogni implementazione di <xref:Microsoft.Extensions.Hosting.IHostedService> registrata nella raccolta di servizi ospitati del contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="91cf8-373">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="91cf8-374">In un'app Web, una delle implementazioni di `IHostedService` è un servizio web che avvia un'[implementazione del server HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="91cf8-374">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="91cf8-375">Il motivo principale per cui tutte le risorse interdipendenti dell'app sono incluse in un unico oggetto è la gestione del ciclo di vita, vale a dire il controllo sull'avvio dell'app e sull'arresto normale.</span><span class="sxs-lookup"><span data-stu-id="91cf8-375">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="91cf8-376">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="91cf8-376">Set up a host</span></span>

<span data-ttu-id="91cf8-377">L'host è in genere configurato, compilato ed eseguito da codice nella classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-377">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="91cf8-378">Il metodo `Main`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-378">The `Main` method:</span></span>

* <span data-ttu-id="91cf8-379">Chiama un metodo `CreateHostBuilder` per creare e configurare un oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="91cf8-379">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="91cf8-380">Chiamate `Build` e metodi `Run` nell'oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="91cf8-380">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="91cf8-381">I modelli Web ASP.NET Core generano il codice seguente per creare un host generico:</span><span class="sxs-lookup"><span data-stu-id="91cf8-381">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="91cf8-382">Il codice seguente crea un host generico utilizzando un carico di lavoro non HTTP.</span><span class="sxs-lookup"><span data-stu-id="91cf8-382">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="91cf8-383">L' `IHostedService` implementazione viene aggiunta al contenitore DI di:</span><span class="sxs-lookup"><span data-stu-id="91cf8-383">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="91cf8-384">Per un carico di lavoro HTTP, il metodo `Main` è lo stesso ma `CreateHostBuilder` chiama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-384">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="91cf8-385">Il codice precedente viene generato dai modelli di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="91cf8-385">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="91cf8-386">Se l'app usa Entity Framework Core, non modificare il nome o la firma del metodo `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-386">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="91cf8-387">Gli [strumenti di Entity Framework Core](/ef/core/miscellaneous/cli/) si aspettano di trovare un metodo `CreateHostBuilder` che configura l'host senza eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-387">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="91cf8-388">Per altre informazioni, vedere [Creazione DbContext in fase di progettazione](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="91cf8-388">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="91cf8-389">Impostazioni predefinite del generatore</span><span class="sxs-lookup"><span data-stu-id="91cf8-389">Default builder settings</span></span>

<span data-ttu-id="91cf8-390">Il metodo <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>:</span><span class="sxs-lookup"><span data-stu-id="91cf8-390">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="91cf8-391">Imposta la [radice del contenuto](xref:fundamentals/index#content-root) sul percorso restituito da <xref:System.IO.Directory.GetCurrentDirectory%2A> .</span><span class="sxs-lookup"><span data-stu-id="91cf8-391">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="91cf8-392">Carica la configurazione dell'host da:</span><span class="sxs-lookup"><span data-stu-id="91cf8-392">Loads host configuration from:</span></span>
  * <span data-ttu-id="91cf8-393">Variabili di ambiente con prefisso `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="91cf8-393">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="91cf8-394">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="91cf8-394">Command-line arguments.</span></span>
* <span data-ttu-id="91cf8-395">Carica la configurazione dell'app da:</span><span class="sxs-lookup"><span data-stu-id="91cf8-395">Loads app configuration from:</span></span>
  * <span data-ttu-id="91cf8-396">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="91cf8-396">*appsettings.json*.</span></span>
  * <span data-ttu-id="91cf8-397">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="91cf8-397">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="91cf8-398">[Segreti del manager](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-398">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="91cf8-399">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="91cf8-399">Environment variables.</span></span>
  * <span data-ttu-id="91cf8-400">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="91cf8-400">Command-line arguments.</span></span>
* <span data-ttu-id="91cf8-401">Aggiunge i provider di [log](xref:fundamentals/logging/index) seguenti:</span><span class="sxs-lookup"><span data-stu-id="91cf8-401">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="91cf8-402">Console</span><span class="sxs-lookup"><span data-stu-id="91cf8-402">Console</span></span>
  * <span data-ttu-id="91cf8-403">Debug</span><span class="sxs-lookup"><span data-stu-id="91cf8-403">Debug</span></span>
  * <span data-ttu-id="91cf8-404">EventSource</span><span class="sxs-lookup"><span data-stu-id="91cf8-404">EventSource</span></span>
  * <span data-ttu-id="91cf8-405">EventLog (solo quando è in esecuzione su Windows)</span><span class="sxs-lookup"><span data-stu-id="91cf8-405">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="91cf8-406">Abilita la [convalida dell'ambito](xref:fundamentals/dependency-injection#scope-validation) e la [convalida delle dipendenze](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando l'ambiente è lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="91cf8-406">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="91cf8-407">Il metodo `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-407">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="91cf8-408">Carica la configurazione host dalle variabili di ambiente con prefisso `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="91cf8-408">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="91cf8-409">Imposta il server [Kestrel](xref:fundamentals/servers/kestrel) come server Web e lo configura usando i provider di configurazione dell'host dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-409">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="91cf8-410">Per le opzioni predefinite del server Kestrel, vedere <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-410">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="91cf8-411">Aggiunge [il middleware dell'applicazione di filtri dell'host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="91cf8-411">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="91cf8-412">Aggiunge il [middleware delle intestazioni con inoltri](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è uguale a `true` .</span><span class="sxs-lookup"><span data-stu-id="91cf8-412">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="91cf8-413">Abilita l'integrazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="91cf8-413">Enables IIS integration.</span></span> <span data-ttu-id="91cf8-414">Per le opzioni predefinite di IIS, vedere <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-414">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="91cf8-415">Le sezioni [Impostazioni per tutti i tipi di app](#settings-for-all-app-types) e [Impostazioni per le app Web](#settings-for-web-apps) più avanti in questo articolo mostrano come eseguire l'override delle impostazioni predefinite del generatore.</span><span class="sxs-lookup"><span data-stu-id="91cf8-415">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="91cf8-416">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="91cf8-416">Framework-provided services</span></span>

<span data-ttu-id="91cf8-417">I servizi seguenti vengono registrati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="91cf8-417">The following services are registered automatically:</span></span>

* [<span data-ttu-id="91cf8-418">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="91cf8-418">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="91cf8-419">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="91cf8-419">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="91cf8-420">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="91cf8-420">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="91cf8-421">Per ulteriori informazioni sui servizi forniti dal Framework, vedere <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="91cf8-421">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="91cf8-422">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="91cf8-422">IHostApplicationLifetime</span></span>

<span data-ttu-id="91cf8-423">Inserire il servizio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (in precedenza `IApplicationLifetime`) in qualsiasi classe per gestire le attività post-avvio e di arresto normale.</span><span class="sxs-lookup"><span data-stu-id="91cf8-423">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="91cf8-424">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi del gestore dell'evento di avvio e di arresto.</span><span class="sxs-lookup"><span data-stu-id="91cf8-424">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="91cf8-425">L'interfaccia include anche un metodo `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-425">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="91cf8-426">L'esempio seguente è un' `IHostedService` implementazione di che registra `IHostApplicationLifetime` gli eventi:</span><span class="sxs-lookup"><span data-stu-id="91cf8-426">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="91cf8-427">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="91cf8-427">IHostLifetime</span></span>

<span data-ttu-id="91cf8-428">L'implementazione <xref:Microsoft.Extensions.Hosting.IHostLifetime> controlla quando l'host viene avviato e quando si arresta.</span><span class="sxs-lookup"><span data-stu-id="91cf8-428">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="91cf8-429">Viene usata l'ultima implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="91cf8-429">The last implementation registered is used.</span></span>

<span data-ttu-id="91cf8-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` è l'implementazione `IHostLifetime` predefinita.</span><span class="sxs-lookup"><span data-stu-id="91cf8-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="91cf8-431">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-431">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="91cf8-432">Ascolta la <kbd>combinazione di tasti CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM e chiama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> per avviare il processo di arresto.</span><span class="sxs-lookup"><span data-stu-id="91cf8-432">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="91cf8-433">Sblocca estensioni come [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="91cf8-433">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="91cf8-434">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="91cf8-434">IHostEnvironment</span></span>

<span data-ttu-id="91cf8-435">Inserire il <xref:Microsoft.Extensions.Hosting.IHostEnvironment> servizio in una classe per ottenere informazioni sulle impostazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="91cf8-435">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="91cf8-436">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="91cf8-436">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="91cf8-437">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="91cf8-437">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="91cf8-438">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="91cf8-438">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="91cf8-439">Le app Web implementano l' `IWebHostEnvironment` interfaccia, che eredita `IHostEnvironment` e aggiunge [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="91cf8-439">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="91cf8-440">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="91cf8-440">Host configuration</span></span>

<span data-ttu-id="91cf8-441">La configurazione dell'host viene usata per le proprietà dell'implementazione <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-441">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="91cf8-442">La configurazione dell'host è disponibile da [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) all'interno di <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-442">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="91cf8-443">Dopo `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` viene sostituito con la configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-443">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="91cf8-444">Per aggiungere la configurazione dell'host, chiamare <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-444">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="91cf8-445">`ConfigureHostConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="91cf8-445">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="91cf8-446">L'host usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="91cf8-446">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="91cf8-447">Il provider di variabili di ambiente con prefisso `DOTNET_` e argomenti della riga di comando sono inclusi in `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="91cf8-447">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="91cf8-448">Per le app Web, viene aggiunto il provider di variabili di ambiente con prefisso `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-448">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="91cf8-449">Il prefisso viene rimosso quando vengono lette le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="91cf8-449">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="91cf8-450">Ad esempio, il valore della variabile di ambiente per `ASPNETCORE_ENVIRONMENT` diventa il valore di configurazione dell'host per la chiave `environment`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-450">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="91cf8-451">L'esempio seguente crea la configurazione host:</span><span class="sxs-lookup"><span data-stu-id="91cf8-451">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="91cf8-452">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="91cf8-452">App configuration</span></span>

<span data-ttu-id="91cf8-453">La configurazione dell'app viene creata chiamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="91cf8-454">`ConfigureAppConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="91cf8-454">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="91cf8-455">L'app usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="91cf8-455">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="91cf8-456">La configurazione creata da `ConfigureAppConfiguration` è disponibile in [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) per le operazioni successive e come servizio da DI.</span><span class="sxs-lookup"><span data-stu-id="91cf8-456">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="91cf8-457">La configurazione dell'host viene aggiunta anche alla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-457">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="91cf8-458">Per altre informazioni, vedere [Configurazione in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="91cf8-458">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="91cf8-459">Impostazioni per tutti i tipi di app</span><span class="sxs-lookup"><span data-stu-id="91cf8-459">Settings for all app types</span></span>

<span data-ttu-id="91cf8-460">Questa sezione elenca le impostazioni dell'host che si applicano ai carichi di lavoro HTTP e non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="91cf8-460">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="91cf8-461">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-461">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="91cf8-462">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="91cf8-462">ApplicationName</span></span>

<span data-ttu-id="91cf8-463">La proprietà [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) viene impostata dalla configurazione dell'host durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="91cf8-463">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="91cf8-464">**Chiave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="91cf8-464">**Key**: `applicationName`</span></span>  
<span data-ttu-id="91cf8-465">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-465">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-466">**Impostazione predefinita**: il nome dell'assembly che contiene il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-466">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="91cf8-467">**Variabile di ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="91cf8-467">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="91cf8-468">per impostare questo valore usare la variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="91cf8-468">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="91cf8-469">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="91cf8-469">ContentRoot</span></span>

<span data-ttu-id="91cf8-470">La proprietà [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina il punto in cui l'host inizia a cercare i file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="91cf8-470">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="91cf8-471">Se il percorso non esiste, l'host non verrà avviato.</span><span class="sxs-lookup"><span data-stu-id="91cf8-471">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="91cf8-472">**Chiave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="91cf8-472">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="91cf8-473">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-473">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-474">**Impostazione predefinita**: la cartella in cui risiede l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-474">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="91cf8-475">**Variabile di ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="91cf8-475">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="91cf8-476">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseContentRoot` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-476">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="91cf8-477">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="91cf8-477">For more information, see:</span></span>

* [<span data-ttu-id="91cf8-478">Nozioni fondamentali: radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="91cf8-478">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="91cf8-479">WebRoot</span><span class="sxs-lookup"><span data-stu-id="91cf8-479">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="91cf8-480">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="91cf8-480">EnvironmentName</span></span>

<span data-ttu-id="91cf8-481">La proprietà [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) può essere impostata su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="91cf8-481">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="91cf8-482">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-482">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="91cf8-483">I valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="91cf8-483">Values aren't case-sensitive.</span></span>

<span data-ttu-id="91cf8-484">**Chiave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="91cf8-484">**Key**: `environment`</span></span>  
<span data-ttu-id="91cf8-485">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-485">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-486">**Impostazione predefinita**: `Production`</span><span class="sxs-lookup"><span data-stu-id="91cf8-486">**Default**: `Production`</span></span>  
<span data-ttu-id="91cf8-487">**Variabile di ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="91cf8-487">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="91cf8-488">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseEnvironment` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-488">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="91cf8-489">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="91cf8-489">ShutdownTimeout</span></span>

<span data-ttu-id="91cf8-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) imposta il timeout per <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="91cf8-491">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="91cf8-491">The default value is five seconds.</span></span>  <span data-ttu-id="91cf8-492">Durante il periodo di timeout, l'host:</span><span class="sxs-lookup"><span data-stu-id="91cf8-492">During the timeout period, the host:</span></span>

* <span data-ttu-id="91cf8-493">attiva [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="91cf8-493">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="91cf8-494">Tenta di arrestare i servizi ospitati, registrando gli errori per i servizi che non si interrompono.</span><span class="sxs-lookup"><span data-stu-id="91cf8-494">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="91cf8-495">Se il periodo di timeout scade prima che siano stati arrestati tutti i servizi ospitati, gli eventuali servizi attivi rimanenti vengono interrotti quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="91cf8-495">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="91cf8-496">I servizi si arrestano anche se non hanno completato l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="91cf8-496">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="91cf8-497">Se l'arresto dei servizi richiede più tempo, aumentare il valore di timeout.</span><span class="sxs-lookup"><span data-stu-id="91cf8-497">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="91cf8-498">**Chiave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="91cf8-498">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="91cf8-499">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="91cf8-499">**Type**: `int`</span></span>  
<span data-ttu-id="91cf8-500">**Impostazione predefinita**: 5 secondi</span><span class="sxs-lookup"><span data-stu-id="91cf8-500">**Default**: 5 seconds</span></span>  
<span data-ttu-id="91cf8-501">**Variabile di ambiente**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="91cf8-501">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="91cf8-502">Per impostare questo valore, usare la variabile di ambiente o configurare `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-502">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="91cf8-503">Nell'esempio seguente il timeout viene impostato su 20 secondi:</span><span class="sxs-lookup"><span data-stu-id="91cf8-503">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="91cf8-504">Impostazioni per le app Web</span><span class="sxs-lookup"><span data-stu-id="91cf8-504">Settings for web apps</span></span>

<span data-ttu-id="91cf8-505">Alcune impostazioni host si applicano solo ai carichi di lavoro HTTP.</span><span class="sxs-lookup"><span data-stu-id="91cf8-505">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="91cf8-506">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-506">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="91cf8-507">Per queste impostazioni sono disponibili i metodi di estensione su `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-507">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="91cf8-508">Gli esempi di codice che illustrano come chiamare i metodi di estensione presumono che `webBuilder` sia un'istanza di `IWebHostBuilder`, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="91cf8-508">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="91cf8-509">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="91cf8-509">CaptureStartupErrors</span></span>

<span data-ttu-id="91cf8-510">Quando è `false`, gli errori durante l'avvio causano la chiusura dell'host.</span><span class="sxs-lookup"><span data-stu-id="91cf8-510">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="91cf8-511">Quando è `true`, l'host acquisisce le eccezioni durante l'avvio e tenta di avviare il server.</span><span class="sxs-lookup"><span data-stu-id="91cf8-511">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="91cf8-512">**Chiave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="91cf8-512">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="91cf8-513">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="91cf8-513">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="91cf8-514">**Impostazione predefinita**: il valore predefinito è `false` a meno che l'app non venga eseguita con Kestrel in IIS, in tal caso il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-514">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="91cf8-515">**Variabile di ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="91cf8-515">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="91cf8-516">Per impostare questo valore, usare la configurazione o chiamare `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-516">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="91cf8-517">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="91cf8-517">DetailedErrors</span></span>

<span data-ttu-id="91cf8-518">Quando è abilitata o quando l'ambiente è `Development`, l'app acquisisce gli errori dettagliati.</span><span class="sxs-lookup"><span data-stu-id="91cf8-518">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="91cf8-519">**Chiave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="91cf8-519">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="91cf8-520">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="91cf8-520">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="91cf8-521">**Impostazione predefinita**: `false`</span><span class="sxs-lookup"><span data-stu-id="91cf8-521">**Default**: `false`</span></span>  
<span data-ttu-id="91cf8-522">**Variabile di ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="91cf8-522">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="91cf8-523">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-523">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="91cf8-524">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="91cf8-524">HostingStartupAssemblies</span></span>

<span data-ttu-id="91cf8-525">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da caricare all'avvio.</span><span class="sxs-lookup"><span data-stu-id="91cf8-525">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="91cf8-526">Sebbene il valore di configurazione predefinito sia una stringa vuota, gli assembly di avvio dell'hosting includono sempre l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-526">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="91cf8-527">Quando vengono specificati, gli assembly di avvio dell'hosting vengono aggiunti all'assembly dell'app per essere caricati quando l'app compila i servizi comuni durante l'avvio.</span><span class="sxs-lookup"><span data-stu-id="91cf8-527">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="91cf8-528">**Chiave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="91cf8-528">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="91cf8-529">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-529">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-530">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="91cf8-530">**Default**: Empty string</span></span>  
<span data-ttu-id="91cf8-531">**Variabile di ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="91cf8-531">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="91cf8-532">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-532">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="91cf8-533">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="91cf8-533">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="91cf8-534">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da escludere all'avvio.</span><span class="sxs-lookup"><span data-stu-id="91cf8-534">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="91cf8-535">**Chiave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="91cf8-535">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="91cf8-536">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-536">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-537">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="91cf8-537">**Default**: Empty string</span></span>  
<span data-ttu-id="91cf8-538">**Variabile di ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="91cf8-538">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="91cf8-539">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-539">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="91cf8-540">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="91cf8-540">HTTPS_Port</span></span>

<span data-ttu-id="91cf8-541">Porta di reindirizzamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="91cf8-541">The HTTPS redirect port.</span></span> <span data-ttu-id="91cf8-542">Usata per [imporre HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="91cf8-542">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="91cf8-543">**Chiave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="91cf8-543">**Key**: `https_port`</span></span>  
<span data-ttu-id="91cf8-544">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-544">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-545">**Impostazione predefinita**: un valore predefinito non è impostato.</span><span class="sxs-lookup"><span data-stu-id="91cf8-545">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="91cf8-546">**Variabile di ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="91cf8-546">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="91cf8-547">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-547">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="91cf8-548">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="91cf8-548">PreferHostingUrls</span></span>

<span data-ttu-id="91cf8-549">Indica se l'host deve essere in ascolto sugli URL configurati con `IWebHostBuilder` anziché gli URL configurati con l' `IServer` implementazione di.</span><span class="sxs-lookup"><span data-stu-id="91cf8-549">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="91cf8-550">**Chiave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="91cf8-550">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="91cf8-551">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="91cf8-551">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="91cf8-552">**Impostazione predefinita**: `true`</span><span class="sxs-lookup"><span data-stu-id="91cf8-552">**Default**: `true`</span></span>  
<span data-ttu-id="91cf8-553">**Variabile di ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="91cf8-553">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="91cf8-554">Per impostare questo valore, usare la variabile di ambiente o chiamare `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-554">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="91cf8-555">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="91cf8-555">PreventHostingStartup</span></span>

<span data-ttu-id="91cf8-556">Impedisce il caricamento automatico degli assembly di avvio dell'hosting, inclusi gli assembly di avvio dell'hosting configurati dall'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-556">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="91cf8-557">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-557">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="91cf8-558">**Chiave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="91cf8-558">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="91cf8-559">**Tipo**: `bool` ( `true` o `1` )</span><span class="sxs-lookup"><span data-stu-id="91cf8-559">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="91cf8-560">**Impostazione predefinita**: `false`</span><span class="sxs-lookup"><span data-stu-id="91cf8-560">**Default**: `false`</span></span>  
<span data-ttu-id="91cf8-561">**Variabile di ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="91cf8-561">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="91cf8-562">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-562">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="91cf8-563">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="91cf8-563">StartupAssembly</span></span>

<span data-ttu-id="91cf8-564">L'assembly per la ricerca della classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-564">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="91cf8-565">**Chiave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="91cf8-565">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="91cf8-566">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-566">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-567">**Impostazione predefinita**: assembly dell'app</span><span class="sxs-lookup"><span data-stu-id="91cf8-567">**Default**: The app's assembly</span></span>  
<span data-ttu-id="91cf8-568">**Variabile di ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="91cf8-568">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="91cf8-569">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-569">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="91cf8-570">`UseStartup` può richiedere un nome di assembly (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="91cf8-570">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="91cf8-571">Se vengono chiamati più metodi `UseStartup`, l'ultimo metodo ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="91cf8-571">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="91cf8-572">URL</span><span class="sxs-lookup"><span data-stu-id="91cf8-572">URLs</span></span>

<span data-ttu-id="91cf8-573">Elenco delimitato da punto e virgola degli indirizzi IP o gli indirizzi host con le porte e protocolli su cui il server deve eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="91cf8-573">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="91cf8-574">Ad esempio: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-574">For example, `http://localhost:123`.</span></span> <span data-ttu-id="91cf8-575">Usare "\*" per indicare che il server deve eseguire l'ascolto per le richieste su tutti gli indirizzi IP o nomi host usando la porta e il protocollo specificati (ad esempio, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="91cf8-575">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="91cf8-576">Il protocollo (`http://` o `https://`) deve essere incluso con ogni URL.</span><span class="sxs-lookup"><span data-stu-id="91cf8-576">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="91cf8-577">I formati supportati variano a seconda del server.</span><span class="sxs-lookup"><span data-stu-id="91cf8-577">Supported formats vary among servers.</span></span>

<span data-ttu-id="91cf8-578">**Chiave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="91cf8-578">**Key**: `urls`</span></span>  
<span data-ttu-id="91cf8-579">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-579">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-580">**Impostazione predefinita**: `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="91cf8-580">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="91cf8-581">**Variabile di ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="91cf8-581">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="91cf8-582">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-582">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="91cf8-583">Kestrel ha una propria API di configurazione degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="91cf8-583">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="91cf8-584">Per altre informazioni, vedere <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-584">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="91cf8-585">WebRoot</span><span class="sxs-lookup"><span data-stu-id="91cf8-585">WebRoot</span></span>

<span data-ttu-id="91cf8-586">La proprietà [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina il percorso relativo degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-586">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="91cf8-587">Se il percorso non esiste, viene usato un provider di file no-op.</span><span class="sxs-lookup"><span data-stu-id="91cf8-587">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="91cf8-588">**Chiave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="91cf8-588">**Key**: `webroot`</span></span>  
<span data-ttu-id="91cf8-589">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-589">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-590">**Impostazione predefinita**: il valore predefinito è `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="91cf8-590">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="91cf8-591">Il percorso di *{radice del contenuto}/wwwroot* deve esistere.</span><span class="sxs-lookup"><span data-stu-id="91cf8-591">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="91cf8-592">**Variabile di ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="91cf8-592">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="91cf8-593">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseWebRoot` su `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-593">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="91cf8-594">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="91cf8-594">For more information, see:</span></span>

* [<span data-ttu-id="91cf8-595">Nozioni fondamentali: radice Web</span><span class="sxs-lookup"><span data-stu-id="91cf8-595">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="91cf8-596">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="91cf8-596">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="91cf8-597">Gestire la durata dell'host</span><span class="sxs-lookup"><span data-stu-id="91cf8-597">Manage the host lifetime</span></span>

<span data-ttu-id="91cf8-598">Chiamare metodi sull'implementazione <xref:Microsoft.Extensions.Hosting.IHost> incorporata per avviare e arrestare l'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-598">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="91cf8-599">Questi metodi influiscono su tutte le implementazioni <xref:Microsoft.Extensions.Hosting.IHostedService> che vengono registrate nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="91cf8-599">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="91cf8-600">Esegui</span><span class="sxs-lookup"><span data-stu-id="91cf8-600">Run</span></span>

<span data-ttu-id="91cf8-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> esegue l'app e blocca il thread di chiamata fino all'arresto dell'host.</span><span class="sxs-lookup"><span data-stu-id="91cf8-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="91cf8-602">RunAsync</span><span class="sxs-lookup"><span data-stu-id="91cf8-602">RunAsync</span></span>

<span data-ttu-id="91cf8-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> esegue l'app e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="91cf8-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="91cf8-604">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="91cf8-604">RunConsoleAsync</span></span>

<span data-ttu-id="91cf8-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Abilita il supporto della console, compila e avvia l'host e attende la <kbd>Ctrl</kbd> + chiusura di CTRL<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="91cf8-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="91cf8-606">Avvio</span><span class="sxs-lookup"><span data-stu-id="91cf8-606">Start</span></span>

<span data-ttu-id="91cf8-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> avvia l'host in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="91cf8-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="91cf8-608">StartAsync</span><span class="sxs-lookup"><span data-stu-id="91cf8-608">StartAsync</span></span>

<span data-ttu-id="91cf8-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> avvia l'host e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="91cf8-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="91cf8-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> viene chiamato all'avvio di `StartAsync`, che attende il completamento prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="91cf8-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="91cf8-611">Questo è utile per ritardare l'avvio fino al segnale trasmesso da un evento esterno.</span><span class="sxs-lookup"><span data-stu-id="91cf8-611">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="91cf8-612">StopAsync</span><span class="sxs-lookup"><span data-stu-id="91cf8-612">StopAsync</span></span>

<span data-ttu-id="91cf8-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> prova ad arrestare l'host entro il timeout specificato.</span><span class="sxs-lookup"><span data-stu-id="91cf8-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="91cf8-614">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="91cf8-614">WaitForShutdown</span></span>

<span data-ttu-id="91cf8-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blocca il thread chiamante finché l'arresto non viene attivato da IHostLifetime, ad esempio tramite <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="91cf8-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="91cf8-616">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="91cf8-616">WaitForShutdownAsync</span></span>

<span data-ttu-id="91cf8-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato l'arresto tramite il token specificato, quindi chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="91cf8-618">Controllo esterno</span><span class="sxs-lookup"><span data-stu-id="91cf8-618">External control</span></span>

<span data-ttu-id="91cf8-619">Il controllo diretto della durata dell'host può essere ottenuto usando metodi che possono essere chiamati dall'esterno:</span><span class="sxs-lookup"><span data-stu-id="91cf8-619">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="91cf8-620">Le app ASP.NET Core configurano e avviano un host.</span><span class="sxs-lookup"><span data-stu-id="91cf8-620">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="91cf8-621">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-621">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="91cf8-622">Questo articolo illustra l'host generico di ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), che si usa per le app che non elaborano le richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="91cf8-622">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="91cf8-623">Lo scopo dell'host generico è separare la pipeline HTTP dall'API dell'host Web, per abilitare una gamma più ampia di scenari host.</span><span class="sxs-lookup"><span data-stu-id="91cf8-623">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="91cf8-624">La messaggistica, le attività in background e altri carichi di lavoro non HTTP basati sull'host generico traggono vantaggio dalle funzionalità a montaggio incrociato come la configurazione, l'inserimento di dipendenze (DI) e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="91cf8-624">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="91cf8-625">L'host generico è una nuova funzionalità introdotta in ASP.NET Core 2.1 e non è adatto agli scenari di hosting Web.</span><span class="sxs-lookup"><span data-stu-id="91cf8-625">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="91cf8-626">Per gli scenari di hosting Web usare l'[host Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="91cf8-626">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="91cf8-627">L'host generico sostituirà l'host Web in una versione futura, funzionando come API host principale negli scenari sia HTTP che non HTTP.</span><span class="sxs-lookup"><span data-stu-id="91cf8-627">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="91cf8-628">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="91cf8-628">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="91cf8-629">Quando si esegue l'app di esempio in [Visual Studio Code](https://code.visualstudio.com/), usare un *terminale esterno o integrato*.</span><span class="sxs-lookup"><span data-stu-id="91cf8-629">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="91cf8-630">Non eseguire l'esempio in un ambiente `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-630">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="91cf8-631">Per impostare la console in Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="91cf8-631">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="91cf8-632">Aprire il file *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="91cf8-632">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="91cf8-633">Nella configurazione **.NET Core Launch (console)** trovare la voce **console**.</span><span class="sxs-lookup"><span data-stu-id="91cf8-633">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="91cf8-634">Impostare il valore su `externalTerminal` o `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-634">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="91cf8-635">Introduzione</span><span class="sxs-lookup"><span data-stu-id="91cf8-635">Introduction</span></span>

<span data-ttu-id="91cf8-636">La libreria host generico è disponibile nello spazio dei nomi <xref:Microsoft.Extensions.Hosting> e viene specificata dal pacchetto [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="91cf8-636">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="91cf8-637">Il pacchetto `Microsoft.Extensions.Hosting` è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 o versioni successive).</span><span class="sxs-lookup"><span data-stu-id="91cf8-637">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="91cf8-638"><xref:Microsoft.Extensions.Hosting.IHostedService> è il punto di ingresso per l'esecuzione del codice.</span><span class="sxs-lookup"><span data-stu-id="91cf8-638"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="91cf8-639">Ogni implementazione <xref:Microsoft.Extensions.Hosting.IHostedService> viene eseguita nell'ordine di [registrazione del servizio in ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="91cf8-639">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="91cf8-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> viene chiamato su ogni <xref:Microsoft.Extensions.Hosting.IHostedService> quando viene avviato l'host e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> viene chiamato in ordine di registrazione inverso quando l'host viene chiuso normalmente.</span><span class="sxs-lookup"><span data-stu-id="91cf8-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="91cf8-641">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="91cf8-641">Set up a host</span></span>

<span data-ttu-id="91cf8-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> è il componente principale che le librerie e le app usano per inizializzare, compilare ed eseguire l'host:</span><span class="sxs-lookup"><span data-stu-id="91cf8-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="91cf8-643">Opzioni</span><span class="sxs-lookup"><span data-stu-id="91cf8-643">Options</span></span>

<span data-ttu-id="91cf8-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configura le opzioni per <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="91cf8-645">Timeout di arresto</span><span class="sxs-lookup"><span data-stu-id="91cf8-645">Shutdown timeout</span></span>

<span data-ttu-id="91cf8-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> imposta il timeout per <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="91cf8-647">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="91cf8-647">The default value is five seconds.</span></span>

<span data-ttu-id="91cf8-648">La seguente configurazione di opzione in `Program.Main` aumenta il timeout predefinito di cinque secondi di chiusura a 20 secondi:</span><span class="sxs-lookup"><span data-stu-id="91cf8-648">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="91cf8-649">Servizi predefiniti</span><span class="sxs-lookup"><span data-stu-id="91cf8-649">Default services</span></span>

<span data-ttu-id="91cf8-650">I servizi seguenti vengono registrati durante l'inizializzazione dell'host:</span><span class="sxs-lookup"><span data-stu-id="91cf8-650">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="91cf8-651">[Ambiente](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="91cf8-651">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="91cf8-652">[Configurazione](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="91cf8-652">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="91cf8-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="91cf8-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="91cf8-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="91cf8-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="91cf8-655">[Opzioni](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="91cf8-655">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="91cf8-656">[Registrazione](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="91cf8-656">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="91cf8-657">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="91cf8-657">Host configuration</span></span>

<span data-ttu-id="91cf8-658">La configurazione dell'host viene creata tramite:</span><span class="sxs-lookup"><span data-stu-id="91cf8-658">Host configuration is created by:</span></span>

* <span data-ttu-id="91cf8-659">Chiamata ai metodi di estensione su <xref:Microsoft.Extensions.Hosting.IHostBuilder> per impostare la [radice del contenuto](#content-root) e l'[ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="91cf8-659">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="91cf8-660">Lettura della configurazione dai provider di configurazione in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-660">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="91cf8-661">Metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="91cf8-661">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="91cf8-662">Chiave applicazione (nome)</span><span class="sxs-lookup"><span data-stu-id="91cf8-662">Application key (name)</span></span>

<span data-ttu-id="91cf8-663">La proprietà [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) viene impostata dalla configurazione dell'host durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="91cf8-663">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="91cf8-664">Per impostare il valore in modo esplicito, usare [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="91cf8-664">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="91cf8-665">**Chiave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="91cf8-665">**Key**: `applicationName`</span></span>  
<span data-ttu-id="91cf8-666">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-666">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-667">**Impostazione predefinita**: il nome dell'assembly contenente il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-667">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="91cf8-668">**Imposta utilizzando**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="91cf8-668">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="91cf8-669">**Variabile di ambiente**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` è [facoltativo e definito dall'utente](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="91cf8-669">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="91cf8-670">Radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="91cf8-670">Content root</span></span>

<span data-ttu-id="91cf8-671">Questa impostazione determina la posizione da cui l'host inizia la ricerca dei file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="91cf8-671">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="91cf8-672">**Chiave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="91cf8-672">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="91cf8-673">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-673">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-674">**Impostazione predefinita**: il valore predefinito corrisponde alla cartella contenente l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-674">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="91cf8-675">**Imposta utilizzando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="91cf8-675">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="91cf8-676">**Variabile di ambiente**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` è [facoltativo e definito dall'utente](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="91cf8-676">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="91cf8-677">Se il percorso non esiste, l'host non verrà avviato.</span><span class="sxs-lookup"><span data-stu-id="91cf8-677">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="91cf8-678">Per altre informazioni, vedere [nozioni fondamentali: radice del contenuto](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="91cf8-678">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="91cf8-679">Ambiente</span><span class="sxs-lookup"><span data-stu-id="91cf8-679">Environment</span></span>

<span data-ttu-id="91cf8-680">Imposta l' [ambiente](xref:fundamentals/environments)dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-680">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="91cf8-681">**Chiave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="91cf8-681">**Key**: `environment`</span></span>  
<span data-ttu-id="91cf8-682">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="91cf8-682">**Type**: `string`</span></span>  
<span data-ttu-id="91cf8-683">**Impostazione predefinita**: `Production`</span><span class="sxs-lookup"><span data-stu-id="91cf8-683">**Default**: `Production`</span></span>  
<span data-ttu-id="91cf8-684">**Imposta utilizzando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="91cf8-684">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="91cf8-685">**Variabile di ambiente**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` è [facoltativo e definito dall'utente](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="91cf8-685">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="91cf8-686">L'ambiente può essere impostato su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="91cf8-686">The environment can be set to any value.</span></span> <span data-ttu-id="91cf8-687">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-687">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="91cf8-688">I valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="91cf8-688">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="91cf8-689">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="91cf8-689">ConfigureHostConfiguration</span></span>

<span data-ttu-id="91cf8-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per creare un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfiguration> per l'host.</span><span class="sxs-lookup"><span data-stu-id="91cf8-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="91cf8-691">La configurazione dell'host viene usata per inizializzare l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> da usare nel processo di compilazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-691">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="91cf8-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="91cf8-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="91cf8-693">L'host usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="91cf8-693">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="91cf8-694">Nessun provider è incluso per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="91cf8-694">No providers are included by default.</span></span> <span data-ttu-id="91cf8-695">È necessario specificare in modo esplicito tutti i provider di configurazione richiesti dall'app in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, inclusi:</span><span class="sxs-lookup"><span data-stu-id="91cf8-695">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="91cf8-696">Configurazione dei file (ad esempio, da un file *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="91cf8-696">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="91cf8-697">Configurazione delle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="91cf8-697">Environment variable configuration.</span></span>
* <span data-ttu-id="91cf8-698">Configurazione degli argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="91cf8-698">Command-line argument configuration.</span></span>
* <span data-ttu-id="91cf8-699">Tutti gli altri provider di configurazione necessari.</span><span class="sxs-lookup"><span data-stu-id="91cf8-699">Any other required configuration providers.</span></span>

<span data-ttu-id="91cf8-700">La configurazione file dell'host viene abilitata specificando il percorso di base dell'app con `SetBasePath` seguito da una chiamata a uno dei [provider di configurazione dei file](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="91cf8-700">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="91cf8-701">L'app di esempio usa un file JSON, *hostsettings.json*, e chiama <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> per utilizzare le impostazioni di configurazione host del file.</span><span class="sxs-lookup"><span data-stu-id="91cf8-701">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="91cf8-702">Per aggiungere la [configurazione delle variabili di ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) dell'host, chiamare <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sul generatore di host.</span><span class="sxs-lookup"><span data-stu-id="91cf8-702">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="91cf8-703">`AddEnvironmentVariables` accetta un prefisso facoltativo definito dall'utente.</span><span class="sxs-lookup"><span data-stu-id="91cf8-703">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="91cf8-704">L'app di esempio usa un prefisso di `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-704">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="91cf8-705">Il prefisso viene rimosso quando vengono lette le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="91cf8-705">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="91cf8-706">Quando viene configurato l'host dell'app di esempio, il valore della variabile di ambiente per `PREFIX_ENVIRONMENT` diventa il valore di configurazione dell'host per la chiave `environment`.</span><span class="sxs-lookup"><span data-stu-id="91cf8-706">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="91cf8-707">Durante lo sviluppo quando si usa [Visual Studio](https://visualstudio.microsoft.com) o si esegue un'app con `dotnet run`, le variabili di ambiente possono essere impostate nel file *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="91cf8-707">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="91cf8-708">Durante lo sviluppo in [Visual Studio Code](https://code.visualstudio.com/), le variabili di ambiente possono essere impostate nel file *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="91cf8-708">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="91cf8-709">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-709">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="91cf8-710">La [configurazione della riga di comando](xref:fundamentals/configuration/index#command-line-configuration-provider) viene aggiunta chiamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-710">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="91cf8-711">La configurazione della riga di comando viene aggiunta per ultima per consentire agli argomenti della riga di comando di eseguire l'override della configurazione fornita dai provider di configurazione precedenti.</span><span class="sxs-lookup"><span data-stu-id="91cf8-711">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="91cf8-712">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="91cf8-712">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="91cf8-713">È possibile fornire una configurazione aggiuntiva con le chiavi [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="91cf8-713">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="91cf8-714">Configurazione di esempio di `HostBuilder` che usa <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="91cf8-714">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="91cf8-715">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="91cf8-715">ConfigureAppConfiguration</span></span>

<span data-ttu-id="91cf8-716">La configurazione dell'app viene creata chiamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sull'implementazione <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-716">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="91cf8-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per creare un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfiguration> per l'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="91cf8-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="91cf8-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="91cf8-719">L'app usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="91cf8-719">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="91cf8-720">La configurazione creata da <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> è disponibile in [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) per le operazioni successive e in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-720">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="91cf8-721">La configurazione dell'app riceve automaticamente la configurazione dell'host fornita da [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="91cf8-721">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="91cf8-722">Configurazione app di esempio che usa <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="91cf8-722">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="91cf8-723">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="91cf8-723">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="91cf8-724">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="91cf8-724">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="91cf8-725">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="91cf8-725">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="91cf8-726">Per spostare i file delle impostazioni nella directory di output, specificare i file delle impostazioni come [elementi di progetto MSBuild](/visualstudio/msbuild/common-msbuild-project-items) nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="91cf8-726">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="91cf8-727">L'app di esempio sposta i file delle impostazioni dell'app JSON e *hostsettings.json* con l'elemento `<Content>` seguente:</span><span class="sxs-lookup"><span data-stu-id="91cf8-727">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="91cf8-728">I metodi di estensione di configurazione, ad esempio <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> e <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, necessitano di altri pacchetti NuGet, ad esempio [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) e [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="91cf8-728">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="91cf8-729">A meno che l'app non usi il [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), questi pacchetti devono essere aggiunti al progetto oltre al pacchetto [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) di base.</span><span class="sxs-lookup"><span data-stu-id="91cf8-729">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="91cf8-730">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-730">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="91cf8-731">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="91cf8-731">ConfigureServices</span></span>

<span data-ttu-id="91cf8-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> aggiunge servizi al contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="91cf8-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="91cf8-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="91cf8-734">Un servizio ospitato è una classe con logica di attività in background che implementa l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-734">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="91cf8-735">Per altre informazioni, vedere <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-735">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="91cf8-736">L'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa il metodo di estensione `AddHostedService` per aggiungere all'app un servizio per gli eventi di durata (`LifetimeEventsHostedService`) e un'attività in background con timeout (`TimedHostedService`):</span><span class="sxs-lookup"><span data-stu-id="91cf8-736">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="91cf8-737">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="91cf8-737">ConfigureLogging</span></span>

<span data-ttu-id="91cf8-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> aggiunge un delegato per configurare l'interfaccia <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornita.</span><span class="sxs-lookup"><span data-stu-id="91cf8-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="91cf8-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="91cf8-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="91cf8-740">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="91cf8-740">UseConsoleLifetime</span></span>

<span data-ttu-id="91cf8-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>Ascolta la <kbd>combinazione di tasti CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM e chiama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> per avviare il processo di arresto.</span><span class="sxs-lookup"><span data-stu-id="91cf8-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="91cf8-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> Sblocca le estensioni, ad esempio [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="91cf8-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="91cf8-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` è preregistrata come implementazione di durata predefinita.</span><span class="sxs-lookup"><span data-stu-id="91cf8-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="91cf8-744">Viene usata l'ultima durata registrata.</span><span class="sxs-lookup"><span data-stu-id="91cf8-744">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="91cf8-745">Configurazione contenitore</span><span class="sxs-lookup"><span data-stu-id="91cf8-745">Container configuration</span></span>

<span data-ttu-id="91cf8-746">Per supportare l'inserimento di altri contenitori, l'host può accettare un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-746">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="91cf8-747">La disponibilità di una factory non fa parte della registrazione del contenitore DI, ma è una funzionalità intrinseca dell'host usata per creare il contenitore DI concreto.</span><span class="sxs-lookup"><span data-stu-id="91cf8-747">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="91cf8-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) esegue l'override della factory predefinita usata per la creazione del provider di servizi dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="91cf8-749">La configurazione del contenitore personalizzato è gestita dal metodo <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-749">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="91cf8-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> offre un'esperienza fortemente tipizzata per la configurazione del contenitore sulla base dell'API host sottostante.</span><span class="sxs-lookup"><span data-stu-id="91cf8-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="91cf8-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="91cf8-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="91cf8-752">Creare un contenitore di servizi per l'app:</span><span class="sxs-lookup"><span data-stu-id="91cf8-752">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="91cf8-753">Specificare una factory per il contenitore di servizi:</span><span class="sxs-lookup"><span data-stu-id="91cf8-753">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="91cf8-754">Usare la factory e configurare il contenitore di servizi personalizzato per l'app:</span><span class="sxs-lookup"><span data-stu-id="91cf8-754">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="91cf8-755">Estendibilità</span><span class="sxs-lookup"><span data-stu-id="91cf8-755">Extensibility</span></span>

<span data-ttu-id="91cf8-756">L'estensibilità host viene eseguita con i metodi di estensione su <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-756">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="91cf8-757">L'esempio seguente illustra come un metodo di estensione estende un'implementazione <xref:Microsoft.Extensions.Hosting.IHostBuilder> con l'esempio [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) dimostrato in <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-757">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="91cf8-758">Un'app stabilisce il metodo di estensione `UseHostedService` per registrare il servizio ospitato passato in `T`:</span><span class="sxs-lookup"><span data-stu-id="91cf8-758">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="91cf8-759">Gestire l'host</span><span class="sxs-lookup"><span data-stu-id="91cf8-759">Manage the host</span></span>

<span data-ttu-id="91cf8-760">L'implementazione <xref:Microsoft.Extensions.Hosting.IHost> è responsabile dell'avvio e arresto delle implementazioni <xref:Microsoft.Extensions.Hosting.IHostedService> che sono registrate nel contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="91cf8-760">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="91cf8-761">Esegui</span><span class="sxs-lookup"><span data-stu-id="91cf8-761">Run</span></span>

<span data-ttu-id="91cf8-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> esegue l'app e blocca il thread di chiamata fino all'arresto dell'host:</span><span class="sxs-lookup"><span data-stu-id="91cf8-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="91cf8-763">RunAsync</span><span class="sxs-lookup"><span data-stu-id="91cf8-763">RunAsync</span></span>

<span data-ttu-id="91cf8-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> esegue l'app e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto:</span><span class="sxs-lookup"><span data-stu-id="91cf8-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="91cf8-765">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="91cf8-765">RunConsoleAsync</span></span>

<span data-ttu-id="91cf8-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Abilita il supporto della console, compila e avvia l'host e attende la <kbd>Ctrl</kbd> + chiusura di CTRL<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="91cf8-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="91cf8-767">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="91cf8-767">Start and StopAsync</span></span>

<span data-ttu-id="91cf8-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> avvia l'host in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="91cf8-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="91cf8-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> prova ad arrestare l'host entro il timeout specificato.</span><span class="sxs-lookup"><span data-stu-id="91cf8-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="91cf8-770">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="91cf8-770">StartAsync and StopAsync</span></span>

<span data-ttu-id="91cf8-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> avvia l'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="91cf8-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> arresta l'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="91cf8-773">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="91cf8-773">WaitForShutdown</span></span>

<span data-ttu-id="91cf8-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>viene attivato tramite <xref:Microsoft.Extensions.Hosting.IHostLifetime> , ad esempio (in `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` attesa di <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="91cf8-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="91cf8-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="91cf8-776">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="91cf8-776">WaitForShutdownAsync</span></span>

<span data-ttu-id="91cf8-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato l'arresto tramite il token specificato, quindi chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="91cf8-778">Controllo esterno</span><span class="sxs-lookup"><span data-stu-id="91cf8-778">External control</span></span>

<span data-ttu-id="91cf8-779">Il controllo esterno dell'host può essere ottenuto usando metodi che possono essere chiamati dall'esterno:</span><span class="sxs-lookup"><span data-stu-id="91cf8-779">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="91cf8-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> viene chiamato all'avvio di <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, che attende il completamento prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="91cf8-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="91cf8-781">Questo è utile per ritardare l'avvio fino al segnale trasmesso da un evento esterno.</span><span class="sxs-lookup"><span data-stu-id="91cf8-781">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="91cf8-782">Interfaccia IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="91cf8-782">IHostingEnvironment interface</span></span>

<span data-ttu-id="91cf8-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> include informazioni sull'ambiente di hosting dell'app.</span><span class="sxs-lookup"><span data-stu-id="91cf8-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="91cf8-784">Usare l'[inserimento di un costruttore](xref:fundamentals/dependency-injection) per ottenere <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> per poterne usare le proprietà e i metodi di estensione:</span><span class="sxs-lookup"><span data-stu-id="91cf8-784">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="91cf8-785">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="91cf8-785">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="91cf8-786">Interfaccia IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="91cf8-786">IApplicationLifetime interface</span></span>

<span data-ttu-id="91cf8-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> consente attività post-avvio e di arresto, incluse le richieste di arresto normale.</span><span class="sxs-lookup"><span data-stu-id="91cf8-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="91cf8-788">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi <xref:System.Action> che definiscono gli eventi di avvio e arresto.</span><span class="sxs-lookup"><span data-stu-id="91cf8-788">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="91cf8-789">Token di annullamento</span><span class="sxs-lookup"><span data-stu-id="91cf8-789">Cancellation Token</span></span> | <span data-ttu-id="91cf8-790">Attivato quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="91cf8-790">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="91cf8-791">L'host è stato completamente avviato.</span><span class="sxs-lookup"><span data-stu-id="91cf8-791">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="91cf8-792">L'host sta completando un arresto normale.</span><span class="sxs-lookup"><span data-stu-id="91cf8-792">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="91cf8-793">Tutte le richieste devono essere elaborate.</span><span class="sxs-lookup"><span data-stu-id="91cf8-793">All requests should be processed.</span></span> <span data-ttu-id="91cf8-794">L'arresto è bloccato fino al completamento di questo evento.</span><span class="sxs-lookup"><span data-stu-id="91cf8-794">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="91cf8-795">L'host sta eseguendo un arresto normale.</span><span class="sxs-lookup"><span data-stu-id="91cf8-795">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="91cf8-796">È possibile che le richieste siano ancora in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="91cf8-796">Requests may still be processing.</span></span> <span data-ttu-id="91cf8-797">L'arresto è bloccato fino al completamento di questo evento.</span><span class="sxs-lookup"><span data-stu-id="91cf8-797">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="91cf8-798">Inserire tramite costruttore il servizio <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> in qualsiasi classe.</span><span class="sxs-lookup"><span data-stu-id="91cf8-798">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="91cf8-799">L'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa l'inserimento di costruttori in una classe `LifetimeEventsHostedService` (implementazione <xref:Microsoft.Extensions.Hosting.IHostedService>) per registrare gli eventi.</span><span class="sxs-lookup"><span data-stu-id="91cf8-799">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="91cf8-800">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="91cf8-800">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="91cf8-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> richiede la chiusura dell'applicazione corrente.</span><span class="sxs-lookup"><span data-stu-id="91cf8-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="91cf8-802">La classe seguente usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> per arrestare normalmente un'app quando viene chiamato il metodo `Shutdown` della classe:</span><span class="sxs-lookup"><span data-stu-id="91cf8-802">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="91cf8-803">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="91cf8-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
