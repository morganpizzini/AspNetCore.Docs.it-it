---
title: Host generico .NET
author: rick-anderson
description: Informazioni sull'host generico .NET Core, che gestisce l'avvio e la durata delle app.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: b528a33fa52bfe56faaf9f3ff8c7e43db0d4e184
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384028"
---
# <a name="net-generic-host"></a><span data-ttu-id="4af81-103">Host generico .NET</span><span class="sxs-lookup"><span data-stu-id="4af81-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="4af81-104">I modelli ASP.NET Core creano un<xref:Microsoft.Extensions.Hosting.HostBuilder>host generico .NET Core ( ).</span><span class="sxs-lookup"><span data-stu-id="4af81-104">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="4af81-105">Definizione host</span><span class="sxs-lookup"><span data-stu-id="4af81-105">Host definition</span></span>

<span data-ttu-id="4af81-106">Un *host* è un oggetto che incapsula le risorse di un'app, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="4af81-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="4af81-107">Inserimento di dipendenze (DI)</span><span class="sxs-lookup"><span data-stu-id="4af81-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="4af81-108">Registrazione</span><span class="sxs-lookup"><span data-stu-id="4af81-108">Logging</span></span>
* <span data-ttu-id="4af81-109">Configurazione</span><span class="sxs-lookup"><span data-stu-id="4af81-109">Configuration</span></span>
* <span data-ttu-id="4af81-110">`IHostedService` Implementazioni</span><span class="sxs-lookup"><span data-stu-id="4af81-110">`IHostedService` implementations</span></span>

<span data-ttu-id="4af81-111">Quando un host viene avviato, chiama `IHostedService.StartAsync` in ogni implementazione di <xref:Microsoft.Extensions.Hosting.IHostedService> che trova nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="4af81-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="4af81-112">In un'app Web, una delle implementazioni di `IHostedService` è un servizio web che avvia un'[implementazione del server HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="4af81-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="4af81-113">Il motivo principale per cui tutte le risorse interdipendenti dell'app sono incluse in un unico oggetto è la gestione del ciclo di vita, vale a dire il controllo sull'avvio dell'app e sull'arresto normale.</span><span class="sxs-lookup"><span data-stu-id="4af81-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="4af81-114">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="4af81-114">Set up a host</span></span>

<span data-ttu-id="4af81-115">L'host è in genere configurato, compilato ed eseguito da codice nella classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="4af81-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="4af81-116">Il metodo `Main`:</span><span class="sxs-lookup"><span data-stu-id="4af81-116">The `Main` method:</span></span>

* <span data-ttu-id="4af81-117">Chiama un metodo `CreateHostBuilder` per creare e configurare un oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="4af81-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="4af81-118">Chiamate `Build` e metodi `Run` nell'oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="4af81-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="4af81-119">I modelli Web ASP.NET Core generano il codice seguente per creare un host:The ASP.NET Core web templates generate the following code to create a host:</span><span class="sxs-lookup"><span data-stu-id="4af81-119">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="4af81-120">Il codice seguente crea un carico `IHostedService` di lavoro non HTTP con un'implementazione aggiunta al contenitore DI.</span><span class="sxs-lookup"><span data-stu-id="4af81-120">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="4af81-121">Per un carico di lavoro HTTP, il metodo `Main` è lo stesso ma `CreateHostBuilder` chiama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="4af81-121">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="4af81-122">Se l'app usa Entity Framework Core, non modificare il nome o la firma del metodo `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4af81-122">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="4af81-123">Gli [strumenti di Entity Framework Core](/ef/core/miscellaneous/cli/) si aspettano di trovare un metodo `CreateHostBuilder` che configura l'host senza eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-123">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="4af81-124">Per altre informazioni, vedere [Creazione DbContext in fase di progettazione](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="4af81-124">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="4af81-125">Impostazioni predefinite del generatore</span><span class="sxs-lookup"><span data-stu-id="4af81-125">Default builder settings</span></span>

<span data-ttu-id="4af81-126">Il metodo <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="4af81-126">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="4af81-127">Imposta la radice del [contenuto](xref:fundamentals/index#content-root) <xref:System.IO.Directory.GetCurrentDirectory*>sul percorso restituito da .</span><span class="sxs-lookup"><span data-stu-id="4af81-127">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="4af81-128">Carica la configurazione dell'host da:</span><span class="sxs-lookup"><span data-stu-id="4af81-128">Loads host configuration from:</span></span>
  * <span data-ttu-id="4af81-129">Variabili di ambiente `DOTNET_`precedute da .</span><span class="sxs-lookup"><span data-stu-id="4af81-129">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="4af81-130">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="4af81-130">Command-line arguments.</span></span>
* <span data-ttu-id="4af81-131">Carica la configurazione dell'app da:</span><span class="sxs-lookup"><span data-stu-id="4af81-131">Loads app configuration from:</span></span>
  * <span data-ttu-id="4af81-132">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4af81-132">*appsettings.json*.</span></span>
  * <span data-ttu-id="4af81-133">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="4af81-133">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="4af81-134">[Segreti del manager](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="4af81-134">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="4af81-135">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="4af81-135">Environment variables.</span></span>
  * <span data-ttu-id="4af81-136">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="4af81-136">Command-line arguments.</span></span>
* <span data-ttu-id="4af81-137">Aggiunge i provider di [log](xref:fundamentals/logging/index) seguenti:</span><span class="sxs-lookup"><span data-stu-id="4af81-137">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="4af81-138">Console</span><span class="sxs-lookup"><span data-stu-id="4af81-138">Console</span></span>
  * <span data-ttu-id="4af81-139">Debug</span><span class="sxs-lookup"><span data-stu-id="4af81-139">Debug</span></span>
  * <span data-ttu-id="4af81-140">EventSource</span><span class="sxs-lookup"><span data-stu-id="4af81-140">EventSource</span></span>
  * <span data-ttu-id="4af81-141">EventLog (solo quando è in esecuzione su Windows)</span><span class="sxs-lookup"><span data-stu-id="4af81-141">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="4af81-142">Abilita la [convalida dell'ambito](xref:fundamentals/dependency-injection#scope-validation) e la [convalida delle dipendenze](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando l'ambiente è lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="4af81-142">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="4af81-143">Il metodo `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="4af81-143">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="4af81-144">Carica la configurazione host dalle `ASPNETCORE_`variabili di ambiente precedute da .</span><span class="sxs-lookup"><span data-stu-id="4af81-144">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="4af81-145">Imposta il server [Kestrel](xref:fundamentals/servers/kestrel) come server Web e lo configura usando i provider di configurazione dell'host dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-145">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="4af81-146">Per le opzioni predefinite del server Kestrel, vedere <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="4af81-146">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="4af81-147">Aggiunge [il middleware dell'applicazione di filtri dell'host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="4af81-147">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="4af81-148">Aggiunge il [middleware Intestazioni inoltrate](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è uguale `true`a .</span><span class="sxs-lookup"><span data-stu-id="4af81-148">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="4af81-149">Abilita l'integrazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="4af81-149">Enables IIS integration.</span></span> <span data-ttu-id="4af81-150">Per le opzioni predefinite di IIS, vedere <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="4af81-150">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="4af81-151">Le sezioni [Impostazioni per tutti i tipi di app](#settings-for-all-app-types) e [Impostazioni per le app Web](#settings-for-web-apps) più avanti in questo articolo mostrano come eseguire l'override delle impostazioni predefinite del generatore.</span><span class="sxs-lookup"><span data-stu-id="4af81-151">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="4af81-152">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="4af81-152">Framework-provided services</span></span>

<span data-ttu-id="4af81-153">I seguenti servizi vengono registrati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="4af81-153">The following services are registered automatically:</span></span>

* [<span data-ttu-id="4af81-154">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4af81-154">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="4af81-155">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4af81-155">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="4af81-156">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4af81-156">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="4af81-157">Per ulteriori informazioni sui servizi <xref:fundamentals/dependency-injection#framework-provided-services>forniti dal framework, vedere .</span><span class="sxs-lookup"><span data-stu-id="4af81-157">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="4af81-158">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4af81-158">IHostApplicationLifetime</span></span>

<span data-ttu-id="4af81-159">Inserire il servizio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (in precedenza `IApplicationLifetime`) in qualsiasi classe per gestire le attività post-avvio e di arresto normale.</span><span class="sxs-lookup"><span data-stu-id="4af81-159">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="4af81-160">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi del gestore dell'evento di avvio e di arresto.</span><span class="sxs-lookup"><span data-stu-id="4af81-160">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="4af81-161">L'interfaccia include anche un metodo `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="4af81-161">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="4af81-162">L'esempio seguente `IHostedService` è un'implementazione che registra gli eventi:The following example is an implementation that registers `IHostApplicationLifetime` events:</span><span class="sxs-lookup"><span data-stu-id="4af81-162">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="4af81-163">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4af81-163">IHostLifetime</span></span>

<span data-ttu-id="4af81-164">L'implementazione <xref:Microsoft.Extensions.Hosting.IHostLifetime> controlla quando l'host viene avviato e quando si arresta.</span><span class="sxs-lookup"><span data-stu-id="4af81-164">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="4af81-165">Viene usata l'ultima implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="4af81-165">The last implementation registered is used.</span></span>

<span data-ttu-id="4af81-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` è l'implementazione `IHostLifetime` predefinita.</span><span class="sxs-lookup"><span data-stu-id="4af81-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="4af81-167">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="4af81-167">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="4af81-168">Si ascolta <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> SIGTERM e chiama per avviare il processo di arresto.</span><span class="sxs-lookup"><span data-stu-id="4af81-168">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="4af81-169">Sblocca estensioni come [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="4af81-169">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="4af81-170">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4af81-170">IHostEnvironment</span></span>

<span data-ttu-id="4af81-171">Inserire <xref:Microsoft.Extensions.Hosting.IHostEnvironment> il servizio in una classe per ottenere informazioni sulle impostazioni seguenti:Inject the service into a class to get information about the following settings:</span><span class="sxs-lookup"><span data-stu-id="4af81-171">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="4af81-172">Applicationname</span><span class="sxs-lookup"><span data-stu-id="4af81-172">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="4af81-173">NomeAmbiente</span><span class="sxs-lookup"><span data-stu-id="4af81-173">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="4af81-174">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="4af81-174">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="4af81-175">Le app `IWebHostEnvironment` Web implementano `IHostEnvironment` l'interfaccia , che eredita e aggiunge [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="4af81-175">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="4af81-176">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="4af81-176">Host configuration</span></span>

<span data-ttu-id="4af81-177">La configurazione dell'host viene usata per le proprietà dell'implementazione <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="4af81-177">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="4af81-178">La configurazione dell'host è disponibile da [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) all'interno di <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="4af81-178">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="4af81-179">Dopo `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` viene sostituito con la configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-179">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="4af81-180">Per aggiungere la configurazione dell'host, chiamare <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4af81-180">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="4af81-181">`ConfigureHostConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="4af81-181">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4af81-182">L'host usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="4af81-182">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="4af81-183">Il provider della `DOTNET_` variabile di ambiente con `CreateDefaultBuilder`prefisso e argomenti della riga di comando sono inclusi da .</span><span class="sxs-lookup"><span data-stu-id="4af81-183">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4af81-184">Per le app Web, viene aggiunto il provider di variabili di ambiente con prefisso `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="4af81-184">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="4af81-185">Il prefisso viene rimosso quando vengono lette le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="4af81-185">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="4af81-186">Ad esempio, il valore della variabile di ambiente per `ASPNETCORE_ENVIRONMENT` diventa il valore di configurazione dell'host per la chiave `environment`.</span><span class="sxs-lookup"><span data-stu-id="4af81-186">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="4af81-187">L'esempio seguente crea la configurazione host:</span><span class="sxs-lookup"><span data-stu-id="4af81-187">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="4af81-188">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="4af81-188">App configuration</span></span>

<span data-ttu-id="4af81-189">La configurazione dell'app viene creata chiamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4af81-189">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="4af81-190">`ConfigureAppConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="4af81-190">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4af81-191">L'app usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="4af81-191">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="4af81-192">La configurazione creata da `ConfigureAppConfiguration` è disponibile in [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) per le operazioni successive e come servizio da DI.</span><span class="sxs-lookup"><span data-stu-id="4af81-192">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="4af81-193">La configurazione dell'host viene aggiunta anche alla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-193">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="4af81-194">Per altre informazioni, vedere [Configurazione in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="4af81-194">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="4af81-195">Impostazioni per tutti i tipi di app</span><span class="sxs-lookup"><span data-stu-id="4af81-195">Settings for all app types</span></span>

<span data-ttu-id="4af81-196">Questa sezione elenca le impostazioni dell'host che si applicano ai carichi di lavoro HTTP e non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="4af81-196">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="4af81-197">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="4af81-197">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="4af81-198">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="4af81-198">ApplicationName</span></span>

<span data-ttu-id="4af81-199">La proprietà [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) viene impostata dalla configurazione dell'host durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="4af81-199">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="4af81-200">**Tasto**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="4af81-200">**Key**: `applicationName`</span></span>  
<span data-ttu-id="4af81-201">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-201">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-202">**Default**: Il nome dell'assembly che contiene il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-202">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="4af81-203">**Variabile di ambiente**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="4af81-203">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="4af81-204">per impostare questo valore usare la variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="4af81-204">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="4af81-205">Radice contenuto</span><span class="sxs-lookup"><span data-stu-id="4af81-205">ContentRoot</span></span>

<span data-ttu-id="4af81-206">La proprietà [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina il punto in cui l'host inizia a cercare i file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="4af81-206">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="4af81-207">Se il percorso non esiste, l'host non verrà avviato.</span><span class="sxs-lookup"><span data-stu-id="4af81-207">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="4af81-208">**Tasto**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="4af81-208">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="4af81-209">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-209">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-210">**Predefinito:** la cartella in cui risiede l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-210">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="4af81-211">**Variabile di ambiente**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="4af81-211">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="4af81-212">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseContentRoot` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4af81-212">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="4af81-213">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="4af81-213">For more information, see:</span></span>

* [<span data-ttu-id="4af81-214">Nozioni fondamentali: radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="4af81-214">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="4af81-215">WebRoot</span><span class="sxs-lookup"><span data-stu-id="4af81-215">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="4af81-216">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="4af81-216">EnvironmentName</span></span>

<span data-ttu-id="4af81-217">La proprietà [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) può essere impostata su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="4af81-217">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="4af81-218">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="4af81-218">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="4af81-219">I valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="4af81-219">Values aren't case-sensitive.</span></span>

<span data-ttu-id="4af81-220">**Tasto**:`environment`</span><span class="sxs-lookup"><span data-stu-id="4af81-220">**Key**: `environment`</span></span>  
<span data-ttu-id="4af81-221">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-221">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-222">**Predefinito**:`Production`</span><span class="sxs-lookup"><span data-stu-id="4af81-222">**Default**: `Production`</span></span>  
<span data-ttu-id="4af81-223">**Variabile di ambiente**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="4af81-223">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="4af81-224">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseEnvironment` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4af81-224">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="4af81-225">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="4af81-225">ShutdownTimeout</span></span>

<span data-ttu-id="4af81-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) imposta il timeout per <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4af81-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="4af81-227">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="4af81-227">The default value is five seconds.</span></span>  <span data-ttu-id="4af81-228">Durante il periodo di timeout, l'host:</span><span class="sxs-lookup"><span data-stu-id="4af81-228">During the timeout period, the host:</span></span>

* <span data-ttu-id="4af81-229">attiva [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="4af81-229">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="4af81-230">Tenta di arrestare i servizi ospitati, registrando gli errori per i servizi che non si interrompono.</span><span class="sxs-lookup"><span data-stu-id="4af81-230">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="4af81-231">Se il periodo di timeout scade prima che siano stati arrestati tutti i servizi ospitati, gli eventuali servizi attivi rimanenti vengono interrotti quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="4af81-231">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="4af81-232">I servizi si arrestano anche se non hanno completato l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="4af81-232">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="4af81-233">Se l'arresto dei servizi richiede più tempo, aumentare il valore di timeout.</span><span class="sxs-lookup"><span data-stu-id="4af81-233">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="4af81-234">**Tasto**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="4af81-234">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="4af81-235">**Tipo**:`int`</span><span class="sxs-lookup"><span data-stu-id="4af81-235">**Type**: `int`</span></span>  
<span data-ttu-id="4af81-236">**Predefinito**: 5 secondi</span><span class="sxs-lookup"><span data-stu-id="4af81-236">**Default**: 5 seconds</span></span>  
<span data-ttu-id="4af81-237">**Variabile di ambiente**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="4af81-237">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="4af81-238">Per impostare questo valore, usare la variabile di ambiente o configurare `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="4af81-238">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="4af81-239">Nell'esempio seguente il timeout viene impostato su 20 secondi:</span><span class="sxs-lookup"><span data-stu-id="4af81-239">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="4af81-240">Impostazioni per le app Web</span><span class="sxs-lookup"><span data-stu-id="4af81-240">Settings for web apps</span></span>

<span data-ttu-id="4af81-241">Alcune impostazioni host si applicano solo ai carichi di lavoro HTTP.</span><span class="sxs-lookup"><span data-stu-id="4af81-241">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="4af81-242">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="4af81-242">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="4af81-243">Per queste impostazioni sono disponibili i metodi di estensione su `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4af81-243">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="4af81-244">Gli esempi di codice che illustrano come chiamare i metodi di estensione presumono che `webBuilder` sia un'istanza di `IWebHostBuilder`, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="4af81-244">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="4af81-245">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="4af81-245">CaptureStartupErrors</span></span>

<span data-ttu-id="4af81-246">Quando è `false`, gli errori durante l'avvio causano la chiusura dell'host.</span><span class="sxs-lookup"><span data-stu-id="4af81-246">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="4af81-247">Quando è `true`, l'host acquisisce le eccezioni durante l'avvio e tenta di avviare il server.</span><span class="sxs-lookup"><span data-stu-id="4af81-247">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="4af81-248">**Tasto**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="4af81-248">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="4af81-249">**Tipo** `bool` :`true` `1`( o )</span><span class="sxs-lookup"><span data-stu-id="4af81-249">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4af81-250">**Impostazione predefinita**: il valore predefinito è `false` a meno che l'app non venga eseguita con Kestrel in IIS, in tal caso il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="4af81-250">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="4af81-251">**Variabile di ambiente**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="4af81-251">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="4af81-252">Per impostare questo valore, usare la configurazione o chiamare `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="4af81-252">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="4af81-253">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="4af81-253">DetailedErrors</span></span>

<span data-ttu-id="4af81-254">Quando è abilitata o quando l'ambiente è `Development`, l'app acquisisce gli errori dettagliati.</span><span class="sxs-lookup"><span data-stu-id="4af81-254">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="4af81-255">**Tasto**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="4af81-255">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="4af81-256">**Tipo** `bool` :`true` `1`( o )</span><span class="sxs-lookup"><span data-stu-id="4af81-256">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4af81-257">**Predefinito**:`false`</span><span class="sxs-lookup"><span data-stu-id="4af81-257">**Default**: `false`</span></span>  
<span data-ttu-id="4af81-258">**Variabile di ambiente**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="4af81-258">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="4af81-259">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4af81-259">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="4af81-260">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="4af81-260">HostingStartupAssemblies</span></span>

<span data-ttu-id="4af81-261">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da caricare all'avvio.</span><span class="sxs-lookup"><span data-stu-id="4af81-261">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="4af81-262">Sebbene il valore di configurazione predefinito sia una stringa vuota, gli assembly di avvio dell'hosting includono sempre l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-262">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="4af81-263">Quando vengono specificati, gli assembly di avvio dell'hosting vengono aggiunti all'assembly dell'app per essere caricati quando l'app compila i servizi comuni durante l'avvio.</span><span class="sxs-lookup"><span data-stu-id="4af81-263">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="4af81-264">**Tasto**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4af81-264">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="4af81-265">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-265">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-266">**Predefinito**: Stringa vuota</span><span class="sxs-lookup"><span data-stu-id="4af81-266">**Default**: Empty string</span></span>  
<span data-ttu-id="4af81-267">**Variabile di ambiente**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4af81-267">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="4af81-268">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4af81-268">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="4af81-269">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="4af81-269">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="4af81-270">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da escludere all'avvio.</span><span class="sxs-lookup"><span data-stu-id="4af81-270">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="4af81-271">**Tasto**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4af81-271">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="4af81-272">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-272">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-273">**Predefinito**: Stringa vuota</span><span class="sxs-lookup"><span data-stu-id="4af81-273">**Default**: Empty string</span></span>  
<span data-ttu-id="4af81-274">**Variabile di ambiente**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4af81-274">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="4af81-275">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4af81-275">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="4af81-276">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="4af81-276">HTTPS_Port</span></span>

<span data-ttu-id="4af81-277">Porta di reindirizzamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="4af81-277">The HTTPS redirect port.</span></span> <span data-ttu-id="4af81-278">Usata per [imporre HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="4af81-278">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="4af81-279">**Tasto**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="4af81-279">**Key**: `https_port`</span></span>  
<span data-ttu-id="4af81-280">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-280">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-281">**Predefinito:** non viene impostato un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="4af81-281">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="4af81-282">**Variabile di ambiente**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="4af81-282">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="4af81-283">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4af81-283">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="4af81-284">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="4af81-284">PreferHostingUrls</span></span>

<span data-ttu-id="4af81-285">Indica se l'host deve essere in ascolto sugli URL configurati con gli `IWebHostBuilder` URL anziché con quelli configurati con l'implementazione. `IServer`</span><span class="sxs-lookup"><span data-stu-id="4af81-285">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="4af81-286">**Tasto**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="4af81-286">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="4af81-287">**Tipo** `bool` :`true` `1`( o )</span><span class="sxs-lookup"><span data-stu-id="4af81-287">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4af81-288">**Predefinito**:`true`</span><span class="sxs-lookup"><span data-stu-id="4af81-288">**Default**: `true`</span></span>  
<span data-ttu-id="4af81-289">**Variabile di ambiente**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="4af81-289">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="4af81-290">Per impostare questo valore, usare la variabile di ambiente o chiamare `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="4af81-290">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="4af81-291">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="4af81-291">PreventHostingStartup</span></span>

<span data-ttu-id="4af81-292">Impedisce il caricamento automatico degli assembly di avvio dell'hosting, inclusi gli assembly di avvio dell'hosting configurati dall'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-292">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="4af81-293">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4af81-293">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="4af81-294">**Tasto**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="4af81-294">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="4af81-295">**Tipo** `bool` :`true` `1`( o )</span><span class="sxs-lookup"><span data-stu-id="4af81-295">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4af81-296">**Predefinito**:`false`</span><span class="sxs-lookup"><span data-stu-id="4af81-296">**Default**: `false`</span></span>  
<span data-ttu-id="4af81-297">**Variabile di ambiente**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="4af81-297">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="4af81-298">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4af81-298">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="4af81-299">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="4af81-299">StartupAssembly</span></span>

<span data-ttu-id="4af81-300">L'assembly per la ricerca della classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="4af81-300">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="4af81-301">**Tasto**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="4af81-301">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="4af81-302">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-302">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-303">**Impostazione predefinita**: assembly dell'app</span><span class="sxs-lookup"><span data-stu-id="4af81-303">**Default**: The app's assembly</span></span>  
<span data-ttu-id="4af81-304">**Variabile di ambiente**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="4af81-304">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="4af81-305">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="4af81-305">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="4af81-306">`UseStartup` può richiedere un nome di assembly (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="4af81-306">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="4af81-307">Se vengono chiamati più metodi `UseStartup`, l'ultimo metodo ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="4af81-307">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="4af81-308">URL</span><span class="sxs-lookup"><span data-stu-id="4af81-308">URLs</span></span>

<span data-ttu-id="4af81-309">Elenco delimitato da punto e virgola degli indirizzi IP o gli indirizzi host con le porte e protocolli su cui il server deve eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="4af81-309">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="4af81-310">Ad esempio: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="4af81-310">For example, `http://localhost:123`.</span></span> <span data-ttu-id="4af81-311">Usare "\*" per indicare che il server deve eseguire l'ascolto per le richieste su tutti gli indirizzi IP o nomi host usando la porta e il protocollo specificati (ad esempio, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="4af81-311">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="4af81-312">Il protocollo (`http://` o `https://`) deve essere incluso con ogni URL.</span><span class="sxs-lookup"><span data-stu-id="4af81-312">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="4af81-313">I formati supportati variano a seconda del server.</span><span class="sxs-lookup"><span data-stu-id="4af81-313">Supported formats vary among servers.</span></span>

<span data-ttu-id="4af81-314">**Tasto**:`urls`</span><span class="sxs-lookup"><span data-stu-id="4af81-314">**Key**: `urls`</span></span>  
<span data-ttu-id="4af81-315">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-315">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-316">**Predefinito** `http://localhost:5000` : e`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="4af81-316">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="4af81-317">**Variabile di ambiente**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="4af81-317">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="4af81-318">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="4af81-318">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="4af81-319">Kestrel ha una propria API di configurazione degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="4af81-319">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="4af81-320">Per altre informazioni, vedere <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4af81-320">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="4af81-321">WebRoot</span><span class="sxs-lookup"><span data-stu-id="4af81-321">WebRoot</span></span>

<span data-ttu-id="4af81-322">La proprietà [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina il percorso relativo degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-322">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="4af81-323">Se il percorso non esiste, viene usato un provider di file no-op.</span><span class="sxs-lookup"><span data-stu-id="4af81-323">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="4af81-324">**Tasto**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="4af81-324">**Key**: `webroot`</span></span>  
<span data-ttu-id="4af81-325">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-325">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-326">**Default**: Il `wwwroot`valore predefinito è .</span><span class="sxs-lookup"><span data-stu-id="4af81-326">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="4af81-327">Il percorso della *radice del contenuto/wwwroot* deve esistere.</span><span class="sxs-lookup"><span data-stu-id="4af81-327">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="4af81-328">**Variabile di ambiente**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="4af81-328">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="4af81-329">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseWebRoot` su `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4af81-329">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="4af81-330">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="4af81-330">For more information, see:</span></span>

* [<span data-ttu-id="4af81-331">Nozioni fondamentali: radice Web</span><span class="sxs-lookup"><span data-stu-id="4af81-331">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="4af81-332">Radice contenuto</span><span class="sxs-lookup"><span data-stu-id="4af81-332">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="4af81-333">Gestire la durata dell'host</span><span class="sxs-lookup"><span data-stu-id="4af81-333">Manage the host lifetime</span></span>

<span data-ttu-id="4af81-334">Chiamare metodi sull'implementazione <xref:Microsoft.Extensions.Hosting.IHost> incorporata per avviare e arrestare l'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-334">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="4af81-335">Questi metodi influiscono su tutte le implementazioni <xref:Microsoft.Extensions.Hosting.IHostedService> che vengono registrate nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="4af81-335">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="4af81-336">Esegui</span><span class="sxs-lookup"><span data-stu-id="4af81-336">Run</span></span>

<span data-ttu-id="4af81-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> esegue l'app e blocca il thread di chiamata fino all'arresto dell'host.</span><span class="sxs-lookup"><span data-stu-id="4af81-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="4af81-338">RunAsync</span><span class="sxs-lookup"><span data-stu-id="4af81-338">RunAsync</span></span>

<span data-ttu-id="4af81-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> esegue l'app e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="4af81-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="4af81-340">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="4af81-340">RunConsoleAsync</span></span>

<span data-ttu-id="4af81-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>abilita il supporto della console, compila e avvia l'host e attende l'arresto di <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="4af81-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="4af81-342">Inizia</span><span class="sxs-lookup"><span data-stu-id="4af81-342">Start</span></span>

<span data-ttu-id="4af81-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> avvia l'host in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="4af81-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="4af81-344">StartAsync</span><span class="sxs-lookup"><span data-stu-id="4af81-344">StartAsync</span></span>

<span data-ttu-id="4af81-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> avvia l'host e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="4af81-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="4af81-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> viene chiamato all'avvio di `StartAsync`, che attende il completamento prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="4af81-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="4af81-347">Questo è utile per ritardare l'avvio fino al segnale trasmesso da un evento esterno.</span><span class="sxs-lookup"><span data-stu-id="4af81-347">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="4af81-348">StopAsync</span><span class="sxs-lookup"><span data-stu-id="4af81-348">StopAsync</span></span>

<span data-ttu-id="4af81-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> prova ad arrestare l'host entro il timeout specificato.</span><span class="sxs-lookup"><span data-stu-id="4af81-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="4af81-350">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="4af81-350">WaitForShutdown</span></span>

<span data-ttu-id="4af81-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blocca il thread chiamante fino all'arresto non viene attivato da IHostLifetime, ad esempio tramite <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="4af81-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="4af81-352">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="4af81-352">WaitForShutdownAsync</span></span>

<span data-ttu-id="4af81-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato l'arresto tramite il token specificato, quindi chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4af81-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="4af81-354">Controllo esterno</span><span class="sxs-lookup"><span data-stu-id="4af81-354">External control</span></span>

<span data-ttu-id="4af81-355">Il controllo diretto della durata dell'host può essere ottenuto usando metodi che possono essere chiamati dall'esterno:</span><span class="sxs-lookup"><span data-stu-id="4af81-355">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="4af81-356">Le app ASP.NET Core configurano e avviano un host.</span><span class="sxs-lookup"><span data-stu-id="4af81-356">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="4af81-357">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="4af81-357">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="4af81-358">Questo articolo illustra l'host generico di ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), che si usa per le app che non elaborano le richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="4af81-358">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="4af81-359">Lo scopo dell'host generico è separare la pipeline HTTP dall'API dell'host Web, per abilitare una gamma più ampia di scenari host.</span><span class="sxs-lookup"><span data-stu-id="4af81-359">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="4af81-360">La messaggistica, le attività in background e altri carichi di lavoro non HTTP basati sull'host generico traggono vantaggio dalle funzionalità a montaggio incrociato come la configurazione, l'inserimento di dipendenze (DI) e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="4af81-360">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="4af81-361">L'host generico è una nuova funzionalità introdotta in ASP.NET Core 2.1 e non è adatto agli scenari di hosting Web.</span><span class="sxs-lookup"><span data-stu-id="4af81-361">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="4af81-362">Per gli scenari di hosting Web usare l'[host Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="4af81-362">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="4af81-363">L'host generico sostituirà l'host Web in una versione futura, funzionando come API host principale negli scenari sia HTTP che non HTTP.</span><span class="sxs-lookup"><span data-stu-id="4af81-363">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="4af81-364">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4af81-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4af81-365">Quando si esegue l'app di esempio in [Visual Studio Code](https://code.visualstudio.com/), usare un *terminale esterno o integrato*.</span><span class="sxs-lookup"><span data-stu-id="4af81-365">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="4af81-366">Non eseguire l'esempio in un ambiente `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="4af81-366">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="4af81-367">Per impostare la console in Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="4af81-367">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="4af81-368">Aprire il file *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="4af81-368">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="4af81-369">Nella configurazione **.NET Core Launch (console)** trovare la voce **console**.</span><span class="sxs-lookup"><span data-stu-id="4af81-369">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="4af81-370">Impostare il valore su `externalTerminal` o `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="4af81-370">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="4af81-371">Introduzione</span><span class="sxs-lookup"><span data-stu-id="4af81-371">Introduction</span></span>

<span data-ttu-id="4af81-372">La libreria host generico è disponibile nello spazio dei nomi <xref:Microsoft.Extensions.Hosting> e viene specificata dal pacchetto [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="4af81-372">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="4af81-373">Il pacchetto `Microsoft.Extensions.Hosting` è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 o versioni successive).</span><span class="sxs-lookup"><span data-stu-id="4af81-373">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="4af81-374"><xref:Microsoft.Extensions.Hosting.IHostedService> è il punto di ingresso per l'esecuzione del codice.</span><span class="sxs-lookup"><span data-stu-id="4af81-374"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="4af81-375">Ogni implementazione <xref:Microsoft.Extensions.Hosting.IHostedService> viene eseguita nell'ordine di [registrazione del servizio in ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="4af81-375">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="4af81-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> viene chiamato su ogni <xref:Microsoft.Extensions.Hosting.IHostedService> quando viene avviato l'host e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> viene chiamato in ordine di registrazione inverso quando l'host viene chiuso normalmente.</span><span class="sxs-lookup"><span data-stu-id="4af81-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="4af81-377">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="4af81-377">Set up a host</span></span>

<span data-ttu-id="4af81-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder> è il componente principale che le librerie e le app usano per inizializzare, compilare ed eseguire l'host:</span><span class="sxs-lookup"><span data-stu-id="4af81-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="4af81-379">Opzioni</span><span class="sxs-lookup"><span data-stu-id="4af81-379">Options</span></span>

<span data-ttu-id="4af81-380"><xref:Microsoft.Extensions.Hosting.HostOptions> configura le opzioni per <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="4af81-380"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="4af81-381">Timeout di arresto</span><span class="sxs-lookup"><span data-stu-id="4af81-381">Shutdown timeout</span></span>

<span data-ttu-id="4af81-382"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> imposta il timeout per <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4af81-382"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="4af81-383">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="4af81-383">The default value is five seconds.</span></span>

<span data-ttu-id="4af81-384">La seguente configurazione `Program.Main` di opzione in aumenta il timeout di arresto predefinito di cinque secondi a 20 secondi:</span><span class="sxs-lookup"><span data-stu-id="4af81-384">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="4af81-385">Servizi predefiniti</span><span class="sxs-lookup"><span data-stu-id="4af81-385">Default services</span></span>

<span data-ttu-id="4af81-386">I servizi seguenti vengono registrati durante l'inizializzazione dell'host:</span><span class="sxs-lookup"><span data-stu-id="4af81-386">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="4af81-387">[Ambiente](xref:fundamentals/environments) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>( )</span><span class="sxs-lookup"><span data-stu-id="4af81-387">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="4af81-388">[Configurazione](xref:fundamentals/configuration/index) <xref:Microsoft.Extensions.Configuration.IConfiguration>( )</span><span class="sxs-lookup"><span data-stu-id="4af81-388">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="4af81-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="4af81-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="4af81-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="4af81-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="4af81-391">[Opzioni](xref:fundamentals/configuration/options) <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>( )</span><span class="sxs-lookup"><span data-stu-id="4af81-391">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="4af81-392">[Registrazione](xref:fundamentals/logging/index) <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>( )</span><span class="sxs-lookup"><span data-stu-id="4af81-392">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="4af81-393">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="4af81-393">Host configuration</span></span>

<span data-ttu-id="4af81-394">La configurazione dell'host viene creata tramite:</span><span class="sxs-lookup"><span data-stu-id="4af81-394">Host configuration is created by:</span></span>

* <span data-ttu-id="4af81-395">Chiamata ai metodi di estensione su <xref:Microsoft.Extensions.Hosting.IHostBuilder> per impostare la [radice del contenuto](#content-root) e l'[ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="4af81-395">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="4af81-396">Lettura della configurazione dai provider di configurazione in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="4af81-396">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="4af81-397">Metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="4af81-397">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="4af81-398">Chiave applicazione (nome)</span><span class="sxs-lookup"><span data-stu-id="4af81-398">Application key (name)</span></span>

<span data-ttu-id="4af81-399">La proprietà [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) viene impostata dalla configurazione dell'host durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="4af81-399">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="4af81-400">Per impostare il valore in modo esplicito, usare [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="4af81-400">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="4af81-401">**Tasto**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="4af81-401">**Key**: `applicationName`</span></span>  
<span data-ttu-id="4af81-402">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-402">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-403">**Impostazione predefinita**: il nome dell'assembly contenente il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-403">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="4af81-404">**Impostare utilizzando**:`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="4af81-404">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="4af81-405">**Variabile**di `<PREFIX_>APPLICATIONNAME` `<PREFIX_>` ambiente : ( è [facoltativa e definita dall'utente](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="4af81-405">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="4af81-406">Radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="4af81-406">Content root</span></span>

<span data-ttu-id="4af81-407">Questa impostazione determina la posizione da cui l'host inizia la ricerca dei file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="4af81-407">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="4af81-408">**Tasto**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="4af81-408">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="4af81-409">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-409">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-410">**Impostazione predefinita**: il valore predefinito corrisponde alla cartella contenente l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-410">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="4af81-411">**Impostare utilizzando**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="4af81-411">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="4af81-412">**Variabile**di `<PREFIX_>CONTENTROOT` `<PREFIX_>` ambiente : ( è [facoltativa e definita dall'utente](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="4af81-412">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="4af81-413">Se il percorso non esiste, l'host non verrà avviato.</span><span class="sxs-lookup"><span data-stu-id="4af81-413">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="4af81-414">Per ulteriori informazioni, vedere [Nozioni fondamentali: radice del contenuto](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="4af81-414">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="4af81-415">Environment</span><span class="sxs-lookup"><span data-stu-id="4af81-415">Environment</span></span>

<span data-ttu-id="4af81-416">Imposta [l'ambiente](xref:fundamentals/environments)dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-416">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="4af81-417">**Tasto**:`environment`</span><span class="sxs-lookup"><span data-stu-id="4af81-417">**Key**: `environment`</span></span>  
<span data-ttu-id="4af81-418">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-418">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-419">**Predefinito**:`Production`</span><span class="sxs-lookup"><span data-stu-id="4af81-419">**Default**: `Production`</span></span>  
<span data-ttu-id="4af81-420">**Impostare utilizzando**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="4af81-420">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="4af81-421">**Variabile**di `<PREFIX_>ENVIRONMENT` `<PREFIX_>` ambiente : ( è [facoltativa e definita dall'utente](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="4af81-421">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="4af81-422">L'ambiente può essere impostato su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="4af81-422">The environment can be set to any value.</span></span> <span data-ttu-id="4af81-423">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="4af81-423">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="4af81-424">I valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="4af81-424">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="4af81-425">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="4af81-425">ConfigureHostConfiguration</span></span>

<span data-ttu-id="4af81-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per creare un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfiguration> per l'host.</span><span class="sxs-lookup"><span data-stu-id="4af81-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="4af81-427">La configurazione dell'host viene usata per inizializzare l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> da usare nel processo di compilazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-427">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="4af81-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="4af81-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="4af81-429">L'host usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="4af81-429">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="4af81-430">Nessun provider è incluso per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="4af81-430">No providers are included by default.</span></span> <span data-ttu-id="4af81-431">È necessario specificare in modo esplicito tutti i provider di configurazione richiesti dall'app in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, inclusi:</span><span class="sxs-lookup"><span data-stu-id="4af81-431">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="4af81-432">Configurazione dei file (ad esempio, da un file *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="4af81-432">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="4af81-433">Configurazione delle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="4af81-433">Environment variable configuration.</span></span>
* <span data-ttu-id="4af81-434">Configurazione degli argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="4af81-434">Command-line argument configuration.</span></span>
* <span data-ttu-id="4af81-435">Tutti gli altri provider di configurazione necessari.</span><span class="sxs-lookup"><span data-stu-id="4af81-435">Any other required configuration providers.</span></span>

<span data-ttu-id="4af81-436">La configurazione file dell'host viene abilitata specificando il percorso di base dell'app con `SetBasePath` seguito da una chiamata a uno dei [provider di configurazione dei file](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4af81-436">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="4af81-437">L'app di esempio usa un file JSON, *hostsettings.json*, e chiama <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> per utilizzare le impostazioni di configurazione host del file.</span><span class="sxs-lookup"><span data-stu-id="4af81-437">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="4af81-438">Per aggiungere la [configurazione delle variabili di ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) dell'host, chiamare <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sul generatore di host.</span><span class="sxs-lookup"><span data-stu-id="4af81-438">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="4af81-439">`AddEnvironmentVariables` accetta un prefisso facoltativo definito dall'utente.</span><span class="sxs-lookup"><span data-stu-id="4af81-439">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="4af81-440">L'app di esempio usa un prefisso di `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="4af81-440">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="4af81-441">Il prefisso viene rimosso quando vengono lette le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="4af81-441">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="4af81-442">Quando viene configurato l'host dell'app di esempio, il valore della variabile di ambiente per `PREFIX_ENVIRONMENT` diventa il valore di configurazione dell'host per la chiave `environment`.</span><span class="sxs-lookup"><span data-stu-id="4af81-442">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="4af81-443">Durante lo sviluppo quando si usa [Visual Studio](https://visualstudio.microsoft.com) o si esegue un'app con `dotnet run`, le variabili di ambiente possono essere impostate nel file *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4af81-443">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="4af81-444">Durante lo sviluppo in [Visual Studio Code](https://code.visualstudio.com/), le variabili di ambiente possono essere impostate nel file *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="4af81-444">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="4af81-445">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4af81-445">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4af81-446">La [configurazione della riga di comando](xref:fundamentals/configuration/index#command-line-configuration-provider) viene aggiunta chiamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="4af81-446">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="4af81-447">La configurazione della riga di comando viene aggiunta per ultima per consentire agli argomenti della riga di comando di eseguire l'override della configurazione fornita dai provider di configurazione precedenti.</span><span class="sxs-lookup"><span data-stu-id="4af81-447">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="4af81-448">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="4af81-448">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="4af81-449">È possibile fornire una configurazione aggiuntiva con le chiavi [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="4af81-449">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="4af81-450">Configurazione di esempio di `HostBuilder` che usa <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="4af81-450">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="4af81-451">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="4af81-451">ConfigureAppConfiguration</span></span>

<span data-ttu-id="4af81-452">La configurazione dell'app viene creata chiamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sull'implementazione <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="4af81-452">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="4af81-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per creare un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfiguration> per l'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="4af81-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="4af81-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="4af81-455">L'app usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="4af81-455">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="4af81-456">La configurazione creata da <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> è disponibile in [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) per le operazioni successive e in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="4af81-456">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="4af81-457">La configurazione dell'app riceve automaticamente la configurazione dell'host fornita da [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="4af81-457">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="4af81-458">Configurazione app di esempio che usa <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="4af81-458">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="4af81-459">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="4af81-459">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="4af81-460">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="4af81-460">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="4af81-461">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="4af81-461">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="4af81-462">Per spostare i file delle impostazioni nella directory di output, specificare i file delle impostazioni come [elementi di progetto MSBuild](/visualstudio/msbuild/common-msbuild-project-items) nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="4af81-462">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="4af81-463">L'app di esempio sposta i file delle impostazioni dell'app JSON e *hostsettings.json* con l'elemento `<Content>` seguente:</span><span class="sxs-lookup"><span data-stu-id="4af81-463">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="4af81-464">I metodi di estensione di configurazione, ad esempio <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> e <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, necessitano di altri pacchetti NuGet, ad esempio [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) e [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="4af81-464">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="4af81-465">A meno che l'app non usi il [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), questi pacchetti devono essere aggiunti al progetto oltre al pacchetto [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) di base.</span><span class="sxs-lookup"><span data-stu-id="4af81-465">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="4af81-466">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="4af81-466">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="4af81-467">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="4af81-467">ConfigureServices</span></span>

<span data-ttu-id="4af81-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> aggiunge servizi al contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4af81-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="4af81-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="4af81-470">Un servizio ospitato è una classe con logica di attività in background che implementa l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="4af81-470">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="4af81-471">Per altre informazioni, vedere <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="4af81-471">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="4af81-472">L'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa il metodo di estensione `AddHostedService` per aggiungere all'app un servizio per gli eventi di durata (`LifetimeEventsHostedService`) e un'attività in background con timeout (`TimedHostedService`):</span><span class="sxs-lookup"><span data-stu-id="4af81-472">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="4af81-473">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="4af81-473">ConfigureLogging</span></span>

<span data-ttu-id="4af81-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> aggiunge un delegato per configurare l'interfaccia <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornita.</span><span class="sxs-lookup"><span data-stu-id="4af81-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="4af81-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="4af81-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="4af81-476">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="4af81-476">UseConsoleLifetime</span></span>

<span data-ttu-id="4af81-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>per <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM e chiama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> per avviare il processo di arresto.</span><span class="sxs-lookup"><span data-stu-id="4af81-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="4af81-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>sblocca estensioni come [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="4af81-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="4af81-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` è preregistrata come implementazione di durata predefinita.</span><span class="sxs-lookup"><span data-stu-id="4af81-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="4af81-480">Viene usata l'ultima durata registrata.</span><span class="sxs-lookup"><span data-stu-id="4af81-480">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="4af81-481">Configurazione contenitore</span><span class="sxs-lookup"><span data-stu-id="4af81-481">Container configuration</span></span>

<span data-ttu-id="4af81-482">Per supportare l'inserimento di altri contenitori, l'host può accettare un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="4af81-482">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="4af81-483">La disponibilità di una factory non fa parte della registrazione del contenitore DI, ma è una funzionalità intrinseca dell'host usata per creare il contenitore DI concreto.</span><span class="sxs-lookup"><span data-stu-id="4af81-483">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="4af81-484">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) esegue l'override della factory predefinita usata per la creazione del provider di servizi dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-484">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="4af81-485">La configurazione del contenitore personalizzato è gestita dal metodo <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="4af81-485">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="4af81-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> offre un'esperienza fortemente tipizzata per la configurazione del contenitore sulla base dell'API host sottostante.</span><span class="sxs-lookup"><span data-stu-id="4af81-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="4af81-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="4af81-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="4af81-488">Creare un contenitore di servizi per l'app:</span><span class="sxs-lookup"><span data-stu-id="4af81-488">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="4af81-489">Specificare una factory per il contenitore di servizi:</span><span class="sxs-lookup"><span data-stu-id="4af81-489">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="4af81-490">Usare la factory e configurare il contenitore di servizi personalizzato per l'app:</span><span class="sxs-lookup"><span data-stu-id="4af81-490">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="4af81-491">Estendibilità</span><span class="sxs-lookup"><span data-stu-id="4af81-491">Extensibility</span></span>

<span data-ttu-id="4af81-492">L'estensibilità host viene eseguita con i metodi di estensione su <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="4af81-492">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="4af81-493">L'esempio seguente illustra come un metodo di estensione estende un'implementazione <xref:Microsoft.Extensions.Hosting.IHostBuilder> con l'esempio [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) dimostrato in <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="4af81-493">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="4af81-494">Un'app stabilisce il metodo di estensione `UseHostedService` per registrare il servizio ospitato passato in `T`:</span><span class="sxs-lookup"><span data-stu-id="4af81-494">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="4af81-495">Gestire l'host</span><span class="sxs-lookup"><span data-stu-id="4af81-495">Manage the host</span></span>

<span data-ttu-id="4af81-496">L'implementazione <xref:Microsoft.Extensions.Hosting.IHost> è responsabile dell'avvio e arresto delle implementazioni <xref:Microsoft.Extensions.Hosting.IHostedService> che sono registrate nel contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="4af81-496">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="4af81-497">Esegui</span><span class="sxs-lookup"><span data-stu-id="4af81-497">Run</span></span>

<span data-ttu-id="4af81-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> esegue l'app e blocca il thread di chiamata fino all'arresto dell'host:</span><span class="sxs-lookup"><span data-stu-id="4af81-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="4af81-499">RunAsync</span><span class="sxs-lookup"><span data-stu-id="4af81-499">RunAsync</span></span>

<span data-ttu-id="4af81-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> esegue l'app e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto:</span><span class="sxs-lookup"><span data-stu-id="4af81-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="4af81-501">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="4af81-501">RunConsoleAsync</span></span>

<span data-ttu-id="4af81-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>abilita il supporto della console, compila e avvia l'host e attende l'arresto di <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="4af81-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="4af81-503">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="4af81-503">Start and StopAsync</span></span>

<span data-ttu-id="4af81-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> avvia l'host in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="4af81-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="4af81-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> prova ad arrestare l'host entro il timeout specificato.</span><span class="sxs-lookup"><span data-stu-id="4af81-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="4af81-506">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="4af81-506">StartAsync and StopAsync</span></span>

<span data-ttu-id="4af81-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> avvia l'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="4af81-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> arresta l'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="4af81-509">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="4af81-509">WaitForShutdown</span></span>

<span data-ttu-id="4af81-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>viene <xref:Microsoft.Extensions.Hosting.IHostLifetime>attivato tramite , `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` ad esempio (ascolta <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="4af81-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="4af81-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4af81-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="4af81-512">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="4af81-512">WaitForShutdownAsync</span></span>

<span data-ttu-id="4af81-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato l'arresto tramite il token specificato, quindi chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4af81-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="4af81-514">Controllo esterno</span><span class="sxs-lookup"><span data-stu-id="4af81-514">External control</span></span>

<span data-ttu-id="4af81-515">Il controllo esterno dell'host può essere ottenuto usando metodi che possono essere chiamati dall'esterno:</span><span class="sxs-lookup"><span data-stu-id="4af81-515">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="4af81-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> viene chiamato all'avvio di <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, che attende il completamento prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="4af81-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="4af81-517">Questo è utile per ritardare l'avvio fino al segnale trasmesso da un evento esterno.</span><span class="sxs-lookup"><span data-stu-id="4af81-517">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="4af81-518">Interfaccia IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="4af81-518">IHostingEnvironment interface</span></span>

<span data-ttu-id="4af81-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> include informazioni sull'ambiente di hosting dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="4af81-520">Usare l'[inserimento di un costruttore](xref:fundamentals/dependency-injection) per ottenere <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> per poterne usare le proprietà e i metodi di estensione:</span><span class="sxs-lookup"><span data-stu-id="4af81-520">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="4af81-521">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4af81-521">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="4af81-522">Interfaccia IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4af81-522">IApplicationLifetime interface</span></span>

<span data-ttu-id="4af81-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> consente attività post-avvio e di arresto, incluse le richieste di arresto normale.</span><span class="sxs-lookup"><span data-stu-id="4af81-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="4af81-524">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi <xref:System.Action> che definiscono gli eventi di avvio e arresto.</span><span class="sxs-lookup"><span data-stu-id="4af81-524">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="4af81-525">Token di annullamento</span><span class="sxs-lookup"><span data-stu-id="4af81-525">Cancellation Token</span></span> | <span data-ttu-id="4af81-526">Attivato quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="4af81-526">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="4af81-527">L'host è stato completamente avviato.</span><span class="sxs-lookup"><span data-stu-id="4af81-527">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="4af81-528">L'host sta completando un arresto normale.</span><span class="sxs-lookup"><span data-stu-id="4af81-528">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="4af81-529">Tutte le richieste devono essere elaborate.</span><span class="sxs-lookup"><span data-stu-id="4af81-529">All requests should be processed.</span></span> <span data-ttu-id="4af81-530">L'arresto è bloccato fino al completamento di questo evento.</span><span class="sxs-lookup"><span data-stu-id="4af81-530">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="4af81-531">L'host sta eseguendo un arresto normale.</span><span class="sxs-lookup"><span data-stu-id="4af81-531">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="4af81-532">È possibile che le richieste siano ancora in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="4af81-532">Requests may still be processing.</span></span> <span data-ttu-id="4af81-533">L'arresto è bloccato fino al completamento di questo evento.</span><span class="sxs-lookup"><span data-stu-id="4af81-533">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="4af81-534">Inserire tramite costruttore il servizio <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> in qualsiasi classe.</span><span class="sxs-lookup"><span data-stu-id="4af81-534">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="4af81-535">L'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa l'inserimento di costruttori in una classe `LifetimeEventsHostedService` (implementazione <xref:Microsoft.Extensions.Hosting.IHostedService>) per registrare gli eventi.</span><span class="sxs-lookup"><span data-stu-id="4af81-535">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="4af81-536">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="4af81-536">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="4af81-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> richiede la chiusura dell'applicazione corrente.</span><span class="sxs-lookup"><span data-stu-id="4af81-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="4af81-538">La classe seguente usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> per arrestare normalmente un'app quando viene chiamato il metodo `Shutdown` della classe:</span><span class="sxs-lookup"><span data-stu-id="4af81-538">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

<span data-ttu-id="4af81-539">I modelli ASP.NET Core creano un<xref:Microsoft.Extensions.Hosting.HostBuilder>host generico .NET Core ( ).</span><span class="sxs-lookup"><span data-stu-id="4af81-539">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="4af81-540">Definizione host</span><span class="sxs-lookup"><span data-stu-id="4af81-540">Host definition</span></span>

<span data-ttu-id="4af81-541">Un *host* è un oggetto che incapsula le risorse di un'app, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="4af81-541">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="4af81-542">Inserimento di dipendenze (DI)</span><span class="sxs-lookup"><span data-stu-id="4af81-542">Dependency injection (DI)</span></span>
* <span data-ttu-id="4af81-543">Registrazione</span><span class="sxs-lookup"><span data-stu-id="4af81-543">Logging</span></span>
* <span data-ttu-id="4af81-544">Configurazione</span><span class="sxs-lookup"><span data-stu-id="4af81-544">Configuration</span></span>
* <span data-ttu-id="4af81-545">`IHostedService` Implementazioni</span><span class="sxs-lookup"><span data-stu-id="4af81-545">`IHostedService` implementations</span></span>

<span data-ttu-id="4af81-546">Quando un host viene avviato, chiama `IHostedService.StartAsync` in ogni implementazione di <xref:Microsoft.Extensions.Hosting.IHostedService> che trova nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="4af81-546">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="4af81-547">In un'app Web, una delle implementazioni di `IHostedService` è un servizio web che avvia un'[implementazione del server HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="4af81-547">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="4af81-548">Il motivo principale per cui tutte le risorse interdipendenti dell'app sono incluse in un unico oggetto è la gestione del ciclo di vita, vale a dire il controllo sull'avvio dell'app e sull'arresto normale.</span><span class="sxs-lookup"><span data-stu-id="4af81-548">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="4af81-549">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="4af81-549">Set up a host</span></span>

<span data-ttu-id="4af81-550">L'host è in genere configurato, compilato ed eseguito da codice nella classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="4af81-550">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="4af81-551">Il metodo `Main`:</span><span class="sxs-lookup"><span data-stu-id="4af81-551">The `Main` method:</span></span>

* <span data-ttu-id="4af81-552">Chiama un metodo `CreateHostBuilder` per creare e configurare un oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="4af81-552">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="4af81-553">Chiamate `Build` e metodi `Run` nell'oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="4af81-553">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="4af81-554">I modelli Web ASP.NET Core generano il codice seguente per creare un host:The ASP.NET Core web templates generate the following code to create a host:</span><span class="sxs-lookup"><span data-stu-id="4af81-554">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="4af81-555">Il codice seguente crea un carico `IHostedService` di lavoro non HTTP con un'implementazione aggiunta al contenitore DI.</span><span class="sxs-lookup"><span data-stu-id="4af81-555">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="4af81-556">Per un carico di lavoro HTTP, il metodo `Main` è lo stesso ma `CreateHostBuilder` chiama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="4af81-556">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="4af81-557">Se l'app usa Entity Framework Core, non modificare il nome o la firma del metodo `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4af81-557">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="4af81-558">Gli [strumenti di Entity Framework Core](/ef/core/miscellaneous/cli/) si aspettano di trovare un metodo `CreateHostBuilder` che configura l'host senza eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-558">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="4af81-559">Per altre informazioni, vedere [Creazione DbContext in fase di progettazione](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="4af81-559">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="4af81-560">Impostazioni predefinite del generatore</span><span class="sxs-lookup"><span data-stu-id="4af81-560">Default builder settings</span></span>

<span data-ttu-id="4af81-561">Il metodo <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="4af81-561">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="4af81-562">Imposta la radice del [contenuto](xref:fundamentals/index#content-root) <xref:System.IO.Directory.GetCurrentDirectory*>sul percorso restituito da .</span><span class="sxs-lookup"><span data-stu-id="4af81-562">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="4af81-563">Carica la configurazione dell'host da:</span><span class="sxs-lookup"><span data-stu-id="4af81-563">Loads host configuration from:</span></span>
  * <span data-ttu-id="4af81-564">Variabili di ambiente `DOTNET_`precedute da .</span><span class="sxs-lookup"><span data-stu-id="4af81-564">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="4af81-565">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="4af81-565">Command-line arguments.</span></span>
* <span data-ttu-id="4af81-566">Carica la configurazione dell'app da:</span><span class="sxs-lookup"><span data-stu-id="4af81-566">Loads app configuration from:</span></span>
  * <span data-ttu-id="4af81-567">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4af81-567">*appsettings.json*.</span></span>
  * <span data-ttu-id="4af81-568">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="4af81-568">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="4af81-569">[Segreti del manager](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="4af81-569">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="4af81-570">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="4af81-570">Environment variables.</span></span>
  * <span data-ttu-id="4af81-571">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="4af81-571">Command-line arguments.</span></span>
* <span data-ttu-id="4af81-572">Aggiunge i provider di [log](xref:fundamentals/logging/index) seguenti:</span><span class="sxs-lookup"><span data-stu-id="4af81-572">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="4af81-573">Console</span><span class="sxs-lookup"><span data-stu-id="4af81-573">Console</span></span>
  * <span data-ttu-id="4af81-574">Debug</span><span class="sxs-lookup"><span data-stu-id="4af81-574">Debug</span></span>
  * <span data-ttu-id="4af81-575">EventSource</span><span class="sxs-lookup"><span data-stu-id="4af81-575">EventSource</span></span>
  * <span data-ttu-id="4af81-576">EventLog (solo quando è in esecuzione su Windows)</span><span class="sxs-lookup"><span data-stu-id="4af81-576">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="4af81-577">Abilita la [convalida dell'ambito](xref:fundamentals/dependency-injection#scope-validation) e la [convalida delle dipendenze](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando l'ambiente è lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="4af81-577">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="4af81-578">Il metodo `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="4af81-578">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="4af81-579">Carica la configurazione host dalle `ASPNETCORE_`variabili di ambiente precedute da .</span><span class="sxs-lookup"><span data-stu-id="4af81-579">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="4af81-580">Imposta il server [Kestrel](xref:fundamentals/servers/kestrel) come server Web e lo configura usando i provider di configurazione dell'host dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-580">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="4af81-581">Per le opzioni predefinite del server Kestrel, vedere <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="4af81-581">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="4af81-582">Aggiunge [il middleware dell'applicazione di filtri dell'host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="4af81-582">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="4af81-583">Aggiunge il [middleware Intestazioni inoltrate](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è uguale `true`a .</span><span class="sxs-lookup"><span data-stu-id="4af81-583">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="4af81-584">Abilita l'integrazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="4af81-584">Enables IIS integration.</span></span> <span data-ttu-id="4af81-585">Per le opzioni predefinite di IIS, vedere <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="4af81-585">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="4af81-586">Le sezioni [Impostazioni per tutti i tipi di app](#settings-for-all-app-types) e [Impostazioni per le app Web](#settings-for-web-apps) più avanti in questo articolo mostrano come eseguire l'override delle impostazioni predefinite del generatore.</span><span class="sxs-lookup"><span data-stu-id="4af81-586">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="4af81-587">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="4af81-587">Framework-provided services</span></span>

<span data-ttu-id="4af81-588">I seguenti servizi vengono registrati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="4af81-588">The following services are registered automatically:</span></span>

* [<span data-ttu-id="4af81-589">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4af81-589">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="4af81-590">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4af81-590">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="4af81-591">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4af81-591">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="4af81-592">Per ulteriori informazioni sui servizi <xref:fundamentals/dependency-injection#framework-provided-services>forniti dal framework, vedere .</span><span class="sxs-lookup"><span data-stu-id="4af81-592">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="4af81-593">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4af81-593">IHostApplicationLifetime</span></span>

<span data-ttu-id="4af81-594">Inserire il servizio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (in precedenza `IApplicationLifetime`) in qualsiasi classe per gestire le attività post-avvio e di arresto normale.</span><span class="sxs-lookup"><span data-stu-id="4af81-594">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="4af81-595">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi del gestore dell'evento di avvio e di arresto.</span><span class="sxs-lookup"><span data-stu-id="4af81-595">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="4af81-596">L'interfaccia include anche un metodo `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="4af81-596">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="4af81-597">L'esempio seguente `IHostedService` è un'implementazione che registra gli eventi:The following example is an implementation that registers `IHostApplicationLifetime` events:</span><span class="sxs-lookup"><span data-stu-id="4af81-597">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="4af81-598">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4af81-598">IHostLifetime</span></span>

<span data-ttu-id="4af81-599">L'implementazione <xref:Microsoft.Extensions.Hosting.IHostLifetime> controlla quando l'host viene avviato e quando si arresta.</span><span class="sxs-lookup"><span data-stu-id="4af81-599">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="4af81-600">Viene usata l'ultima implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="4af81-600">The last implementation registered is used.</span></span>

<span data-ttu-id="4af81-601">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` è l'implementazione `IHostLifetime` predefinita.</span><span class="sxs-lookup"><span data-stu-id="4af81-601">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="4af81-602">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="4af81-602">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="4af81-603">Si ascolta <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> SIGTERM e chiama per avviare il processo di arresto.</span><span class="sxs-lookup"><span data-stu-id="4af81-603">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="4af81-604">Sblocca estensioni come [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="4af81-604">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="4af81-605">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4af81-605">IHostEnvironment</span></span>

<span data-ttu-id="4af81-606">Inserire <xref:Microsoft.Extensions.Hosting.IHostEnvironment> il servizio in una classe per ottenere informazioni sulle impostazioni seguenti:Inject the service into a class to get information about the following settings:</span><span class="sxs-lookup"><span data-stu-id="4af81-606">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="4af81-607">Applicationname</span><span class="sxs-lookup"><span data-stu-id="4af81-607">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="4af81-608">NomeAmbiente</span><span class="sxs-lookup"><span data-stu-id="4af81-608">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="4af81-609">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="4af81-609">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="4af81-610">Le app `IWebHostEnvironment` Web implementano `IHostEnvironment` l'interfaccia , che eredita e aggiunge [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="4af81-610">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="4af81-611">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="4af81-611">Host configuration</span></span>

<span data-ttu-id="4af81-612">La configurazione dell'host viene usata per le proprietà dell'implementazione <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="4af81-612">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="4af81-613">La configurazione dell'host è disponibile da [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) all'interno di <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="4af81-613">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="4af81-614">Dopo `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` viene sostituito con la configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-614">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="4af81-615">Per aggiungere la configurazione dell'host, chiamare <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4af81-615">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="4af81-616">`ConfigureHostConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="4af81-616">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4af81-617">L'host usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="4af81-617">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="4af81-618">Il provider della `DOTNET_` variabile di ambiente con `CreateDefaultBuilder`prefisso e argomenti della riga di comando sono inclusi da .</span><span class="sxs-lookup"><span data-stu-id="4af81-618">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4af81-619">Per le app Web, viene aggiunto il provider di variabili di ambiente con prefisso `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="4af81-619">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="4af81-620">Il prefisso viene rimosso quando vengono lette le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="4af81-620">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="4af81-621">Ad esempio, il valore della variabile di ambiente per `ASPNETCORE_ENVIRONMENT` diventa il valore di configurazione dell'host per la chiave `environment`.</span><span class="sxs-lookup"><span data-stu-id="4af81-621">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="4af81-622">L'esempio seguente crea la configurazione host:</span><span class="sxs-lookup"><span data-stu-id="4af81-622">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="4af81-623">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="4af81-623">App configuration</span></span>

<span data-ttu-id="4af81-624">La configurazione dell'app viene creata chiamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4af81-624">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="4af81-625">`ConfigureAppConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="4af81-625">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4af81-626">L'app usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="4af81-626">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="4af81-627">La configurazione creata da `ConfigureAppConfiguration` è disponibile in [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) per le operazioni successive e come servizio da DI.</span><span class="sxs-lookup"><span data-stu-id="4af81-627">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="4af81-628">La configurazione dell'host viene aggiunta anche alla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-628">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="4af81-629">Per altre informazioni, vedere [Configurazione in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="4af81-629">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="4af81-630">Impostazioni per tutti i tipi di app</span><span class="sxs-lookup"><span data-stu-id="4af81-630">Settings for all app types</span></span>

<span data-ttu-id="4af81-631">Questa sezione elenca le impostazioni dell'host che si applicano ai carichi di lavoro HTTP e non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="4af81-631">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="4af81-632">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="4af81-632">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="4af81-633">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="4af81-633">ApplicationName</span></span>

<span data-ttu-id="4af81-634">La proprietà [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) viene impostata dalla configurazione dell'host durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="4af81-634">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="4af81-635">**Tasto**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="4af81-635">**Key**: `applicationName`</span></span>  
<span data-ttu-id="4af81-636">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-636">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-637">**Default**: Il nome dell'assembly che contiene il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-637">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="4af81-638">**Variabile di ambiente**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="4af81-638">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="4af81-639">per impostare questo valore usare la variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="4af81-639">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="4af81-640">Radice contenuto</span><span class="sxs-lookup"><span data-stu-id="4af81-640">ContentRoot</span></span>

<span data-ttu-id="4af81-641">La proprietà [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina il punto in cui l'host inizia a cercare i file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="4af81-641">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="4af81-642">Se il percorso non esiste, l'host non verrà avviato.</span><span class="sxs-lookup"><span data-stu-id="4af81-642">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="4af81-643">**Tasto**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="4af81-643">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="4af81-644">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-644">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-645">**Predefinito:** la cartella in cui risiede l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-645">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="4af81-646">**Variabile di ambiente**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="4af81-646">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="4af81-647">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseContentRoot` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4af81-647">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="4af81-648">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="4af81-648">For more information, see:</span></span>

* [<span data-ttu-id="4af81-649">Nozioni fondamentali: radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="4af81-649">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="4af81-650">WebRoot</span><span class="sxs-lookup"><span data-stu-id="4af81-650">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="4af81-651">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="4af81-651">EnvironmentName</span></span>

<span data-ttu-id="4af81-652">La proprietà [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) può essere impostata su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="4af81-652">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="4af81-653">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="4af81-653">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="4af81-654">I valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="4af81-654">Values aren't case-sensitive.</span></span>

<span data-ttu-id="4af81-655">**Tasto**:`environment`</span><span class="sxs-lookup"><span data-stu-id="4af81-655">**Key**: `environment`</span></span>  
<span data-ttu-id="4af81-656">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-656">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-657">**Predefinito**:`Production`</span><span class="sxs-lookup"><span data-stu-id="4af81-657">**Default**: `Production`</span></span>  
<span data-ttu-id="4af81-658">**Variabile di ambiente**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="4af81-658">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="4af81-659">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseEnvironment` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4af81-659">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="4af81-660">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="4af81-660">ShutdownTimeout</span></span>

<span data-ttu-id="4af81-661">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) imposta il timeout per <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4af81-661">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="4af81-662">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="4af81-662">The default value is five seconds.</span></span>  <span data-ttu-id="4af81-663">Durante il periodo di timeout, l'host:</span><span class="sxs-lookup"><span data-stu-id="4af81-663">During the timeout period, the host:</span></span>

* <span data-ttu-id="4af81-664">attiva [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="4af81-664">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="4af81-665">Tenta di arrestare i servizi ospitati, registrando gli errori per i servizi che non si interrompono.</span><span class="sxs-lookup"><span data-stu-id="4af81-665">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="4af81-666">Se il periodo di timeout scade prima che siano stati arrestati tutti i servizi ospitati, gli eventuali servizi attivi rimanenti vengono interrotti quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="4af81-666">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="4af81-667">I servizi si arrestano anche se non hanno completato l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="4af81-667">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="4af81-668">Se l'arresto dei servizi richiede più tempo, aumentare il valore di timeout.</span><span class="sxs-lookup"><span data-stu-id="4af81-668">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="4af81-669">**Tasto**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="4af81-669">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="4af81-670">**Tipo**:`int`</span><span class="sxs-lookup"><span data-stu-id="4af81-670">**Type**: `int`</span></span>  
<span data-ttu-id="4af81-671">**Predefinito**: 5 secondi</span><span class="sxs-lookup"><span data-stu-id="4af81-671">**Default**: 5 seconds</span></span>  
<span data-ttu-id="4af81-672">**Variabile di ambiente**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="4af81-672">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="4af81-673">Per impostare questo valore, usare la variabile di ambiente o configurare `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="4af81-673">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="4af81-674">Nell'esempio seguente il timeout viene impostato su 20 secondi:</span><span class="sxs-lookup"><span data-stu-id="4af81-674">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="4af81-675">Disabilitare il ricaricamento della configurazione dell'app in caso di modifica</span><span class="sxs-lookup"><span data-stu-id="4af81-675">Disable app configuration reload on change</span></span>

<span data-ttu-id="4af81-676">Per [impostazione predefinita,](xref:fundamentals/configuration/index#default) *appsettings.json* e *appsettings. Environment: .json* vengono ricaricati quando il file viene modificato.</span><span class="sxs-lookup"><span data-stu-id="4af81-676">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="4af81-677">Per disattivare questo comportamento di ricaricamento in ASP.NET Core `hostBuilder:reloadConfigOnChange` 5.0 Preview 3 o versioni successive, impostare la chiave su `false`.</span><span class="sxs-lookup"><span data-stu-id="4af81-677">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="4af81-678">**Tasto**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="4af81-678">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="4af81-679">**Tipo** `bool` :`true` `1`( o )</span><span class="sxs-lookup"><span data-stu-id="4af81-679">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4af81-680">**Predefinito**:`true`</span><span class="sxs-lookup"><span data-stu-id="4af81-680">**Default**: `true`</span></span>  
<span data-ttu-id="4af81-681">**Argomento della riga di comando**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="4af81-681">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="4af81-682">**Variabile di ambiente**:`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="4af81-682">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="4af81-683">Il separatore due punti (`:`) non funziona con le chiavi gerarchiche variabili di ambiente su tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="4af81-683">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="4af81-684">Per ulteriori informazioni, vedere [Variabili di ambiente](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="4af81-684">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="4af81-685">Impostazioni per le app Web</span><span class="sxs-lookup"><span data-stu-id="4af81-685">Settings for web apps</span></span>

<span data-ttu-id="4af81-686">Alcune impostazioni host si applicano solo ai carichi di lavoro HTTP.</span><span class="sxs-lookup"><span data-stu-id="4af81-686">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="4af81-687">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="4af81-687">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="4af81-688">Per queste impostazioni sono disponibili i metodi di estensione su `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4af81-688">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="4af81-689">Gli esempi di codice che illustrano come chiamare i metodi di estensione presumono che `webBuilder` sia un'istanza di `IWebHostBuilder`, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="4af81-689">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="4af81-690">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="4af81-690">CaptureStartupErrors</span></span>

<span data-ttu-id="4af81-691">Quando è `false`, gli errori durante l'avvio causano la chiusura dell'host.</span><span class="sxs-lookup"><span data-stu-id="4af81-691">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="4af81-692">Quando è `true`, l'host acquisisce le eccezioni durante l'avvio e tenta di avviare il server.</span><span class="sxs-lookup"><span data-stu-id="4af81-692">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="4af81-693">**Tasto**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="4af81-693">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="4af81-694">**Tipo** `bool` :`true` `1`( o )</span><span class="sxs-lookup"><span data-stu-id="4af81-694">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4af81-695">**Impostazione predefinita**: il valore predefinito è `false` a meno che l'app non venga eseguita con Kestrel in IIS, in tal caso il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="4af81-695">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="4af81-696">**Variabile di ambiente**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="4af81-696">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="4af81-697">Per impostare questo valore, usare la configurazione o chiamare `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="4af81-697">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="4af81-698">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="4af81-698">DetailedErrors</span></span>

<span data-ttu-id="4af81-699">Quando è abilitata o quando l'ambiente è `Development`, l'app acquisisce gli errori dettagliati.</span><span class="sxs-lookup"><span data-stu-id="4af81-699">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="4af81-700">**Tasto**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="4af81-700">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="4af81-701">**Tipo** `bool` :`true` `1`( o )</span><span class="sxs-lookup"><span data-stu-id="4af81-701">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4af81-702">**Predefinito**:`false`</span><span class="sxs-lookup"><span data-stu-id="4af81-702">**Default**: `false`</span></span>  
<span data-ttu-id="4af81-703">**Variabile di ambiente**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="4af81-703">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="4af81-704">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4af81-704">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="4af81-705">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="4af81-705">HostingStartupAssemblies</span></span>

<span data-ttu-id="4af81-706">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da caricare all'avvio.</span><span class="sxs-lookup"><span data-stu-id="4af81-706">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="4af81-707">Sebbene il valore di configurazione predefinito sia una stringa vuota, gli assembly di avvio dell'hosting includono sempre l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-707">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="4af81-708">Quando vengono specificati, gli assembly di avvio dell'hosting vengono aggiunti all'assembly dell'app per essere caricati quando l'app compila i servizi comuni durante l'avvio.</span><span class="sxs-lookup"><span data-stu-id="4af81-708">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="4af81-709">**Tasto**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4af81-709">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="4af81-710">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-710">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-711">**Predefinito**: Stringa vuota</span><span class="sxs-lookup"><span data-stu-id="4af81-711">**Default**: Empty string</span></span>  
<span data-ttu-id="4af81-712">**Variabile di ambiente**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4af81-712">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="4af81-713">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4af81-713">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="4af81-714">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="4af81-714">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="4af81-715">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da escludere all'avvio.</span><span class="sxs-lookup"><span data-stu-id="4af81-715">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="4af81-716">**Tasto**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4af81-716">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="4af81-717">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-717">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-718">**Predefinito**: Stringa vuota</span><span class="sxs-lookup"><span data-stu-id="4af81-718">**Default**: Empty string</span></span>  
<span data-ttu-id="4af81-719">**Variabile di ambiente**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4af81-719">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="4af81-720">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4af81-720">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="4af81-721">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="4af81-721">HTTPS_Port</span></span>

<span data-ttu-id="4af81-722">Porta di reindirizzamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="4af81-722">The HTTPS redirect port.</span></span> <span data-ttu-id="4af81-723">Usata per [imporre HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="4af81-723">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="4af81-724">**Tasto**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="4af81-724">**Key**: `https_port`</span></span>  
<span data-ttu-id="4af81-725">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-725">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-726">**Predefinito:** non viene impostato un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="4af81-726">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="4af81-727">**Variabile di ambiente**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="4af81-727">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="4af81-728">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4af81-728">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="4af81-729">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="4af81-729">PreferHostingUrls</span></span>

<span data-ttu-id="4af81-730">Indica se l'host deve essere in ascolto sugli URL configurati con gli `IWebHostBuilder` URL anziché con quelli configurati con l'implementazione. `IServer`</span><span class="sxs-lookup"><span data-stu-id="4af81-730">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="4af81-731">**Tasto**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="4af81-731">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="4af81-732">**Tipo** `bool` :`true` `1`( o )</span><span class="sxs-lookup"><span data-stu-id="4af81-732">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4af81-733">**Predefinito**:`true`</span><span class="sxs-lookup"><span data-stu-id="4af81-733">**Default**: `true`</span></span>  
<span data-ttu-id="4af81-734">**Variabile di ambiente**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="4af81-734">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="4af81-735">Per impostare questo valore, usare la variabile di ambiente o chiamare `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="4af81-735">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="4af81-736">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="4af81-736">PreventHostingStartup</span></span>

<span data-ttu-id="4af81-737">Impedisce il caricamento automatico degli assembly di avvio dell'hosting, inclusi gli assembly di avvio dell'hosting configurati dall'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-737">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="4af81-738">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4af81-738">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="4af81-739">**Tasto**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="4af81-739">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="4af81-740">**Tipo** `bool` :`true` `1`( o )</span><span class="sxs-lookup"><span data-stu-id="4af81-740">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4af81-741">**Predefinito**:`false`</span><span class="sxs-lookup"><span data-stu-id="4af81-741">**Default**: `false`</span></span>  
<span data-ttu-id="4af81-742">**Variabile di ambiente**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="4af81-742">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="4af81-743">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4af81-743">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="4af81-744">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="4af81-744">StartupAssembly</span></span>

<span data-ttu-id="4af81-745">L'assembly per la ricerca della classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="4af81-745">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="4af81-746">**Tasto**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="4af81-746">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="4af81-747">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-747">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-748">**Impostazione predefinita**: assembly dell'app</span><span class="sxs-lookup"><span data-stu-id="4af81-748">**Default**: The app's assembly</span></span>  
<span data-ttu-id="4af81-749">**Variabile di ambiente**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="4af81-749">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="4af81-750">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="4af81-750">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="4af81-751">`UseStartup` può richiedere un nome di assembly (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="4af81-751">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="4af81-752">Se vengono chiamati più metodi `UseStartup`, l'ultimo metodo ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="4af81-752">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="4af81-753">URL</span><span class="sxs-lookup"><span data-stu-id="4af81-753">URLs</span></span>

<span data-ttu-id="4af81-754">Elenco delimitato da punto e virgola degli indirizzi IP o gli indirizzi host con le porte e protocolli su cui il server deve eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="4af81-754">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="4af81-755">Ad esempio: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="4af81-755">For example, `http://localhost:123`.</span></span> <span data-ttu-id="4af81-756">Usare "\*" per indicare che il server deve eseguire l'ascolto per le richieste su tutti gli indirizzi IP o nomi host usando la porta e il protocollo specificati (ad esempio, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="4af81-756">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="4af81-757">Il protocollo (`http://` o `https://`) deve essere incluso con ogni URL.</span><span class="sxs-lookup"><span data-stu-id="4af81-757">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="4af81-758">I formati supportati variano a seconda del server.</span><span class="sxs-lookup"><span data-stu-id="4af81-758">Supported formats vary among servers.</span></span>

<span data-ttu-id="4af81-759">**Tasto**:`urls`</span><span class="sxs-lookup"><span data-stu-id="4af81-759">**Key**: `urls`</span></span>  
<span data-ttu-id="4af81-760">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-760">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-761">**Predefinito** `http://localhost:5000` : e`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="4af81-761">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="4af81-762">**Variabile di ambiente**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="4af81-762">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="4af81-763">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="4af81-763">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="4af81-764">Kestrel ha una propria API di configurazione degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="4af81-764">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="4af81-765">Per altre informazioni, vedere <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4af81-765">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="4af81-766">WebRoot</span><span class="sxs-lookup"><span data-stu-id="4af81-766">WebRoot</span></span>

<span data-ttu-id="4af81-767">La proprietà [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina il percorso relativo degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-767">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="4af81-768">Se il percorso non esiste, viene usato un provider di file no-op.</span><span class="sxs-lookup"><span data-stu-id="4af81-768">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="4af81-769">**Tasto**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="4af81-769">**Key**: `webroot`</span></span>  
<span data-ttu-id="4af81-770">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="4af81-770">**Type**: `string`</span></span>  
<span data-ttu-id="4af81-771">**Default**: Il `wwwroot`valore predefinito è .</span><span class="sxs-lookup"><span data-stu-id="4af81-771">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="4af81-772">Il percorso della *radice del contenuto/wwwroot* deve esistere.</span><span class="sxs-lookup"><span data-stu-id="4af81-772">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="4af81-773">**Variabile di ambiente**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="4af81-773">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="4af81-774">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseWebRoot` su `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4af81-774">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="4af81-775">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="4af81-775">For more information, see:</span></span>

* [<span data-ttu-id="4af81-776">Nozioni fondamentali: radice Web</span><span class="sxs-lookup"><span data-stu-id="4af81-776">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="4af81-777">Radice contenuto</span><span class="sxs-lookup"><span data-stu-id="4af81-777">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="4af81-778">Gestire la durata dell'host</span><span class="sxs-lookup"><span data-stu-id="4af81-778">Manage the host lifetime</span></span>

<span data-ttu-id="4af81-779">Chiamare metodi sull'implementazione <xref:Microsoft.Extensions.Hosting.IHost> incorporata per avviare e arrestare l'app.</span><span class="sxs-lookup"><span data-stu-id="4af81-779">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="4af81-780">Questi metodi influiscono su tutte le implementazioni <xref:Microsoft.Extensions.Hosting.IHostedService> che vengono registrate nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="4af81-780">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="4af81-781">Esegui</span><span class="sxs-lookup"><span data-stu-id="4af81-781">Run</span></span>

<span data-ttu-id="4af81-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> esegue l'app e blocca il thread di chiamata fino all'arresto dell'host.</span><span class="sxs-lookup"><span data-stu-id="4af81-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="4af81-783">RunAsync</span><span class="sxs-lookup"><span data-stu-id="4af81-783">RunAsync</span></span>

<span data-ttu-id="4af81-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> esegue l'app e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="4af81-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="4af81-785">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="4af81-785">RunConsoleAsync</span></span>

<span data-ttu-id="4af81-786"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>abilita il supporto della console, compila e avvia l'host e attende l'arresto di <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="4af81-786"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="4af81-787">Inizia</span><span class="sxs-lookup"><span data-stu-id="4af81-787">Start</span></span>

<span data-ttu-id="4af81-788"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> avvia l'host in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="4af81-788"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="4af81-789">StartAsync</span><span class="sxs-lookup"><span data-stu-id="4af81-789">StartAsync</span></span>

<span data-ttu-id="4af81-790"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> avvia l'host e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="4af81-790"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="4af81-791"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> viene chiamato all'avvio di `StartAsync`, che attende il completamento prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="4af81-791"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="4af81-792">Questo è utile per ritardare l'avvio fino al segnale trasmesso da un evento esterno.</span><span class="sxs-lookup"><span data-stu-id="4af81-792">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="4af81-793">StopAsync</span><span class="sxs-lookup"><span data-stu-id="4af81-793">StopAsync</span></span>

<span data-ttu-id="4af81-794"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> prova ad arrestare l'host entro il timeout specificato.</span><span class="sxs-lookup"><span data-stu-id="4af81-794"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="4af81-795">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="4af81-795">WaitForShutdown</span></span>

<span data-ttu-id="4af81-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blocca il thread chiamante fino all'arresto non viene attivato da IHostLifetime, ad esempio tramite <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="4af81-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="4af81-797">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="4af81-797">WaitForShutdownAsync</span></span>

<span data-ttu-id="4af81-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato l'arresto tramite il token specificato, quindi chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4af81-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="4af81-799">Controllo esterno</span><span class="sxs-lookup"><span data-stu-id="4af81-799">External control</span></span>

<span data-ttu-id="4af81-800">Il controllo diretto della durata dell'host può essere ottenuto usando metodi che possono essere chiamati dall'esterno:</span><span class="sxs-lookup"><span data-stu-id="4af81-800">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="4af81-801">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="4af81-801">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
