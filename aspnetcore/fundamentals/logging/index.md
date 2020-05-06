---
title: Registrazione in .NET Core e ASP.NET Core
author: rick-anderson
description: Informazioni su come usare il framework di registrazione fornito dal pacchetto NuGet Microsoft.Extensions.Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: ca62e374c6031ca3c2d438df87f2d13636d9c612
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776103"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="0d829-103">Registrazione in .NET Core e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0d829-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0d829-104">Di [Tom Dykstra](https://github.com/tdykstra) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="0d829-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="0d829-105">.NET Core supporta un'API di registrazione che funziona con un'ampia gamma di provider di registrazione predefiniti e di terze parti.</span><span class="sxs-lookup"><span data-stu-id="0d829-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="0d829-106">Questo articolo illustra come usare l'API di registrazione con i provider predefiniti.</span><span class="sxs-lookup"><span data-stu-id="0d829-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="0d829-107">La maggior parte degli esempi di codice illustrati in questo articolo proviene da app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0d829-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="0d829-108">Le parti specifiche per la registrazione di questi frammenti di codice si applicano a qualsiasi app .NET Core che usa l' [host generico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="0d829-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="0d829-109">Per un esempio di come usare l'host generico in un'app console non Web, vedere il file *Program.cs* dell' [app di esempio attività in background](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span><span class="sxs-lookup"><span data-stu-id="0d829-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="0d829-110">Il codice di registrazione per le app senza host generico differisce per il modo in cui vengono [aggiunti i provider](#add-providers) e [creati i logger](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="0d829-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="0d829-111">Gli esempi di codice non host sono illustrati nelle sezioni dell'articolo in cui sono riportate queste procedure.</span><span class="sxs-lookup"><span data-stu-id="0d829-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="0d829-112">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0d829-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="0d829-113">Aggiungere provider</span><span class="sxs-lookup"><span data-stu-id="0d829-113">Add providers</span></span>

<span data-ttu-id="0d829-114">Un provider di registrazione visualizza o archivia i log.</span><span class="sxs-lookup"><span data-stu-id="0d829-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="0d829-115">Il provider Console, ad esempio, visualizza i log nella console, mentre il provider Azure Application Insights li archivia in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="0d829-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="0d829-116">I log possono essere inviati a più destinazioni aggiungendo più provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="0d829-117">Per aggiungere un provider in un'app che usa l'host generico, chiamare il metodo di estensione `Add{provider name}` del provider in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0d829-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="0d829-118">In un'app console non host chiamare il metodo di estensione `Add{provider name}` del provider durante la creazione di un oggetto `LoggerFactory`:</span><span class="sxs-lookup"><span data-stu-id="0d829-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="0d829-119">`LoggerFactory` e `AddConsole` richiedono un'istruzione `using` per `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="0d829-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="0d829-120">I modelli di progetto ASP.NET Core predefiniti chiamano <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, che aggiunge i provider di registrazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="0d829-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="0d829-121">Console</span><span class="sxs-lookup"><span data-stu-id="0d829-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="0d829-122">Debug</span><span class="sxs-lookup"><span data-stu-id="0d829-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="0d829-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="0d829-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="0d829-124">[EventLog](#windows-eventlog-provider) (solo in caso di esecuzione in Windows)</span><span class="sxs-lookup"><span data-stu-id="0d829-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="0d829-125">È possibile sostituire i provider predefiniti con quelli di propria scelta.</span><span class="sxs-lookup"><span data-stu-id="0d829-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="0d829-126">Chiamare <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e aggiungere i provider desiderati.</span><span class="sxs-lookup"><span data-stu-id="0d829-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="0d829-127">Altre informazioni sui [provider di registrazione predefiniti](#built-in-logging-providers) e sui [provider di registrazione di terze parti](#third-party-logging-providers) vengono fornite più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="0d829-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="0d829-128">Creare log</span><span class="sxs-lookup"><span data-stu-id="0d829-128">Create logs</span></span>

<span data-ttu-id="0d829-129">Per creare i log, usare un oggetto <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="0d829-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="0d829-130">In un'app Web o in un servizio ospitato, ottenere un oggetto `ILogger` da un inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="0d829-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="0d829-131">Nelle app console non host, usare `LoggerFactory` per creare un oggetto `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="0d829-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="0d829-132">L'esempio seguente di ASP.NET Core crea un logger con `TodoApiSample.Pages.AboutModel` come categoria.</span><span class="sxs-lookup"><span data-stu-id="0d829-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="0d829-133">La *categoria* del log è una stringa associata a ogni log.</span><span class="sxs-lookup"><span data-stu-id="0d829-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="0d829-134">L'istanza di `ILogger<T>` fornita dall'inserimento delle dipendenze crea log con nome completo di tipo `T` come categoria.</span><span class="sxs-lookup"><span data-stu-id="0d829-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="0d829-135">L'esempio seguente di app console non host crea un logger con `LoggingConsoleApp.Program` come categoria.</span><span class="sxs-lookup"><span data-stu-id="0d829-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="0d829-136">Negli esempi seguenti di ASP.NET Core e app console, il logger viene usato per creare log con `Information` come livello.</span><span class="sxs-lookup"><span data-stu-id="0d829-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="0d829-137">Il *livello* del log indica la gravità dell'evento registrato.</span><span class="sxs-lookup"><span data-stu-id="0d829-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="0d829-138">[Livelli](#log-level) e [categorie](#log-category) vengono illustrati in modo dettagliato più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="0d829-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="0d829-139">Creare log nella classe Program</span><span class="sxs-lookup"><span data-stu-id="0d829-139">Create logs in the Program class</span></span>

<span data-ttu-id="0d829-140">Per scrivere log nella classe `Program` di un'app ASP.NET Core, ottenere un'istanza di `ILogger` dall'inserimento delle dipendenze dopo aver creato l'host:</span><span class="sxs-lookup"><span data-stu-id="0d829-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="0d829-141">La registrazione durante la costruzione dell'host non è supportata direttamente.</span><span class="sxs-lookup"><span data-stu-id="0d829-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="0d829-142">Tuttavia, è possibile usare un logger separato.</span><span class="sxs-lookup"><span data-stu-id="0d829-142">However, a separate logger can be used.</span></span> <span data-ttu-id="0d829-143">Nell'esempio seguente viene usato un logger [Serilog](https://serilog.net/) per eseguire l'accesso `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="0d829-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="0d829-144">`AddSerilog`Usa la configurazione statica specificata in `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="0d829-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="0d829-145">Creare log nella classe Startup</span><span class="sxs-lookup"><span data-stu-id="0d829-145">Create logs in the Startup class</span></span>

<span data-ttu-id="0d829-146">Per scrivere log nel metodo `Startup.Configure` di un'app ASP.NET Core, includere un parametro `ILogger` nella firma del metodo:</span><span class="sxs-lookup"><span data-stu-id="0d829-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="0d829-147">La scrittura di log prima del completamento della configurazione del contenitore di inserimento delle dipendenze nel metodo `Startup.ConfigureServices` non è supportata:</span><span class="sxs-lookup"><span data-stu-id="0d829-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="0d829-148">L'inserimento del logger nel costruttore `Startup` non è supportato.</span><span class="sxs-lookup"><span data-stu-id="0d829-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="0d829-149">L'inserimento del logger nella firma del metodo `Startup.ConfigureServices` non è supportato</span><span class="sxs-lookup"><span data-stu-id="0d829-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="0d829-150">Questa restrizione è dovuta al fatto che la registrazione dipende dall'inserimento delle dipendenze e dalla configurazione, che a sua volta dipende dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="0d829-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="0d829-151">Il contenitore di inserimento delle dipendenze non viene configurato finché il metodo `ConfigureServices` non è completato.</span><span class="sxs-lookup"><span data-stu-id="0d829-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="0d829-152">L'inserimento di un logger nel costruttore `Startup` è possibile nelle versioni precedenti di ASP.NET Core perché viene creato un contenitore di inserimento delle dipendenze separato per l'host Web.</span><span class="sxs-lookup"><span data-stu-id="0d829-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="0d829-153">Per informazioni sul motivo per cui viene creato un solo contenitore per l'host generico, vedere l'[annuncio relativo alle modifiche di rilievo](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="0d829-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="0d829-154">Se è necessario configurare un servizio che dipende da `ILogger<T>`, è comunque possibile eseguire questa operazione usando l'inserimento nel costruttore o specificando un metodo factory.</span><span class="sxs-lookup"><span data-stu-id="0d829-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="0d829-155">L'approccio con il metodo factory è consigliato solo se non sono disponibili altre opzioni.</span><span class="sxs-lookup"><span data-stu-id="0d829-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="0d829-156">Si supponga, ad esempio, di dover specificare una proprietà con un servizio ottenuto dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="0d829-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="0d829-157">Il precedente codice evidenziato è un oggetto `Func` che viene eseguito la prima volta che il contenitore di inserimento delle dipendenze deve creare un'istanza di `MyService`.</span><span class="sxs-lookup"><span data-stu-id="0d829-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="0d829-158">È possibile accedere a uno dei servizi registrati in questo modo.</span><span class="sxs-lookup"><span data-stu-id="0d829-158">You can access any of the registered services in this way.</span></span>

### <a name="create-logs-in-blazor"></a><span data-ttu-id="0d829-159">Creare log in blazer</span><span class="sxs-lookup"><span data-stu-id="0d829-159">Create logs in Blazor</span></span>

#### <a name="blazor-webassembly"></a><span data-ttu-id="0d829-160">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="0d829-160">Blazor WebAssembly</span></span>

<span data-ttu-id="0d829-161">Configurare la registrazione nelle app webassembly Blazer con `WebAssemblyHostBuilder.Logging` la proprietà `Program.Main`in:</span><span class="sxs-lookup"><span data-stu-id="0d829-161">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="0d829-162">La `Logging` proprietà è di tipo <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, pertanto tutti i metodi di estensione disponibili in <xref:Microsoft.Extensions.Logging.ILoggingBuilder> sono disponibili anche in `Logging`.</span><span class="sxs-lookup"><span data-stu-id="0d829-162">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

#### <a name="log-in-razor-components"></a><span data-ttu-id="0d829-163">Accedi ai componenti Razor</span><span class="sxs-lookup"><span data-stu-id="0d829-163">Log in Razor components</span></span>

<span data-ttu-id="0d829-164">I logger rispettano la configurazione di avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-164">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="0d829-165">La `using` direttiva per <xref:Microsoft.Extensions.Logging> è necessaria per supportare i completamenti IntelliSense per <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> le API, ad <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>esempio e.</span><span class="sxs-lookup"><span data-stu-id="0d829-165">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="0d829-166">Nell'esempio seguente viene illustrata la <xref:Microsoft.Extensions.Logging.ILogger> registrazione con un nei componenti Razor:</span><span class="sxs-lookup"><span data-stu-id="0d829-166">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="0d829-167">Nell'esempio seguente viene illustrata la <xref:Microsoft.Extensions.Logging.ILoggerFactory> registrazione con un nei componenti Razor:</span><span class="sxs-lookup"><span data-stu-id="0d829-167">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="0d829-168">Evitare l'uso di metodi logger asincroni</span><span class="sxs-lookup"><span data-stu-id="0d829-168">No asynchronous logger methods</span></span>

<span data-ttu-id="0d829-169">La registrazione deve essere così rapida da non giustificare l'impatto sulle prestazioni del codice asincrono.</span><span class="sxs-lookup"><span data-stu-id="0d829-169">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="0d829-170">Se l'archivio dati di registrazione è lento, non scrivere direttamente al suo interno.</span><span class="sxs-lookup"><span data-stu-id="0d829-170">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="0d829-171">Scrivere invece i messaggi di log prima in un archivio veloce e quindi spostarli nell'archivio lento in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="0d829-171">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="0d829-172">Ad esempio, se la registrazione viene eseguita in SQL Server, è preferibile non farlo direttamente in un metodo `Log`, poiché i metodi `Log` sono sincroni.</span><span class="sxs-lookup"><span data-stu-id="0d829-172">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="0d829-173">Al contrario, aggiungere i messaggi di log in modo sincrono a una coda in memoria e usare un ruolo di lavoro in background per eseguire il pull dei messaggi dalla coda per eseguire le operazioni asincrone di push dei dati in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="0d829-173">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="0d829-174">Per altre informazioni, vedere [questo](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problema di GitHub.</span><span class="sxs-lookup"><span data-stu-id="0d829-174">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="0d829-175">Configurazione</span><span class="sxs-lookup"><span data-stu-id="0d829-175">Configuration</span></span>

<span data-ttu-id="0d829-176">La configurazione dei provider di registrazione viene fornita da uno o più provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="0d829-176">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="0d829-177">Formati di file (INI, JSON e XML).</span><span class="sxs-lookup"><span data-stu-id="0d829-177">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="0d829-178">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="0d829-178">Command-line arguments.</span></span>
* <span data-ttu-id="0d829-179">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="0d829-179">Environment variables.</span></span>
* <span data-ttu-id="0d829-180">Oggetti .NET in memoria.</span><span class="sxs-lookup"><span data-stu-id="0d829-180">In-memory .NET objects.</span></span>
* <span data-ttu-id="0d829-181">Archiviazione con [Secret Manager](xref:security/app-secrets) senza crittografia.</span><span class="sxs-lookup"><span data-stu-id="0d829-181">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="0d829-182">Un archivio utente non crittografato, come [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="0d829-182">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="0d829-183">Provider personalizzati (installati o creati).</span><span class="sxs-lookup"><span data-stu-id="0d829-183">Custom providers (installed or created).</span></span>

<span data-ttu-id="0d829-184">Ad esempio, la configurazione di registrazione viene comunemente fornita dalla sezione `Logging` del file di impostazioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-184">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="0d829-185">L'esempio seguente mostra il contenuto di un file *appsettings.Development.json* tipico:</span><span class="sxs-lookup"><span data-stu-id="0d829-185">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="0d829-186">La proprietà `Logging` può avere le proprietà `LogLevel` e quella del provider di log (nell'esempio, Console).</span><span class="sxs-lookup"><span data-stu-id="0d829-186">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="0d829-187">La proprietà `LogLevel` in `Logging` specifica il [livello](#log-level) minimo per la registrazione per determinate categorie.</span><span class="sxs-lookup"><span data-stu-id="0d829-187">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="0d829-188">Nell'esempio, le categorie `System` e `Microsoft` eseguono la registrazione al livello `Information`, mentre tutte le altre la eseguono al livello `Debug`.</span><span class="sxs-lookup"><span data-stu-id="0d829-188">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="0d829-189">Altre proprietà in `Logging` specificano i provider di registrazione.</span><span class="sxs-lookup"><span data-stu-id="0d829-189">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="0d829-190">L'esempio si riferisce al provider Console.</span><span class="sxs-lookup"><span data-stu-id="0d829-190">The example is for the Console provider.</span></span> <span data-ttu-id="0d829-191">Se un provider supporta gli [ambiti](#log-scopes)del `IncludeScopes` log, indica se sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="0d829-191">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="0d829-192">Una proprietà del provider (ad esempio `Console` nell'esempio) può specificare anche una proprietà `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="0d829-192">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="0d829-193">`LogLevel` in un provider specifica i livelli di registrazione per tale provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-193">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="0d829-194">Se i livelli sono specificati in `Logging.{providername}.LogLevel`, eseguono l'override di eventuali valori impostati in `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="0d829-194">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="0d829-195">L'API di registrazione non include uno scenario per modificare i livelli di registrazione mentre un'app è in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="0d829-195">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="0d829-196">Alcuni provider di configurazione, tuttavia, sono in grado di ricaricare la configurazione, operazione che ha effetto immediato sulla configurazione della registrazione.</span><span class="sxs-lookup"><span data-stu-id="0d829-196">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="0d829-197">Ad esempio, il [provider di configurazione file](xref:fundamentals/configuration/index#file-configuration-provider), aggiunto da `CreateDefaultBuilder` per leggere i file di impostazioni, ricarica la configurazione di registrazione per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="0d829-197">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="0d829-198">Se la configurazione viene modificata nel codice mentre un'app è in esecuzione, l'app può chiamare [IConfigurationRoot. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) per aggiornare la configurazione di registrazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-198">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="0d829-199">Per informazioni sull'implementazione dei provider di configurazione, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="0d829-199">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="0d829-200">Esempio di output di registrazione</span><span class="sxs-lookup"><span data-stu-id="0d829-200">Sample logging output</span></span>

<span data-ttu-id="0d829-201">Con il codice di esempio illustrato nella sezione precedente i log vengono visualizzati nella console quando l'app viene eseguita dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="0d829-201">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="0d829-202">Ecco un esempio di output della console:</span><span class="sxs-lookup"><span data-stu-id="0d829-202">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

<span data-ttu-id="0d829-203">I log precedenti sono stati generati inviando una richiesta HTTP Get all'app di esempio all'indirizzo `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="0d829-203">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="0d829-204">Ecco un esempio degli stessi log visualizzati nella finestra Debug quando si esegue l'app di esempio in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="0d829-204">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

<span data-ttu-id="0d829-205">I log creati dalle chiamate a `ILogger` illustrate nella sezione precedente iniziano con "TodoApiSample".</span><span class="sxs-lookup"><span data-stu-id="0d829-205">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="0d829-206">I log che iniziano con le categorie "Microsoft" provengono dal codice del framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0d829-206">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="0d829-207">ASP.NET Core e il codice dell'applicazione usano la stessa API di registrazione e gli stessi provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-207">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="0d829-208">Il resto di questo articolo spiega alcuni dettagli e le opzioni per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="0d829-208">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="0d829-209">Pacchetti NuGet</span><span class="sxs-lookup"><span data-stu-id="0d829-209">NuGet packages</span></span>

<span data-ttu-id="0d829-210">Le interfacce `ILogger` e `ILoggerFactory` si trovano in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) e le loro implementazioni predefinite si trovano in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="0d829-210">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="0d829-211">Categoria di log</span><span class="sxs-lookup"><span data-stu-id="0d829-211">Log category</span></span>

<span data-ttu-id="0d829-212">Quando viene creato un oggetto `ILogger`, viene specificata la relativa *categoria*.</span><span class="sxs-lookup"><span data-stu-id="0d829-212">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="0d829-213">La categoria è inclusa in ogni messaggio di log creato da tale istanza di `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="0d829-213">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="0d829-214">La categoria può essere qualsiasi stringa, ma per convenzione si usa il nome della classe, ad esempio "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="0d829-214">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="0d829-215">Usare `ILogger<T>` per ottenere un'istanza di `ILogger` che usa il nome completo di tipo `T` come categoria:</span><span class="sxs-lookup"><span data-stu-id="0d829-215">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="0d829-216">Per specificare in modo esplicito la categoria, chiamare `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="0d829-216">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="0d829-217">L'uso di `ILogger<T>` equivale a chiamare `CreateLogger` con il nome completo di tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="0d829-217">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="0d829-218">Livello di registrazione</span><span class="sxs-lookup"><span data-stu-id="0d829-218">Log level</span></span>

<span data-ttu-id="0d829-219">Ogni log specifica un valore <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="0d829-219">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="0d829-220">Il livello di registrazione indica la gravità o l'importanza.</span><span class="sxs-lookup"><span data-stu-id="0d829-220">The log level indicates the severity or importance.</span></span> <span data-ttu-id="0d829-221">È ad esempio possibile scrivere un log `Information` quando un metodo termina normalmente e un log `Warning` quando un metodo restituisce un codice di stato *404 Non trovato*.</span><span class="sxs-lookup"><span data-stu-id="0d829-221">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="0d829-222">Il codice seguente crea i log `Information` e `Warning`:</span><span class="sxs-lookup"><span data-stu-id="0d829-222">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="0d829-223">Nel codice precedente il primo parametro è l'[ID dell'evento di log](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="0d829-223">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="0d829-224">Il secondo parametro è un modello di messaggio con segnaposto per i valori degli argomenti forniti dai parametri dei metodi rimanenti.</span><span class="sxs-lookup"><span data-stu-id="0d829-224">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="0d829-225">I parametri dei metodi sono descritti nella [sezione relativa al modello di messaggio](#log-message-template) più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="0d829-225">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="0d829-226">I metodi di registrazione che includono il livello nel nome del metodo (ad esempio `LogInformation` e `LogWarning`) sono [metodi di estensione per ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="0d829-226">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="0d829-227">Questi metodi chiamano un metodo `Log` che accetta un parametro `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="0d829-227">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="0d829-228">È possibile chiamare il metodo `Log` direttamente anziché chiamare uno di questi metodi di estensione, ma la sintassi è relativamente complessa.</span><span class="sxs-lookup"><span data-stu-id="0d829-228">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="0d829-229">Per altre informazioni, vedere <xref:Microsoft.Extensions.Logging.ILogger> e il [codice sorgente delle estensioni del logger](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="0d829-229">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="0d829-230">ASP.NET Core definisce i livelli di registrazione seguenti, ordinati dal meno grave al più grave.</span><span class="sxs-lookup"><span data-stu-id="0d829-230">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="0d829-231">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="0d829-231">Trace = 0</span></span>

  <span data-ttu-id="0d829-232">Per informazioni in genere utili solo per il debug.</span><span class="sxs-lookup"><span data-stu-id="0d829-232">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="0d829-233">Questi messaggi possono contenere dati sensibili dell'applicazione e pertanto non devono essere abilitati in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="0d829-233">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="0d829-234">*Disattivato per impostazione predefinita.*</span><span class="sxs-lookup"><span data-stu-id="0d829-234">*Disabled by default.*</span></span>

* <span data-ttu-id="0d829-235">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="0d829-235">Debug = 1</span></span>

  <span data-ttu-id="0d829-236">Per informazioni che possono essere utili per lo sviluppo e il debug.</span><span class="sxs-lookup"><span data-stu-id="0d829-236">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="0d829-237">Esempio: `Entering method Configure with flag set to true.` Abilitare i livelli di registrazione `Debug` nell'ambiente di produzione solo in fase di risoluzione dei problemi, per non fare aumentare eccessivamente il volume dei log.</span><span class="sxs-lookup"><span data-stu-id="0d829-237">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="0d829-238">Information = 2</span><span class="sxs-lookup"><span data-stu-id="0d829-238">Information = 2</span></span>

  <span data-ttu-id="0d829-239">Per tenere traccia del flusso generale dell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-239">For tracking the general flow of the app.</span></span> <span data-ttu-id="0d829-240">Queste registrazioni hanno in genere un valore a lungo termine.</span><span class="sxs-lookup"><span data-stu-id="0d829-240">These logs typically have some long-term value.</span></span> <span data-ttu-id="0d829-241">Esempio: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="0d829-241">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="0d829-242">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="0d829-242">Warning = 3</span></span>

  <span data-ttu-id="0d829-243">Per gli eventi imprevisti o anomali nel flusso dell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-243">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="0d829-244">Tali eventi potrebbero includere errori o altre condizioni che non provocano l'arresto dell'app, ma che potrebbe essere necessario analizzare.</span><span class="sxs-lookup"><span data-stu-id="0d829-244">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="0d829-245">Le eccezioni gestite sono una situazione comune in cui usare il livello di registrazione `Warning`.</span><span class="sxs-lookup"><span data-stu-id="0d829-245">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="0d829-246">Esempio: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="0d829-246">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="0d829-247">Error = 4</span><span class="sxs-lookup"><span data-stu-id="0d829-247">Error = 4</span></span>

  <span data-ttu-id="0d829-248">Per errori ed eccezioni che non possono essere gestiti.</span><span class="sxs-lookup"><span data-stu-id="0d829-248">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="0d829-249">Questi messaggi indicano un errore nell'operazione o nell'attività in corso (ad esempio la richiesta HTTP corrente), non un errore a livello di app.</span><span class="sxs-lookup"><span data-stu-id="0d829-249">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="0d829-250">Messaggio di registrazione di esempio:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="0d829-250">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="0d829-251">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="0d829-251">Critical = 5</span></span>

  <span data-ttu-id="0d829-252">Per gli errori che richiedono attenzione immediata.</span><span class="sxs-lookup"><span data-stu-id="0d829-252">For failures that require immediate attention.</span></span> <span data-ttu-id="0d829-253">Esempi: scenari di perdita di dati, spazio su disco insufficiente.</span><span class="sxs-lookup"><span data-stu-id="0d829-253">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="0d829-254">Usare il livello di registrazione per controllare la quantità di output di log scritto in un supporto di archiviazione specifico o in una finestra.</span><span class="sxs-lookup"><span data-stu-id="0d829-254">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="0d829-255">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0d829-255">For example:</span></span>

* <span data-ttu-id="0d829-256">In produzione:</span><span class="sxs-lookup"><span data-stu-id="0d829-256">In production:</span></span>
  * <span data-ttu-id="0d829-257">La `Trace` registrazione ai livelli `Information` attraverso produce un volume elevato di messaggi di log dettagliati.</span><span class="sxs-lookup"><span data-stu-id="0d829-257">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="0d829-258">Per controllare i costi e non superare i limiti di archiviazione `Trace` dei `Information` dati, accedere ai messaggi di livello elevato a un archivio dati a basso costo.</span><span class="sxs-lookup"><span data-stu-id="0d829-258">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="0d829-259">L'accesso `Warning` ai `Critical` livelli in genere produce un minor numero di messaggi di log più piccoli.</span><span class="sxs-lookup"><span data-stu-id="0d829-259">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="0d829-260">Pertanto, i costi e i limiti di archiviazione in genere non rappresentano un problema, il che comporta una maggiore flessibilità di scelta dell'archivio dati.</span><span class="sxs-lookup"><span data-stu-id="0d829-260">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="0d829-261">Durante lo sviluppo:</span><span class="sxs-lookup"><span data-stu-id="0d829-261">During development:</span></span>
  * <span data-ttu-id="0d829-262">Consente `Warning` di `Critical` accedere ai messaggi nella console.</span><span class="sxs-lookup"><span data-stu-id="0d829-262">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="0d829-263">Aggiungere `Trace` `Information` messaggi durante la risoluzione dei problemi.</span><span class="sxs-lookup"><span data-stu-id="0d829-263">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="0d829-264">La sezione [Filtro dei log](#log-filtering) più avanti in questo articolo descrive come controllare quali livelli di registrazione gestisce un provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-264">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="0d829-265">ASP.NET Core scrive log per gli eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="0d829-265">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="0d829-266">Gli esempi di log illustrati in precedenza in questo articolo escludono i log al di sotto del livello `Information`, quindi non vengono creati log di livello `Debug` o `Trace`.</span><span class="sxs-lookup"><span data-stu-id="0d829-266">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="0d829-267">Ecco un esempio di log della console prodotti eseguendo l'app di esempio configurata in modo da mostrare i log di livello `Debug`:</span><span class="sxs-lookup"><span data-stu-id="0d829-267">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="0d829-268">ID evento di registrazione</span><span class="sxs-lookup"><span data-stu-id="0d829-268">Log event ID</span></span>

<span data-ttu-id="0d829-269">Ogni log può specificare un *ID evento*.</span><span class="sxs-lookup"><span data-stu-id="0d829-269">Each log can specify an *event ID*.</span></span> <span data-ttu-id="0d829-270">A tale scopo, l'app di esempio usa una classe `LoggingEvents` definita a livello locale:</span><span class="sxs-lookup"><span data-stu-id="0d829-270">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="0d829-271">Un ID evento associa un set di eventi.</span><span class="sxs-lookup"><span data-stu-id="0d829-271">An event ID associates a set of events.</span></span> <span data-ttu-id="0d829-272">Ad esempio, tutti i log correlati alla visualizzazione di un elenco di elementi in una pagina potrebbero essere indicati con 1001.</span><span class="sxs-lookup"><span data-stu-id="0d829-272">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="0d829-273">Il provider di log può archiviare l'ID evento in un campo ID o nel messaggio di registrazione oppure non archiviarlo.</span><span class="sxs-lookup"><span data-stu-id="0d829-273">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="0d829-274">Il provider Debug non visualizza gli ID evento.</span><span class="sxs-lookup"><span data-stu-id="0d829-274">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="0d829-275">Il provider Console visualizza gli ID evento tra parentesi quadre dopo la categoria:</span><span class="sxs-lookup"><span data-stu-id="0d829-275">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="0d829-276">Modello di messaggio di registrazione</span><span class="sxs-lookup"><span data-stu-id="0d829-276">Log message template</span></span>

<span data-ttu-id="0d829-277">Ogni log specifica un modello di messaggio.</span><span class="sxs-lookup"><span data-stu-id="0d829-277">Each log specifies a message template.</span></span> <span data-ttu-id="0d829-278">Il modello di messaggio può contenere segnaposto per i quali vengono forniti argomenti.</span><span class="sxs-lookup"><span data-stu-id="0d829-278">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="0d829-279">Usare nomi per i segnaposto, non numeri.</span><span class="sxs-lookup"><span data-stu-id="0d829-279">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="0d829-280">L'ordine dei segnaposto, non il loro nome, determina i parametri da usare per fornire i valori corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="0d829-280">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="0d829-281">Nel codice seguente, si noti che i nomi dei parametri non sono in sequenza nel modello di messaggio:</span><span class="sxs-lookup"><span data-stu-id="0d829-281">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="0d829-282">Questo codice crea un messaggio di log con i valori dei parametri in sequenza:</span><span class="sxs-lookup"><span data-stu-id="0d829-282">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="0d829-283">Il framework di registrazione funziona in questo modo per consentire ai provider di registrazione di implementare la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="0d829-283">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="0d829-284">Al sistema di registrazione vengono passati gli argomenti e non solo il modello di messaggio formattato.</span><span class="sxs-lookup"><span data-stu-id="0d829-284">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="0d829-285">Queste informazioni consentono ai provider di registrazione di archiviare i valori dei parametri come campi.</span><span class="sxs-lookup"><span data-stu-id="0d829-285">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="0d829-286">Si supponga, ad esempio, che il metodo logger esegua chiamate simili alla seguente:</span><span class="sxs-lookup"><span data-stu-id="0d829-286">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="0d829-287">Se si inviano i log all'archiviazione tabelle di Azure, ogni entità tabella di Azure può avere le proprietà `ID` e `RequestTime`, il che semplifica le query sui dati dei log.</span><span class="sxs-lookup"><span data-stu-id="0d829-287">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="0d829-288">Una query può trovare tutti i log entro un determinato intervallo `RequestTime` senza che sia necessario analizzare il messaggio di testo per determinare l'intervallo di tempo.</span><span class="sxs-lookup"><span data-stu-id="0d829-288">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="0d829-289">Registrazione delle eccezioni</span><span class="sxs-lookup"><span data-stu-id="0d829-289">Logging exceptions</span></span>

<span data-ttu-id="0d829-290">I metodi logger dispongono di overload che consentono di passare un'eccezione, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="0d829-290">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="0d829-291">Provider diversi gestiscono le informazioni sulle eccezioni in modi diversi.</span><span class="sxs-lookup"><span data-stu-id="0d829-291">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="0d829-292">Ecco un esempio di output del provider Debug dal codice sopra riportato.</span><span class="sxs-lookup"><span data-stu-id="0d829-292">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="0d829-293">Filtro del log</span><span class="sxs-lookup"><span data-stu-id="0d829-293">Log filtering</span></span>

<span data-ttu-id="0d829-294">È possibile specificare un livello di registrazione minimo per un provider e una categoria specifici oppure per tutti i provider o tutte le categorie.</span><span class="sxs-lookup"><span data-stu-id="0d829-294">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="0d829-295">Tutti i log sotto il livello minimo non sono passati al provider, quindi non vengono visualizzati o archiviati.</span><span class="sxs-lookup"><span data-stu-id="0d829-295">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="0d829-296">Per eliminare tutti i log, specificare `LogLevel.None` come livello di registrazione minimo.</span><span class="sxs-lookup"><span data-stu-id="0d829-296">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="0d829-297">Il valore intero di `LogLevel.None` è 6, maggiore di `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="0d829-297">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="0d829-298">Creare regole di filtro nella configurazione</span><span class="sxs-lookup"><span data-stu-id="0d829-298">Create filter rules in configuration</span></span>

<span data-ttu-id="0d829-299">Il codice del modello di `CreateDefaultBuilder` progetto chiama per impostare la registrazione per i provider console, debug e EventSource (ASP.NET Core 2,2 o versione successiva).</span><span class="sxs-lookup"><span data-stu-id="0d829-299">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="0d829-300">Il metodo `CreateDefaultBuilder` imposta inoltre la registrazione per la ricerca della configurazione in una sezione `Logging`, come illustrato [in precedenza in questo articolo](#configuration).</span><span class="sxs-lookup"><span data-stu-id="0d829-300">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="0d829-301">I dati di configurazione specificano i livelli di registrazione minimi in base al provider e alla categoria, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="0d829-301">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="0d829-302">Questo codice JSON crea sei regole di filtro, una per il provider Debug, quattro per il provider Console e una per tutti i provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-302">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="0d829-303">Quando viene creato un oggetto `ILogger`, viene scelta una singola regola per ogni provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-303">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="0d829-304">Regole di filtro nel codice</span><span class="sxs-lookup"><span data-stu-id="0d829-304">Filter rules in code</span></span>

<span data-ttu-id="0d829-305">L'esempio seguente illustra come registrare le regole di filtro nel codice:</span><span class="sxs-lookup"><span data-stu-id="0d829-305">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="0d829-306">Il secondo `AddFilter` specifica il provider Debug tramite il nome del tipo.</span><span class="sxs-lookup"><span data-stu-id="0d829-306">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="0d829-307">Il primo `AddFilter` si applica a tutti i provider in quanto non consente di specificare un tipo di provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-307">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="0d829-308">Applicazione delle regole di filtro</span><span class="sxs-lookup"><span data-stu-id="0d829-308">How filtering rules are applied</span></span>

<span data-ttu-id="0d829-309">I dati di configurazione e il codice `AddFilter` illustrato negli esempi precedenti creano le regole indicate nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="0d829-309">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="0d829-310">I primi sei provengono dall'esempio di configurazione e gli ultimi due dall'esempio di codice.</span><span class="sxs-lookup"><span data-stu-id="0d829-310">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="0d829-311">Number</span><span class="sxs-lookup"><span data-stu-id="0d829-311">Number</span></span> | <span data-ttu-id="0d829-312">Provider</span><span class="sxs-lookup"><span data-stu-id="0d829-312">Provider</span></span>      | <span data-ttu-id="0d829-313">Categorie che iniziano con...</span><span class="sxs-lookup"><span data-stu-id="0d829-313">Categories that begin with ...</span></span>          | <span data-ttu-id="0d829-314">Livello di registrazione minimo</span><span class="sxs-lookup"><span data-stu-id="0d829-314">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="0d829-315">1</span><span class="sxs-lookup"><span data-stu-id="0d829-315">1</span></span>      | <span data-ttu-id="0d829-316">Debug</span><span class="sxs-lookup"><span data-stu-id="0d829-316">Debug</span></span>         | <span data-ttu-id="0d829-317">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="0d829-317">All categories</span></span>                          | <span data-ttu-id="0d829-318">Informazioni</span><span class="sxs-lookup"><span data-stu-id="0d829-318">Information</span></span>       |
| <span data-ttu-id="0d829-319">2</span><span class="sxs-lookup"><span data-stu-id="0d829-319">2</span></span>      | <span data-ttu-id="0d829-320">Console</span><span class="sxs-lookup"><span data-stu-id="0d829-320">Console</span></span>       | <span data-ttu-id="0d829-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="0d829-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="0d829-322">Avviso</span><span class="sxs-lookup"><span data-stu-id="0d829-322">Warning</span></span>           |
| <span data-ttu-id="0d829-323">3</span><span class="sxs-lookup"><span data-stu-id="0d829-323">3</span></span>      | <span data-ttu-id="0d829-324">Console</span><span class="sxs-lookup"><span data-stu-id="0d829-324">Console</span></span>       | <span data-ttu-id="0d829-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="0d829-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="0d829-326">Debug</span><span class="sxs-lookup"><span data-stu-id="0d829-326">Debug</span></span>             |
| <span data-ttu-id="0d829-327">4</span><span class="sxs-lookup"><span data-stu-id="0d829-327">4</span></span>      | <span data-ttu-id="0d829-328">Console</span><span class="sxs-lookup"><span data-stu-id="0d829-328">Console</span></span>       | <span data-ttu-id="0d829-329">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="0d829-329">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="0d829-330">Errore</span><span class="sxs-lookup"><span data-stu-id="0d829-330">Error</span></span>             |
| <span data-ttu-id="0d829-331">5</span><span class="sxs-lookup"><span data-stu-id="0d829-331">5</span></span>      | <span data-ttu-id="0d829-332">Console</span><span class="sxs-lookup"><span data-stu-id="0d829-332">Console</span></span>       | <span data-ttu-id="0d829-333">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="0d829-333">All categories</span></span>                          | <span data-ttu-id="0d829-334">Informazioni</span><span class="sxs-lookup"><span data-stu-id="0d829-334">Information</span></span>       |
| <span data-ttu-id="0d829-335">6</span><span class="sxs-lookup"><span data-stu-id="0d829-335">6</span></span>      | <span data-ttu-id="0d829-336">Tutti i provider</span><span class="sxs-lookup"><span data-stu-id="0d829-336">All providers</span></span> | <span data-ttu-id="0d829-337">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="0d829-337">All categories</span></span>                          | <span data-ttu-id="0d829-338">Debug</span><span class="sxs-lookup"><span data-stu-id="0d829-338">Debug</span></span>             |
| <span data-ttu-id="0d829-339">7</span><span class="sxs-lookup"><span data-stu-id="0d829-339">7</span></span>      | <span data-ttu-id="0d829-340">Tutti i provider</span><span class="sxs-lookup"><span data-stu-id="0d829-340">All providers</span></span> | <span data-ttu-id="0d829-341">Sistema</span><span class="sxs-lookup"><span data-stu-id="0d829-341">System</span></span>                                  | <span data-ttu-id="0d829-342">Debug</span><span class="sxs-lookup"><span data-stu-id="0d829-342">Debug</span></span>             |
| <span data-ttu-id="0d829-343">8</span><span class="sxs-lookup"><span data-stu-id="0d829-343">8</span></span>      | <span data-ttu-id="0d829-344">Debug</span><span class="sxs-lookup"><span data-stu-id="0d829-344">Debug</span></span>         | <span data-ttu-id="0d829-345">Microsoft</span><span class="sxs-lookup"><span data-stu-id="0d829-345">Microsoft</span></span>                               | <span data-ttu-id="0d829-346">Trace</span><span class="sxs-lookup"><span data-stu-id="0d829-346">Trace</span></span>             |

<span data-ttu-id="0d829-347">Quando viene creato un oggetto `ILogger`, l'oggetto `ILoggerFactory` seleziona una singola regola per ogni provider da applicare al logger.</span><span class="sxs-lookup"><span data-stu-id="0d829-347">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="0d829-348">Tutti i messaggi scritti da un'istanza di `ILogger` vengono filtrati in base alle regole selezionate.</span><span class="sxs-lookup"><span data-stu-id="0d829-348">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="0d829-349">Tra le regole disponibili viene selezionata la regola più specifica possibile per ogni coppia di categoria e provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-349">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="0d829-350">L'algoritmo seguente viene usato per ogni provider quando viene creato un `ILogger` per una determinata categoria:</span><span class="sxs-lookup"><span data-stu-id="0d829-350">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="0d829-351">Selezionare tutte le regole corrispondenti al provider o al relativo alias.</span><span class="sxs-lookup"><span data-stu-id="0d829-351">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="0d829-352">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole con un provider vuoto.</span><span class="sxs-lookup"><span data-stu-id="0d829-352">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="0d829-353">Dal risultato del passaggio precedente, selezionare le regole con il prefisso di categoria corrispondente più lungo.</span><span class="sxs-lookup"><span data-stu-id="0d829-353">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="0d829-354">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole che non specificano una categoria.</span><span class="sxs-lookup"><span data-stu-id="0d829-354">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="0d829-355">Se sono selezionate più regole, scegliere l'**ultima**.</span><span class="sxs-lookup"><span data-stu-id="0d829-355">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="0d829-356">Se non è selezionata alcuna regola, usare `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="0d829-356">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="0d829-357">Con l'elenco di regole precedente, si supponga di creare un oggetto `ILogger` per la categoria "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="0d829-357">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="0d829-358">Per il provider Debug si applicano le regole 1, 6 e 8.</span><span class="sxs-lookup"><span data-stu-id="0d829-358">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="0d829-359">La regola 8 è più specifica, così sarà quella selezionata.</span><span class="sxs-lookup"><span data-stu-id="0d829-359">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="0d829-360">Per il provider Console si applicano le regole 3, 4, 5 e 6.</span><span class="sxs-lookup"><span data-stu-id="0d829-360">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="0d829-361">La regola 3 è la più specifica.</span><span class="sxs-lookup"><span data-stu-id="0d829-361">Rule 3 is most specific.</span></span>

<span data-ttu-id="0d829-362">L'istanza di `ILogger` risultante invia i log di livello `Trace` o superiore al provider Debug.</span><span class="sxs-lookup"><span data-stu-id="0d829-362">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="0d829-363">I log di livello `Debug` o superiore vengono inviati al provider Console.</span><span class="sxs-lookup"><span data-stu-id="0d829-363">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="0d829-364">Alias dei provider</span><span class="sxs-lookup"><span data-stu-id="0d829-364">Provider aliases</span></span>

<span data-ttu-id="0d829-365">Ogni provider definisce un *alias* che può essere utilizzato nella configurazione al posto del nome completo di tipo.</span><span class="sxs-lookup"><span data-stu-id="0d829-365">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="0d829-366">Per i provider predefiniti, usare gli alias seguenti:</span><span class="sxs-lookup"><span data-stu-id="0d829-366">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="0d829-367">Console</span><span class="sxs-lookup"><span data-stu-id="0d829-367">Console</span></span>
* <span data-ttu-id="0d829-368">Debug</span><span class="sxs-lookup"><span data-stu-id="0d829-368">Debug</span></span>
* <span data-ttu-id="0d829-369">EventSource</span><span class="sxs-lookup"><span data-stu-id="0d829-369">EventSource</span></span>
* <span data-ttu-id="0d829-370">EventLog</span><span class="sxs-lookup"><span data-stu-id="0d829-370">EventLog</span></span>
* <span data-ttu-id="0d829-371">TraceSource</span><span class="sxs-lookup"><span data-stu-id="0d829-371">TraceSource</span></span>
* <span data-ttu-id="0d829-372">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="0d829-372">AzureAppServicesFile</span></span>
* <span data-ttu-id="0d829-373">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="0d829-373">AzureAppServicesBlob</span></span>
* <span data-ttu-id="0d829-374">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="0d829-374">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="0d829-375">Livello minimo predefinito</span><span class="sxs-lookup"><span data-stu-id="0d829-375">Default minimum level</span></span>

<span data-ttu-id="0d829-376">Esiste un'impostazione del livello minimo che diventa effettiva solo se non si applica alcuna regola di configurazione o codice per un provider e una categoria specifici.</span><span class="sxs-lookup"><span data-stu-id="0d829-376">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="0d829-377">L'esempio seguente illustra come impostare il livello minimo:</span><span class="sxs-lookup"><span data-stu-id="0d829-377">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="0d829-378">Se non si imposta in modo esplicito il livello minimo, il valore predefinito è `Information`, che indica che i log `Trace` e `Debug` vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="0d829-378">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="0d829-379">Funzioni di filtro</span><span class="sxs-lookup"><span data-stu-id="0d829-379">Filter functions</span></span>

<span data-ttu-id="0d829-380">Una funzione di filtro viene richiamata per tutti i provider e le categorie a cui non sono assegnate regole tramite la configurazione o il codice.</span><span class="sxs-lookup"><span data-stu-id="0d829-380">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="0d829-381">Il codice della funzione ha accesso al tipo di provider, alla categoria e al livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="0d829-381">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="0d829-382">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0d829-382">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="0d829-383">Livelli e categorie di sistema</span><span class="sxs-lookup"><span data-stu-id="0d829-383">System categories and levels</span></span>

<span data-ttu-id="0d829-384">Di seguito sono elencate alcune categorie usate da ASP.NET Core ed Entity Framework Core, con note sui log previsti per ognuna:</span><span class="sxs-lookup"><span data-stu-id="0d829-384">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="0d829-385">Category</span><span class="sxs-lookup"><span data-stu-id="0d829-385">Category</span></span>                            | <span data-ttu-id="0d829-386">Note</span><span class="sxs-lookup"><span data-stu-id="0d829-386">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="0d829-387">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="0d829-387">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="0d829-388">Diagnostica generale di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0d829-388">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="0d829-389">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="0d829-389">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="0d829-390">Chiavi considerate, trovate e usate.</span><span class="sxs-lookup"><span data-stu-id="0d829-390">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="0d829-391">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="0d829-391">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="0d829-392">Host consentiti.</span><span class="sxs-lookup"><span data-stu-id="0d829-392">Hosts allowed.</span></span> |
| <span data-ttu-id="0d829-393">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="0d829-393">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="0d829-394">Tempo impiegato per il completamento delle richieste HTTP e ora di inizio delle richieste.</span><span class="sxs-lookup"><span data-stu-id="0d829-394">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="0d829-395">Assembly di avvio dell'hosting caricati.</span><span class="sxs-lookup"><span data-stu-id="0d829-395">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="0d829-396">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="0d829-396">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="0d829-397">Diagnostica di MVC e Razor.</span><span class="sxs-lookup"><span data-stu-id="0d829-397">MVC and Razor diagnostics.</span></span> <span data-ttu-id="0d829-398">Associazione di modelli, esecuzione di filtri, compilazione di viste, selezione di azioni.</span><span class="sxs-lookup"><span data-stu-id="0d829-398">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="0d829-399">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="0d829-399">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="0d829-400">Informazioni sulla corrispondenza di route.</span><span class="sxs-lookup"><span data-stu-id="0d829-400">Route matching information.</span></span> |
| <span data-ttu-id="0d829-401">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="0d829-401">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="0d829-402">Risposte di avvio, arresto e keep-alive per la connessione.</span><span class="sxs-lookup"><span data-stu-id="0d829-402">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="0d829-403">Informazioni sui certificati HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0d829-403">HTTPS certificate information.</span></span> |
| <span data-ttu-id="0d829-404">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="0d829-404">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="0d829-405">File forniti.</span><span class="sxs-lookup"><span data-stu-id="0d829-405">Files served.</span></span> |
| <span data-ttu-id="0d829-406">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="0d829-406">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="0d829-407">Diagnostica generale di Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="0d829-407">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="0d829-408">Attività e configurazione del database, rilevamento delle modifiche, migrazioni.</span><span class="sxs-lookup"><span data-stu-id="0d829-408">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="0d829-409">Ambiti dei log</span><span class="sxs-lookup"><span data-stu-id="0d829-409">Log scopes</span></span>

 <span data-ttu-id="0d829-410">Un *ambito* può raggruppare un set di operazioni logiche.</span><span class="sxs-lookup"><span data-stu-id="0d829-410">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="0d829-411">Questo raggruppamento può essere usato per collegare gli stessi dati a ogni log creato come parte di un set.</span><span class="sxs-lookup"><span data-stu-id="0d829-411">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="0d829-412">Ad esempio, ogni log creato come parte dell'elaborazione di una transazione può includere l'ID transazione.</span><span class="sxs-lookup"><span data-stu-id="0d829-412">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="0d829-413">Un ambito è un tipo `IDisposable` restituito dal metodo <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> e viene mantenuto fino all'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="0d829-413">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="0d829-414">Un ambito si usa mediante il wrapping delle chiamate del logger in un blocco `using`:</span><span class="sxs-lookup"><span data-stu-id="0d829-414">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="0d829-415">Il codice seguente abilita gli ambiti per il provider Console:</span><span class="sxs-lookup"><span data-stu-id="0d829-415">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="0d829-416">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0d829-416">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="0d829-417">La configurazione dell'opzione del logger della console `IncludeScopes` è necessaria per abilitare la registrazione basata sull'ambito.</span><span class="sxs-lookup"><span data-stu-id="0d829-417">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="0d829-418">Per informazioni sulla configurazione, vedere la sezione [Configurazione](#configuration).</span><span class="sxs-lookup"><span data-stu-id="0d829-418">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="0d829-419">Ogni messaggio di registrazione include le informazioni sull'ambito:</span><span class="sxs-lookup"><span data-stu-id="0d829-419">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="0d829-420">Provider di registrazione predefiniti</span><span class="sxs-lookup"><span data-stu-id="0d829-420">Built-in logging providers</span></span>

<span data-ttu-id="0d829-421">ASP.NET Core include i provider seguenti:</span><span class="sxs-lookup"><span data-stu-id="0d829-421">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="0d829-422">Console</span><span class="sxs-lookup"><span data-stu-id="0d829-422">Console</span></span>](#console-provider)
* [<span data-ttu-id="0d829-423">Debug</span><span class="sxs-lookup"><span data-stu-id="0d829-423">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="0d829-424">EventSource</span><span class="sxs-lookup"><span data-stu-id="0d829-424">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="0d829-425">EventLog</span><span class="sxs-lookup"><span data-stu-id="0d829-425">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="0d829-426">TraceSource</span><span class="sxs-lookup"><span data-stu-id="0d829-426">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="0d829-427">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="0d829-427">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="0d829-428">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="0d829-428">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="0d829-429">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="0d829-429">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="0d829-430">Per informazioni su StdOut e la registrazione debug con il modulo ASP.NET Core, vedere <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="0d829-430">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="0d829-431">Provider Console</span><span class="sxs-lookup"><span data-stu-id="0d829-431">Console provider</span></span>

<span data-ttu-id="0d829-432">Il pacchetto del provider [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) invia l'output della registrazione alla console.</span><span class="sxs-lookup"><span data-stu-id="0d829-432">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="0d829-433">Per visualizzare l'output di registrazione del provider Console, aprire un prompt dei comandi nella cartella del progetto ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="0d829-433">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="0d829-434">Provider Debug</span><span class="sxs-lookup"><span data-stu-id="0d829-434">Debug provider</span></span>

<span data-ttu-id="0d829-435">Il pacchetto del provider [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) scrive l'output della registrazione usando la classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (chiamate del metodo `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="0d829-435">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="0d829-436">In Linux, questo provider scrive i log in */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="0d829-436">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="0d829-437">Provider origine evento</span><span class="sxs-lookup"><span data-stu-id="0d829-437">Event Source provider</span></span>

<span data-ttu-id="0d829-438">Il pacchetto del provider [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) scrive in un'origine evento multipiattaforma con il nome `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="0d829-438">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="0d829-439">In Windows, il provider utilizza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="0d829-439">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="0d829-440">Il provider di origine eventi viene aggiunto automaticamente `CreateDefaultBuilder` quando viene chiamato per compilare l'host.</span><span class="sxs-lookup"><span data-stu-id="0d829-440">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="0d829-441">strumenti di traccia DotNet</span><span class="sxs-lookup"><span data-stu-id="0d829-441">dotnet trace tooling</span></span>

<span data-ttu-id="0d829-442">Lo strumento [DotNet-Trace](/dotnet/core/diagnostics/dotnet-trace) è uno strumento globale dell'interfaccia della riga di comando multipiattaforma che consente la raccolta di tracce .NET Core di un processo in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="0d829-442">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="0d829-443">Lo strumento raccoglie i <xref:Microsoft.Extensions.Logging.EventSource> dati del provider utilizzando <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>un.</span><span class="sxs-lookup"><span data-stu-id="0d829-443">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="0d829-444">Installare gli strumenti di traccia DotNet con il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="0d829-444">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="0d829-445">Usare gli strumenti di traccia DotNet per raccogliere una traccia da un'app:</span><span class="sxs-lookup"><span data-stu-id="0d829-445">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="0d829-446">Se l'app non compila l'host con `CreateDefaultBuilder`, aggiungere il [provider di origine eventi](#event-source-provider) alla configurazione di registrazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-446">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="0d829-447">Eseguire l'app con il `dotnet run` comando.</span><span class="sxs-lookup"><span data-stu-id="0d829-447">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="0d829-448">Determinare l'identificatore di processo (PID) dell'app .NET Core:</span><span class="sxs-lookup"><span data-stu-id="0d829-448">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="0d829-449">In Windows usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="0d829-449">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="0d829-450">Gestione attività (CTRL + ALT + CANC)</span><span class="sxs-lookup"><span data-stu-id="0d829-450">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="0d829-451">comando tasklist</span><span class="sxs-lookup"><span data-stu-id="0d829-451">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="0d829-452">Comando PowerShell Get-Process</span><span class="sxs-lookup"><span data-stu-id="0d829-452">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="0d829-453">In Linux usare il [comando pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="0d829-453">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="0d829-454">Trovare il PID per il processo con lo stesso nome dell'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-454">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="0d829-455">Eseguire il `dotnet trace` comando.</span><span class="sxs-lookup"><span data-stu-id="0d829-455">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="0d829-456">Sintassi generale del comando:</span><span class="sxs-lookup"><span data-stu-id="0d829-456">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="0d829-457">Quando si usa una shell dei comandi di PowerShell, `--providers` racchiudere il valore tra`'`virgolette singole ():</span><span class="sxs-lookup"><span data-stu-id="0d829-457">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="0d829-458">Nelle piattaforme non Windows aggiungere l' `-f speedscope` opzione per modificare il formato del file di traccia di output in. `speedscope`</span><span class="sxs-lookup"><span data-stu-id="0d829-458">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="0d829-459">Parola chiave</span><span class="sxs-lookup"><span data-stu-id="0d829-459">Keyword</span></span> | <span data-ttu-id="0d829-460">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0d829-460">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="0d829-461">1</span><span class="sxs-lookup"><span data-stu-id="0d829-461">1</span></span>       | <span data-ttu-id="0d829-462">Registra i `LoggingEventSource`metadati relativi a.</span><span class="sxs-lookup"><span data-stu-id="0d829-462">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="0d829-463">Non registra gli eventi `ILogger`da).</span><span class="sxs-lookup"><span data-stu-id="0d829-463">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="0d829-464">2</span><span class="sxs-lookup"><span data-stu-id="0d829-464">2</span></span>       | <span data-ttu-id="0d829-465">Attiva l'evento `Message` quando `ILogger.Log()` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="0d829-465">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="0d829-466">Fornisce informazioni in un metodo programmatico (non formattato).</span><span class="sxs-lookup"><span data-stu-id="0d829-466">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="0d829-467">4</span><span class="sxs-lookup"><span data-stu-id="0d829-467">4</span></span>       | <span data-ttu-id="0d829-468">Attiva l'evento `FormatMessage` quando `ILogger.Log()` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="0d829-468">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="0d829-469">Fornisce la versione di stringa formattata delle informazioni.</span><span class="sxs-lookup"><span data-stu-id="0d829-469">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="0d829-470">8</span><span class="sxs-lookup"><span data-stu-id="0d829-470">8</span></span>       | <span data-ttu-id="0d829-471">Attiva l'evento `MessageJson` quando `ILogger.Log()` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="0d829-471">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="0d829-472">Fornisce una rappresentazione JSON degli argomenti.</span><span class="sxs-lookup"><span data-stu-id="0d829-472">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="0d829-473">Livello dell'evento</span><span class="sxs-lookup"><span data-stu-id="0d829-473">Event Level</span></span> | <span data-ttu-id="0d829-474">Description</span><span class="sxs-lookup"><span data-stu-id="0d829-474">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="0d829-475">0</span><span class="sxs-lookup"><span data-stu-id="0d829-475">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="0d829-476">1</span><span class="sxs-lookup"><span data-stu-id="0d829-476">1</span></span>           | `Critical`      |
   | <span data-ttu-id="0d829-477">2</span><span class="sxs-lookup"><span data-stu-id="0d829-477">2</span></span>           | `Error`         |
   | <span data-ttu-id="0d829-478">3</span><span class="sxs-lookup"><span data-stu-id="0d829-478">3</span></span>           | `Warning`       |
   | <span data-ttu-id="0d829-479">4</span><span class="sxs-lookup"><span data-stu-id="0d829-479">4</span></span>           | `Informational` |
   | <span data-ttu-id="0d829-480">5</span><span class="sxs-lookup"><span data-stu-id="0d829-480">5</span></span>           | `Verbose`       |

   <span data-ttu-id="0d829-481">`FilterSpecs`le voci `{Logger Category}` per `{Event Level}` e rappresentano altre condizioni di filtro del log.</span><span class="sxs-lookup"><span data-stu-id="0d829-481">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="0d829-482">Separare `FilterSpecs` le voci con un punto`;`e virgola ().</span><span class="sxs-lookup"><span data-stu-id="0d829-482">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="0d829-483">Esempio di utilizzo di una shell dei**no** comandi di Windows (senza `--providers` virgolette singole intorno al valore):</span><span class="sxs-lookup"><span data-stu-id="0d829-483">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="0d829-484">Il comando precedente attiva:</span><span class="sxs-lookup"><span data-stu-id="0d829-484">The preceding command activates:</span></span>

   * <span data-ttu-id="0d829-485">Il logger dell'origine evento per produrre stringhe formattate (`4`) per`2`gli errori ().</span><span class="sxs-lookup"><span data-stu-id="0d829-485">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="0d829-486">`Microsoft.AspNetCore.Hosting`registrazione a livello `Informational` di registrazione (`4`).</span><span class="sxs-lookup"><span data-stu-id="0d829-486">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="0d829-487">Arrestare gli strumenti di traccia DotNet premendo il tasto invio o CTRL + C.</span><span class="sxs-lookup"><span data-stu-id="0d829-487">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="0d829-488">La traccia viene salvata con il nome *Trace. NetTrace* nella cartella in cui `dotnet trace` viene eseguito il comando.</span><span class="sxs-lookup"><span data-stu-id="0d829-488">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="0d829-489">Aprire la traccia con [PerfView](#perfview).</span><span class="sxs-lookup"><span data-stu-id="0d829-489">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="0d829-490">Aprire il file *Trace. NetTrace* ed esplorare gli eventi di traccia.</span><span class="sxs-lookup"><span data-stu-id="0d829-490">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="0d829-491">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="0d829-491">For more information, see:</span></span>

* <span data-ttu-id="0d829-492">[Trace for Performance Analysis Utility (DotNet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (documentazione di .NET Core)</span><span class="sxs-lookup"><span data-stu-id="0d829-492">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="0d829-493">[Trace for Performance Analysis Utility (DotNet-Trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (documentazione del repository GitHub DotNet/Diagnostics)</span><span class="sxs-lookup"><span data-stu-id="0d829-493">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="0d829-494">[Classe LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (browser API .NET)</span><span class="sxs-lookup"><span data-stu-id="0d829-494">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="0d829-495">[Origine riferimento LoggingEventSource (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; per ottenere un'origine riferimento per una versione diversa, modificare il ramo `release/{Version}`in, `{Version}` dove è la versione di ASP.NET Core desiderata.</span><span class="sxs-lookup"><span data-stu-id="0d829-495">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="0d829-496">[PerfView](#perfview) &ndash; utile per la visualizzazione delle tracce dell'origine evento.</span><span class="sxs-lookup"><span data-stu-id="0d829-496">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="0d829-497">PerfView</span><span class="sxs-lookup"><span data-stu-id="0d829-497">Perfview</span></span>

<span data-ttu-id="0d829-498">Usare l' [utilità PerfView](https://github.com/Microsoft/perfview) per raccogliere e visualizzare i log.</span><span class="sxs-lookup"><span data-stu-id="0d829-498">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="0d829-499">Sono disponibili altri strumenti per la visualizzazione dei log ETW, ma PerfView fornisce un'esperienza ottimale per l'uso con gli eventi ETW generati da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0d829-499">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="0d829-500">Per configurare PerfView per la raccolta degli eventi registrati da questo provider, aggiungere la stringa `*Microsoft-Extensions-Logging` nell'elenco **Provider aggiuntivi**</span><span class="sxs-lookup"><span data-stu-id="0d829-500">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="0d829-501">(non dimenticare l'asterisco all'inizio della stringa).</span><span class="sxs-lookup"><span data-stu-id="0d829-501">(Don't miss the asterisk at the start of the string.)</span></span>

![Provider aggiuntivi di PerfView](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="0d829-503">Provider EventLog di Windows</span><span class="sxs-lookup"><span data-stu-id="0d829-503">Windows EventLog provider</span></span>

<span data-ttu-id="0d829-504">Il pacchetto di provider [Microsoft.Extensions.Logging.AventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) invia l'output del log al registro eventi di Windows.</span><span class="sxs-lookup"><span data-stu-id="0d829-504">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="0d829-505">Gli [overload di AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) consentono di passare <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="0d829-505">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="0d829-506">Se `null` o non è specificato, vengono usate le impostazioni predefinite seguenti:</span><span class="sxs-lookup"><span data-stu-id="0d829-506">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="0d829-507">`LogName`&ndash; "Applicazione"</span><span class="sxs-lookup"><span data-stu-id="0d829-507">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="0d829-508">`SourceName`&ndash; "Runtime .NET"</span><span class="sxs-lookup"><span data-stu-id="0d829-508">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="0d829-509">`MachineName` &ndash; computer locale</span><span class="sxs-lookup"><span data-stu-id="0d829-509">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="0d829-510">Gli eventi vengono registrati per un [livello di avviso e un livello superiore](#log-level).</span><span class="sxs-lookup"><span data-stu-id="0d829-510">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="0d829-511">Per registrare eventi inferiori a `Warning`, impostare in modo esplicito il livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="0d829-511">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="0d829-512">Aggiungere ad esempio il codice seguente al file *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="0d829-512">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="0d829-513">Provider TraceSource</span><span class="sxs-lookup"><span data-stu-id="0d829-513">TraceSource provider</span></span>

<span data-ttu-id="0d829-514">Il pacchetto di provider [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa le librerie e i provider <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="0d829-514">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="0d829-515">Gli [overload di AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) consentono di passare un cambio di origine e un listener di traccia.</span><span class="sxs-lookup"><span data-stu-id="0d829-515">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="0d829-516">Per usare questo provider, un'app deve essere in esecuzione in .NET Framework (invece che in .NET Core).</span><span class="sxs-lookup"><span data-stu-id="0d829-516">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="0d829-517">Il provider può instradare i messaggi a diversi [listener](/dotnet/framework/debug-trace-profile/trace-listeners), come <xref:System.Diagnostics.TextWriterTraceListener>, usato nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="0d829-517">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="0d829-518">Provider del Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="0d829-518">Azure App Service provider</span></span>

<span data-ttu-id="0d829-519">Il pacchetto di provider [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) scrive i log in file di testo nel file system di un'app del Servizio app di Azure e nell'[archivio di BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in un account di archiviazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="0d829-519">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="0d829-520">Il pacchetto del provider non è incluso nel framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="0d829-520">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="0d829-521">Per usare il provider, aggiungere il relativo pacchetto al progetto.</span><span class="sxs-lookup"><span data-stu-id="0d829-521">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="0d829-522">Per configurare le impostazioni del provider, usare <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> e <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="0d829-522">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="0d829-523">Quando si distribuisce un'app del servizio app, l'applicazione applica le impostazioni della sezione [Log del servizio app](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) della pagina **Servizio app** del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="0d829-523">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="0d829-524">Quando le impostazioni seguenti vengono aggiornate, le modifiche hanno effetto immediatamente senza richiedere un riavvio o la ridistribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-524">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="0d829-525">**Registrazione applicazioni (file system)**</span><span class="sxs-lookup"><span data-stu-id="0d829-525">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="0d829-526">**Registrazione applicazione (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="0d829-526">**Application Logging (Blob)**</span></span>

<span data-ttu-id="0d829-527">Il percorso predefinito per i file di log è nella cartella *D:\\home\\LogFiles\\Application* e il nome di file predefinito è *diagnostics-aaaammgg.txt*.</span><span class="sxs-lookup"><span data-stu-id="0d829-527">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="0d829-528">Il limite predefinito per le dimensioni del file è 10 MB e il numero massimo predefinito di file conservati è 2.</span><span class="sxs-lookup"><span data-stu-id="0d829-528">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="0d829-529">Il nome BLOB predefinito è *{nome-app}{timestamp}/aaaa/mm/gg/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="0d829-529">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="0d829-530">Il provider funziona solo quando il progetto viene eseguito nell'ambiente di Azure.</span><span class="sxs-lookup"><span data-stu-id="0d829-530">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="0d829-531">Non ha alcun effetto quando il progetto viene eseguito localmente, in quanto non scrive nulla in file locali o in archivi di sviluppo locali per i BLOB.</span><span class="sxs-lookup"><span data-stu-id="0d829-531">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="0d829-532">Flusso di registrazione di Azure</span><span class="sxs-lookup"><span data-stu-id="0d829-532">Azure log streaming</span></span>

<span data-ttu-id="0d829-533">Il flusso di registrazione di Azure consente di visualizzare l'attività di registrazione in tempo reale da:</span><span class="sxs-lookup"><span data-stu-id="0d829-533">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="0d829-534">Server applicazioni</span><span class="sxs-lookup"><span data-stu-id="0d829-534">The app server</span></span>
* <span data-ttu-id="0d829-535">Server Web</span><span class="sxs-lookup"><span data-stu-id="0d829-535">The web server</span></span>
* <span data-ttu-id="0d829-536">Traccia delle richieste non riuscite</span><span class="sxs-lookup"><span data-stu-id="0d829-536">Failed request tracing</span></span>

<span data-ttu-id="0d829-537">Per configurare il flusso di registrazione di Azure:</span><span class="sxs-lookup"><span data-stu-id="0d829-537">To configure Azure log streaming:</span></span>

* <span data-ttu-id="0d829-538">Passare alla pagina **Log del servizio app** dalla pagina del portale dell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-538">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="0d829-539">Impostare **Registrazione applicazioni (file system)** su **Attiva**.</span><span class="sxs-lookup"><span data-stu-id="0d829-539">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="0d829-540">Scegliere il livello di registrazione in **Livello**.</span><span class="sxs-lookup"><span data-stu-id="0d829-540">Choose the log **Level**.</span></span> <span data-ttu-id="0d829-541">Questa impostazione è valida solo per lo streaming dei log di Azure, non per altri provider di registrazione nell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-541">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="0d829-542">Passare alla pagina **Flusso di registrazione** per visualizzare i messaggi dell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-542">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="0d829-543">I messaggi vengono registrati dall'app tramite l'interfaccia `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="0d829-543">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="0d829-544">Registrazione di traccia di Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="0d829-544">Azure Application Insights trace logging</span></span>

<span data-ttu-id="0d829-545">Il pacchetto di provider [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) scrive log in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="0d829-545">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="0d829-546">Application Insights è un servizio che monitora un'app Web e fornisce gli strumenti per l'esecuzione di query sui dati di telemetria e la loro analisi.</span><span class="sxs-lookup"><span data-stu-id="0d829-546">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="0d829-547">Se si usa questo provider, è possibile eseguire query sui log e analizzarli usando gli strumenti di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="0d829-547">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="0d829-548">Il provider di registrazione è incluso come dipendenza di [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), ovvero il pacchetto che fornisce tutti i dati di telemetria disponibili per ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0d829-548">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="0d829-549">Se si usa questo pacchetto, non è necessario installare il pacchetto di provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-549">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="0d829-550">Non usare il pacchetto [](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)Microsoft.ApplicationInsights.Web&mdash;, che è per ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="0d829-550">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="0d829-551">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="0d829-551">For more information, see the following resources:</span></span>

* [<span data-ttu-id="0d829-552">Panoramica di Application Insights</span><span class="sxs-lookup"><span data-stu-id="0d829-552">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="0d829-553">[Application Insights per applicazioni ASP.NET Core](/azure/azure-monitor/app/asp-net-core): iniziare da qui se si vuole implementare l'intervallo completo dei dati di telemetria di Application Insights insieme alla registrazione.</span><span class="sxs-lookup"><span data-stu-id="0d829-553">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="0d829-554">[ApplicationInsightsLoggerProvider per log ILogger .NET Core](/azure/azure-monitor/app/ilogger): iniziare da qui se si vuole implementare il provider di registrazione senza gli altri dati di telemetria di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="0d829-554">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="0d829-555">[Adattatori di registrazione di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="0d829-555">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="0d829-556">[Installare, configurare e inizializzare Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): esercitazione interattiva nel sito Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="0d829-556">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="0d829-557">Provider di registrazione di terze parti</span><span class="sxs-lookup"><span data-stu-id="0d829-557">Third-party logging providers</span></span>

<span data-ttu-id="0d829-558">Framework di registrazione di terze parti che usano ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0d829-558">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="0d829-559">[elmah.io](https://elmah.io/) ([repository GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="0d829-559">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="0d829-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repository GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="0d829-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="0d829-561">[JSNLog](https://jsnlog.com/) ([repository GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="0d829-561">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="0d829-562">[KissLog.net](https://kisslog.net/) ([repository GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="0d829-562">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="0d829-563">[Log4net](https://logging.apache.org/log4net/) ([repository GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="0d829-563">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="0d829-564">[Loggr](https://loggr.net/) ([repository GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="0d829-564">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="0d829-565">[NLog](https://nlog-project.org/) ([repository GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="0d829-565">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="0d829-566">[Sentry](https://sentry.io/welcome/) ([repository GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="0d829-566">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="0d829-567">[Serilog](https://serilog.net/) ([repository GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="0d829-567">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="0d829-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repository Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="0d829-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="0d829-569">Alcuni framework di terze parti possono eseguire la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="0d829-569">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="0d829-570">L'uso di un framework di terze parti è simile a quello di uno dei provider predefiniti:</span><span class="sxs-lookup"><span data-stu-id="0d829-570">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="0d829-571">Aggiungere un pacchetto NuGet al progetto.</span><span class="sxs-lookup"><span data-stu-id="0d829-571">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="0d829-572">Chiamare un `ILoggerFactory` metodo di estensione fornito dal framework di registrazione.</span><span class="sxs-lookup"><span data-stu-id="0d829-572">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="0d829-573">Per altre informazioni, vedere la documentazione di ogni provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-573">For more information, see each provider's documentation.</span></span> <span data-ttu-id="0d829-574">I provider di registrazione di terze parti non sono supportati da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="0d829-574">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0d829-575">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0d829-575">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0d829-576">Di [Tom Dykstra](https://github.com/tdykstra) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="0d829-576">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="0d829-577">.NET Core supporta un'API di registrazione che funziona con un'ampia gamma di provider di registrazione predefiniti e di terze parti.</span><span class="sxs-lookup"><span data-stu-id="0d829-577">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="0d829-578">Questo articolo illustra come usare l'API di registrazione con i provider predefiniti.</span><span class="sxs-lookup"><span data-stu-id="0d829-578">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="0d829-579">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0d829-579">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="0d829-580">Aggiungere provider</span><span class="sxs-lookup"><span data-stu-id="0d829-580">Add providers</span></span>

<span data-ttu-id="0d829-581">Un provider di registrazione visualizza o archivia i log.</span><span class="sxs-lookup"><span data-stu-id="0d829-581">A logging provider displays or stores logs.</span></span> <span data-ttu-id="0d829-582">Il provider Console, ad esempio, visualizza i log nella console, mentre il provider Azure Application Insights li archivia in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="0d829-582">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="0d829-583">I log possono essere inviati a più destinazioni aggiungendo più provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-583">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="0d829-584">Per aggiungere un provider, chiamare il metodo di estensione `Add{provider name}` del provider in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0d829-584">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="0d829-585">Il codice precedente richiede riferimenti a `Microsoft.Extensions.Logging` e `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="0d829-585">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="0d829-586">Il modello di progetto predefinito chiama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, che aggiunge i provider di registrazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="0d829-586">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="0d829-587">Console</span><span class="sxs-lookup"><span data-stu-id="0d829-587">Console</span></span>
* <span data-ttu-id="0d829-588">Debug</span><span class="sxs-lookup"><span data-stu-id="0d829-588">Debug</span></span>
* <span data-ttu-id="0d829-589">EventSource (a partire da ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="0d829-589">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="0d829-590">Se si usa `CreateDefaultBuilder`, è possibile sostituire i provider predefiniti con quelli di propria scelta.</span><span class="sxs-lookup"><span data-stu-id="0d829-590">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="0d829-591">Chiamare <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e aggiungere i provider desiderati.</span><span class="sxs-lookup"><span data-stu-id="0d829-591">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="0d829-592">Altre informazioni sui [provider di registrazione predefiniti](#built-in-logging-providers) e sui [provider di registrazione di terze parti](#third-party-logging-providers) vengono fornite più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="0d829-592">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="0d829-593">Creare log</span><span class="sxs-lookup"><span data-stu-id="0d829-593">Create logs</span></span>

<span data-ttu-id="0d829-594">Per creare i log, usare un oggetto <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="0d829-594">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="0d829-595">In un'app Web o in un servizio ospitato, ottenere un oggetto `ILogger` da un inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="0d829-595">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="0d829-596">Nelle app console non host, usare `LoggerFactory` per creare un oggetto `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="0d829-596">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="0d829-597">L'esempio seguente di ASP.NET Core crea un logger con `TodoApiSample.Pages.AboutModel` come categoria.</span><span class="sxs-lookup"><span data-stu-id="0d829-597">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="0d829-598">La *categoria* del log è una stringa associata a ogni log.</span><span class="sxs-lookup"><span data-stu-id="0d829-598">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="0d829-599">L'istanza di `ILogger<T>` fornita dall'inserimento delle dipendenze crea log con nome completo di tipo `T` come categoria.</span><span class="sxs-lookup"><span data-stu-id="0d829-599">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="0d829-600">Negli esempi seguenti di ASP.NET Core e app console, il logger viene usato per creare log con `Information` come livello.</span><span class="sxs-lookup"><span data-stu-id="0d829-600">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="0d829-601">Il *livello* del log indica la gravità dell'evento registrato.</span><span class="sxs-lookup"><span data-stu-id="0d829-601">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="0d829-602">[Livelli](#log-level) e [categorie](#log-category) vengono illustrati in modo dettagliato più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="0d829-602">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="0d829-603">Creare log nella classe Startup</span><span class="sxs-lookup"><span data-stu-id="0d829-603">Create logs in Startup</span></span>

<span data-ttu-id="0d829-604">Per scrivere log nella classe `Startup`, includere un parametro `ILogger` nella firma del costruttore:</span><span class="sxs-lookup"><span data-stu-id="0d829-604">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="0d829-605">Creare log nella classe Program</span><span class="sxs-lookup"><span data-stu-id="0d829-605">Create logs in the Program class</span></span>

<span data-ttu-id="0d829-606">Per scrivere log nella classe `Program`, ottenere un'istanza di `ILogger` dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="0d829-606">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="0d829-607">La registrazione durante la costruzione dell'host non è supportata direttamente.</span><span class="sxs-lookup"><span data-stu-id="0d829-607">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="0d829-608">Tuttavia, è possibile usare un logger separato.</span><span class="sxs-lookup"><span data-stu-id="0d829-608">However, a separate logger can be used.</span></span> <span data-ttu-id="0d829-609">Nell'esempio seguente viene usato un logger [Serilog](https://serilog.net/) per eseguire l'accesso `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="0d829-609">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="0d829-610">`AddSerilog`Usa la configurazione statica specificata in `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="0d829-610">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="0d829-611">Evitare l'uso di metodi logger asincroni</span><span class="sxs-lookup"><span data-stu-id="0d829-611">No asynchronous logger methods</span></span>

<span data-ttu-id="0d829-612">La registrazione deve essere così rapida da non giustificare l'impatto sulle prestazioni del codice asincrono.</span><span class="sxs-lookup"><span data-stu-id="0d829-612">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="0d829-613">Se l'archivio dati di registrazione è lento, non scrivere direttamente al suo interno.</span><span class="sxs-lookup"><span data-stu-id="0d829-613">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="0d829-614">Scrivere invece i messaggi di log prima in un archivio veloce e quindi spostarli nell'archivio lento in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="0d829-614">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="0d829-615">Ad esempio, se la registrazione viene eseguita in SQL Server, è preferibile non farlo direttamente in un metodo `Log`, poiché i metodi `Log` sono sincroni.</span><span class="sxs-lookup"><span data-stu-id="0d829-615">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="0d829-616">Al contrario, aggiungere i messaggi di log in modo sincrono a una coda in memoria e usare un ruolo di lavoro in background per eseguire il pull dei messaggi dalla coda per eseguire le operazioni asincrone di push dei dati in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="0d829-616">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="0d829-617">Per altre informazioni, vedere [questo](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problema di GitHub.</span><span class="sxs-lookup"><span data-stu-id="0d829-617">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="0d829-618">Configurazione</span><span class="sxs-lookup"><span data-stu-id="0d829-618">Configuration</span></span>

<span data-ttu-id="0d829-619">La configurazione dei provider di registrazione viene fornita da uno o più provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="0d829-619">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="0d829-620">Formati di file (INI, JSON e XML).</span><span class="sxs-lookup"><span data-stu-id="0d829-620">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="0d829-621">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="0d829-621">Command-line arguments.</span></span>
* <span data-ttu-id="0d829-622">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="0d829-622">Environment variables.</span></span>
* <span data-ttu-id="0d829-623">Oggetti .NET in memoria.</span><span class="sxs-lookup"><span data-stu-id="0d829-623">In-memory .NET objects.</span></span>
* <span data-ttu-id="0d829-624">Archiviazione con [Secret Manager](xref:security/app-secrets) senza crittografia.</span><span class="sxs-lookup"><span data-stu-id="0d829-624">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="0d829-625">Un archivio utente non crittografato, come [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="0d829-625">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="0d829-626">Provider personalizzati (installati o creati).</span><span class="sxs-lookup"><span data-stu-id="0d829-626">Custom providers (installed or created).</span></span>

<span data-ttu-id="0d829-627">Ad esempio, la configurazione di registrazione viene comunemente fornita dalla sezione `Logging` del file di impostazioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-627">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="0d829-628">L'esempio seguente mostra il contenuto di un file *appsettings.Development.json* tipico:</span><span class="sxs-lookup"><span data-stu-id="0d829-628">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="0d829-629">La proprietà `Logging` può avere le proprietà `LogLevel` e quella del provider di log (nell'esempio, Console).</span><span class="sxs-lookup"><span data-stu-id="0d829-629">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="0d829-630">La proprietà `LogLevel` in `Logging` specifica il [livello](#log-level) minimo per la registrazione per determinate categorie.</span><span class="sxs-lookup"><span data-stu-id="0d829-630">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="0d829-631">Nell'esempio, le categorie `System` e `Microsoft` eseguono la registrazione al livello `Information`, mentre tutte le altre la eseguono al livello `Debug`.</span><span class="sxs-lookup"><span data-stu-id="0d829-631">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="0d829-632">Altre proprietà in `Logging` specificano i provider di registrazione.</span><span class="sxs-lookup"><span data-stu-id="0d829-632">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="0d829-633">L'esempio si riferisce al provider Console.</span><span class="sxs-lookup"><span data-stu-id="0d829-633">The example is for the Console provider.</span></span> <span data-ttu-id="0d829-634">Se un provider supporta gli [ambiti](#log-scopes)del `IncludeScopes` log, indica se sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="0d829-634">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="0d829-635">Una proprietà del provider (ad esempio `Console` nell'esempio) può specificare anche una proprietà `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="0d829-635">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="0d829-636">`LogLevel` in un provider specifica i livelli di registrazione per tale provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-636">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="0d829-637">Se i livelli sono specificati in `Logging.{providername}.LogLevel`, eseguono l'override di eventuali valori impostati in `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="0d829-637">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="0d829-638">L'API di registrazione non include uno scenario per modificare i livelli di registrazione mentre un'app è in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="0d829-638">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="0d829-639">Alcuni provider di configurazione, tuttavia, sono in grado di ricaricare la configurazione, operazione che ha effetto immediato sulla configurazione della registrazione.</span><span class="sxs-lookup"><span data-stu-id="0d829-639">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="0d829-640">Ad esempio, il [provider di configurazione file](xref:fundamentals/configuration/index#file-configuration-provider), aggiunto da `CreateDefaultBuilder` per leggere i file di impostazioni, ricarica la configurazione di registrazione per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="0d829-640">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="0d829-641">Se la configurazione viene modificata nel codice mentre un'app è in esecuzione, l'app può chiamare [IConfigurationRoot. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) per aggiornare la configurazione di registrazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-641">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="0d829-642">Per informazioni sull'implementazione dei provider di configurazione, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="0d829-642">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="0d829-643">Esempio di output di registrazione</span><span class="sxs-lookup"><span data-stu-id="0d829-643">Sample logging output</span></span>

<span data-ttu-id="0d829-644">Con il codice di esempio illustrato nella sezione precedente i log vengono visualizzati nella console quando l'app viene eseguita dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="0d829-644">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="0d829-645">Ecco un esempio di output della console:</span><span class="sxs-lookup"><span data-stu-id="0d829-645">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

<span data-ttu-id="0d829-646">I log precedenti sono stati generati inviando una richiesta HTTP Get all'app di esempio all'indirizzo `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="0d829-646">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="0d829-647">Ecco un esempio degli stessi log visualizzati nella finestra Debug quando si esegue l'app di esempio in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="0d829-647">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="0d829-648">I log creati dalle chiamate a `ILogger` illustrate nella sezione precedente iniziano con "TodoApi".</span><span class="sxs-lookup"><span data-stu-id="0d829-648">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="0d829-649">I log che iniziano con le categorie "Microsoft" provengono dal codice del framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0d829-649">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="0d829-650">ASP.NET Core e il codice dell'applicazione usano la stessa API di registrazione e gli stessi provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-650">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="0d829-651">Il resto di questo articolo spiega alcuni dettagli e le opzioni per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="0d829-651">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="0d829-652">Pacchetti NuGet</span><span class="sxs-lookup"><span data-stu-id="0d829-652">NuGet packages</span></span>

<span data-ttu-id="0d829-653">Le interfacce `ILogger` e `ILoggerFactory` si trovano in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) e le loro implementazioni predefinite si trovano in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="0d829-653">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="0d829-654">Categoria di log</span><span class="sxs-lookup"><span data-stu-id="0d829-654">Log category</span></span>

<span data-ttu-id="0d829-655">Quando viene creato un oggetto `ILogger`, viene specificata la relativa *categoria*.</span><span class="sxs-lookup"><span data-stu-id="0d829-655">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="0d829-656">La categoria è inclusa in ogni messaggio di log creato da tale istanza di `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="0d829-656">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="0d829-657">La categoria può essere qualsiasi stringa, ma per convenzione si usa il nome della classe, ad esempio "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="0d829-657">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="0d829-658">Usare `ILogger<T>` per ottenere un'istanza di `ILogger` che usa il nome completo di tipo `T` come categoria:</span><span class="sxs-lookup"><span data-stu-id="0d829-658">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="0d829-659">Per specificare in modo esplicito la categoria, chiamare `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="0d829-659">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="0d829-660">L'uso di `ILogger<T>` equivale a chiamare `CreateLogger` con il nome completo di tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="0d829-660">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="0d829-661">Livello di registrazione</span><span class="sxs-lookup"><span data-stu-id="0d829-661">Log level</span></span>

<span data-ttu-id="0d829-662">Ogni log specifica un valore <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="0d829-662">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="0d829-663">Il livello di registrazione indica la gravità o l'importanza.</span><span class="sxs-lookup"><span data-stu-id="0d829-663">The log level indicates the severity or importance.</span></span> <span data-ttu-id="0d829-664">È ad esempio possibile scrivere un log `Information` quando un metodo termina normalmente e un log `Warning` quando un metodo restituisce un codice di stato *404 Non trovato*.</span><span class="sxs-lookup"><span data-stu-id="0d829-664">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="0d829-665">Il codice seguente crea i log `Information` e `Warning`:</span><span class="sxs-lookup"><span data-stu-id="0d829-665">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="0d829-666">Nel codice precedente il primo parametro è l'[ID dell'evento di log](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="0d829-666">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="0d829-667">Il secondo parametro è un modello di messaggio con segnaposto per i valori degli argomenti forniti dai parametri dei metodi rimanenti.</span><span class="sxs-lookup"><span data-stu-id="0d829-667">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="0d829-668">I parametri dei metodi sono descritti nella [sezione relativa al modello di messaggio](#log-message-template) più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="0d829-668">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="0d829-669">I metodi di registrazione che includono il livello nel nome del metodo (ad esempio `LogInformation` e `LogWarning`) sono [metodi di estensione per ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="0d829-669">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="0d829-670">Questi metodi chiamano un metodo `Log` che accetta un parametro `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="0d829-670">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="0d829-671">È possibile chiamare il metodo `Log` direttamente anziché chiamare uno di questi metodi di estensione, ma la sintassi è relativamente complessa.</span><span class="sxs-lookup"><span data-stu-id="0d829-671">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="0d829-672">Per altre informazioni, vedere <xref:Microsoft.Extensions.Logging.ILogger> e il [codice sorgente delle estensioni del logger](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="0d829-672">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="0d829-673">ASP.NET Core definisce i livelli di registrazione seguenti, ordinati dal meno grave al più grave.</span><span class="sxs-lookup"><span data-stu-id="0d829-673">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="0d829-674">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="0d829-674">Trace = 0</span></span>

  <span data-ttu-id="0d829-675">Per informazioni in genere utili solo per il debug.</span><span class="sxs-lookup"><span data-stu-id="0d829-675">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="0d829-676">Questi messaggi possono contenere dati sensibili dell'applicazione e pertanto non devono essere abilitati in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="0d829-676">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="0d829-677">*Disattivato per impostazione predefinita.*</span><span class="sxs-lookup"><span data-stu-id="0d829-677">*Disabled by default.*</span></span>

* <span data-ttu-id="0d829-678">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="0d829-678">Debug = 1</span></span>

  <span data-ttu-id="0d829-679">Per informazioni che possono essere utili per lo sviluppo e il debug.</span><span class="sxs-lookup"><span data-stu-id="0d829-679">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="0d829-680">Esempio: `Entering method Configure with flag set to true.` Abilitare i livelli di registrazione `Debug` nell'ambiente di produzione solo in fase di risoluzione dei problemi, per non fare aumentare eccessivamente il volume dei log.</span><span class="sxs-lookup"><span data-stu-id="0d829-680">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="0d829-681">Information = 2</span><span class="sxs-lookup"><span data-stu-id="0d829-681">Information = 2</span></span>

  <span data-ttu-id="0d829-682">Per tenere traccia del flusso generale dell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-682">For tracking the general flow of the app.</span></span> <span data-ttu-id="0d829-683">Queste registrazioni hanno in genere un valore a lungo termine.</span><span class="sxs-lookup"><span data-stu-id="0d829-683">These logs typically have some long-term value.</span></span> <span data-ttu-id="0d829-684">Esempio: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="0d829-684">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="0d829-685">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="0d829-685">Warning = 3</span></span>

  <span data-ttu-id="0d829-686">Per gli eventi imprevisti o anomali nel flusso dell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-686">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="0d829-687">Tali eventi potrebbero includere errori o altre condizioni che non provocano l'arresto dell'app, ma che potrebbe essere necessario analizzare.</span><span class="sxs-lookup"><span data-stu-id="0d829-687">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="0d829-688">Le eccezioni gestite sono una situazione comune in cui usare il livello di registrazione `Warning`.</span><span class="sxs-lookup"><span data-stu-id="0d829-688">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="0d829-689">Esempio: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="0d829-689">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="0d829-690">Error = 4</span><span class="sxs-lookup"><span data-stu-id="0d829-690">Error = 4</span></span>

  <span data-ttu-id="0d829-691">Per errori ed eccezioni che non possono essere gestiti.</span><span class="sxs-lookup"><span data-stu-id="0d829-691">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="0d829-692">Questi messaggi indicano un errore nell'operazione o nell'attività in corso (ad esempio la richiesta HTTP corrente), non un errore a livello di app.</span><span class="sxs-lookup"><span data-stu-id="0d829-692">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="0d829-693">Messaggio di registrazione di esempio:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="0d829-693">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="0d829-694">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="0d829-694">Critical = 5</span></span>

  <span data-ttu-id="0d829-695">Per gli errori che richiedono attenzione immediata.</span><span class="sxs-lookup"><span data-stu-id="0d829-695">For failures that require immediate attention.</span></span> <span data-ttu-id="0d829-696">Esempi: scenari di perdita di dati, spazio su disco insufficiente.</span><span class="sxs-lookup"><span data-stu-id="0d829-696">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="0d829-697">Usare il livello di registrazione per controllare la quantità di output di log scritto in un supporto di archiviazione specifico o in una finestra.</span><span class="sxs-lookup"><span data-stu-id="0d829-697">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="0d829-698">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0d829-698">For example:</span></span>

* <span data-ttu-id="0d829-699">In produzione:</span><span class="sxs-lookup"><span data-stu-id="0d829-699">In production:</span></span>
  * <span data-ttu-id="0d829-700">La `Trace` registrazione ai livelli `Information` attraverso produce un volume elevato di messaggi di log dettagliati.</span><span class="sxs-lookup"><span data-stu-id="0d829-700">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="0d829-701">Per controllare i costi e non superare i limiti di archiviazione `Trace` dei `Information` dati, accedere ai messaggi di livello elevato a un archivio dati a basso costo.</span><span class="sxs-lookup"><span data-stu-id="0d829-701">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="0d829-702">L'accesso `Warning` ai `Critical` livelli in genere produce un minor numero di messaggi di log più piccoli.</span><span class="sxs-lookup"><span data-stu-id="0d829-702">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="0d829-703">Pertanto, i costi e i limiti di archiviazione in genere non rappresentano un problema, il che comporta una maggiore flessibilità di scelta dell'archivio dati.</span><span class="sxs-lookup"><span data-stu-id="0d829-703">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="0d829-704">Durante lo sviluppo:</span><span class="sxs-lookup"><span data-stu-id="0d829-704">During development:</span></span>
  * <span data-ttu-id="0d829-705">Consente `Warning` di `Critical` accedere ai messaggi nella console.</span><span class="sxs-lookup"><span data-stu-id="0d829-705">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="0d829-706">Aggiungere `Trace` `Information` messaggi durante la risoluzione dei problemi.</span><span class="sxs-lookup"><span data-stu-id="0d829-706">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="0d829-707">La sezione [Filtro dei log](#log-filtering) più avanti in questo articolo descrive come controllare quali livelli di registrazione gestisce un provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-707">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="0d829-708">ASP.NET Core scrive log per gli eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="0d829-708">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="0d829-709">Gli esempi di log illustrati in precedenza in questo articolo escludono i log al di sotto del livello `Information`, quindi non vengono creati log di livello `Debug` o `Trace`.</span><span class="sxs-lookup"><span data-stu-id="0d829-709">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="0d829-710">Ecco un esempio di log della console prodotti eseguendo l'app di esempio configurata in modo da mostrare i log di livello `Debug`:</span><span class="sxs-lookup"><span data-stu-id="0d829-710">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="0d829-711">ID evento di registrazione</span><span class="sxs-lookup"><span data-stu-id="0d829-711">Log event ID</span></span>

<span data-ttu-id="0d829-712">Ogni log può specificare un *ID evento*.</span><span class="sxs-lookup"><span data-stu-id="0d829-712">Each log can specify an *event ID*.</span></span> <span data-ttu-id="0d829-713">A tale scopo, l'app di esempio usa una classe `LoggingEvents` definita a livello locale:</span><span class="sxs-lookup"><span data-stu-id="0d829-713">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="0d829-714">Un ID evento associa un set di eventi.</span><span class="sxs-lookup"><span data-stu-id="0d829-714">An event ID associates a set of events.</span></span> <span data-ttu-id="0d829-715">Ad esempio, tutti i log correlati alla visualizzazione di un elenco di elementi in una pagina potrebbero essere indicati con 1001.</span><span class="sxs-lookup"><span data-stu-id="0d829-715">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="0d829-716">Il provider di log può archiviare l'ID evento in un campo ID o nel messaggio di registrazione oppure non archiviarlo.</span><span class="sxs-lookup"><span data-stu-id="0d829-716">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="0d829-717">Il provider Debug non visualizza gli ID evento.</span><span class="sxs-lookup"><span data-stu-id="0d829-717">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="0d829-718">Il provider Console visualizza gli ID evento tra parentesi quadre dopo la categoria:</span><span class="sxs-lookup"><span data-stu-id="0d829-718">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="0d829-719">Modello di messaggio di registrazione</span><span class="sxs-lookup"><span data-stu-id="0d829-719">Log message template</span></span>

<span data-ttu-id="0d829-720">Ogni log specifica un modello di messaggio.</span><span class="sxs-lookup"><span data-stu-id="0d829-720">Each log specifies a message template.</span></span> <span data-ttu-id="0d829-721">Il modello di messaggio può contenere segnaposto per i quali vengono forniti argomenti.</span><span class="sxs-lookup"><span data-stu-id="0d829-721">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="0d829-722">Usare nomi per i segnaposto, non numeri.</span><span class="sxs-lookup"><span data-stu-id="0d829-722">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="0d829-723">L'ordine dei segnaposto, non il loro nome, determina i parametri da usare per fornire i valori corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="0d829-723">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="0d829-724">Nel codice seguente, si noti che i nomi dei parametri non sono in sequenza nel modello di messaggio:</span><span class="sxs-lookup"><span data-stu-id="0d829-724">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="0d829-725">Questo codice crea un messaggio di log con i valori dei parametri in sequenza:</span><span class="sxs-lookup"><span data-stu-id="0d829-725">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="0d829-726">Il framework di registrazione funziona in questo modo per consentire ai provider di registrazione di implementare la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="0d829-726">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="0d829-727">Al sistema di registrazione vengono passati gli argomenti e non solo il modello di messaggio formattato.</span><span class="sxs-lookup"><span data-stu-id="0d829-727">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="0d829-728">Queste informazioni consentono ai provider di registrazione di archiviare i valori dei parametri come campi.</span><span class="sxs-lookup"><span data-stu-id="0d829-728">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="0d829-729">Si supponga, ad esempio, che il metodo logger esegua chiamate simili alla seguente:</span><span class="sxs-lookup"><span data-stu-id="0d829-729">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="0d829-730">Se si inviano i log all'archiviazione tabelle di Azure, ogni entità tabella di Azure può avere le proprietà `ID` e `RequestTime`, il che semplifica le query sui dati dei log.</span><span class="sxs-lookup"><span data-stu-id="0d829-730">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="0d829-731">Una query può trovare tutti i log entro un determinato intervallo `RequestTime` senza che sia necessario analizzare il messaggio di testo per determinare l'intervallo di tempo.</span><span class="sxs-lookup"><span data-stu-id="0d829-731">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="0d829-732">Registrazione delle eccezioni</span><span class="sxs-lookup"><span data-stu-id="0d829-732">Logging exceptions</span></span>

<span data-ttu-id="0d829-733">I metodi logger dispongono di overload che consentono di passare un'eccezione, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="0d829-733">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="0d829-734">Provider diversi gestiscono le informazioni sulle eccezioni in modi diversi.</span><span class="sxs-lookup"><span data-stu-id="0d829-734">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="0d829-735">Ecco un esempio di output del provider Debug dal codice sopra riportato.</span><span class="sxs-lookup"><span data-stu-id="0d829-735">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="0d829-736">Filtro del log</span><span class="sxs-lookup"><span data-stu-id="0d829-736">Log filtering</span></span>

<span data-ttu-id="0d829-737">È possibile specificare un livello di registrazione minimo per un provider e una categoria specifici oppure per tutti i provider o tutte le categorie.</span><span class="sxs-lookup"><span data-stu-id="0d829-737">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="0d829-738">Tutti i log sotto il livello minimo non sono passati al provider, quindi non vengono visualizzati o archiviati.</span><span class="sxs-lookup"><span data-stu-id="0d829-738">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="0d829-739">Per eliminare tutti i log, specificare `LogLevel.None` come livello di registrazione minimo.</span><span class="sxs-lookup"><span data-stu-id="0d829-739">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="0d829-740">Il valore intero di `LogLevel.None` è 6, maggiore di `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="0d829-740">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="0d829-741">Creare regole di filtro nella configurazione</span><span class="sxs-lookup"><span data-stu-id="0d829-741">Create filter rules in configuration</span></span>

<span data-ttu-id="0d829-742">Il codice del modello di `CreateDefaultBuilder` progetto chiama per impostare la registrazione per i provider console, debug e EventSource (ASP.NET Core 2,2 o versione successiva).</span><span class="sxs-lookup"><span data-stu-id="0d829-742">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="0d829-743">Il metodo `CreateDefaultBuilder` imposta inoltre la registrazione per la ricerca della configurazione in una sezione `Logging`, come illustrato [in precedenza in questo articolo](#configuration).</span><span class="sxs-lookup"><span data-stu-id="0d829-743">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="0d829-744">I dati di configurazione specificano i livelli di registrazione minimi in base al provider e alla categoria, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="0d829-744">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="0d829-745">Questo codice JSON crea sei regole di filtro, una per il provider Debug, quattro per il provider Console e una per tutti i provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-745">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="0d829-746">Quando viene creato un oggetto `ILogger`, viene scelta una singola regola per ogni provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-746">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="0d829-747">Regole di filtro nel codice</span><span class="sxs-lookup"><span data-stu-id="0d829-747">Filter rules in code</span></span>

<span data-ttu-id="0d829-748">L'esempio seguente illustra come registrare le regole di filtro nel codice:</span><span class="sxs-lookup"><span data-stu-id="0d829-748">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="0d829-749">Il secondo `AddFilter` specifica il provider Debug tramite il nome del tipo.</span><span class="sxs-lookup"><span data-stu-id="0d829-749">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="0d829-750">Il primo `AddFilter` si applica a tutti i provider in quanto non consente di specificare un tipo di provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-750">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="0d829-751">Applicazione delle regole di filtro</span><span class="sxs-lookup"><span data-stu-id="0d829-751">How filtering rules are applied</span></span>

<span data-ttu-id="0d829-752">I dati di configurazione e il codice `AddFilter` illustrato negli esempi precedenti creano le regole indicate nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="0d829-752">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="0d829-753">I primi sei provengono dall'esempio di configurazione e gli ultimi due dall'esempio di codice.</span><span class="sxs-lookup"><span data-stu-id="0d829-753">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="0d829-754">Number</span><span class="sxs-lookup"><span data-stu-id="0d829-754">Number</span></span> | <span data-ttu-id="0d829-755">Provider</span><span class="sxs-lookup"><span data-stu-id="0d829-755">Provider</span></span>      | <span data-ttu-id="0d829-756">Categorie che iniziano con...</span><span class="sxs-lookup"><span data-stu-id="0d829-756">Categories that begin with ...</span></span>          | <span data-ttu-id="0d829-757">Livello di registrazione minimo</span><span class="sxs-lookup"><span data-stu-id="0d829-757">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="0d829-758">1</span><span class="sxs-lookup"><span data-stu-id="0d829-758">1</span></span>      | <span data-ttu-id="0d829-759">Debug</span><span class="sxs-lookup"><span data-stu-id="0d829-759">Debug</span></span>         | <span data-ttu-id="0d829-760">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="0d829-760">All categories</span></span>                          | <span data-ttu-id="0d829-761">Informazioni</span><span class="sxs-lookup"><span data-stu-id="0d829-761">Information</span></span>       |
| <span data-ttu-id="0d829-762">2</span><span class="sxs-lookup"><span data-stu-id="0d829-762">2</span></span>      | <span data-ttu-id="0d829-763">Console</span><span class="sxs-lookup"><span data-stu-id="0d829-763">Console</span></span>       | <span data-ttu-id="0d829-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="0d829-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="0d829-765">Avviso</span><span class="sxs-lookup"><span data-stu-id="0d829-765">Warning</span></span>           |
| <span data-ttu-id="0d829-766">3</span><span class="sxs-lookup"><span data-stu-id="0d829-766">3</span></span>      | <span data-ttu-id="0d829-767">Console</span><span class="sxs-lookup"><span data-stu-id="0d829-767">Console</span></span>       | <span data-ttu-id="0d829-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="0d829-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="0d829-769">Debug</span><span class="sxs-lookup"><span data-stu-id="0d829-769">Debug</span></span>             |
| <span data-ttu-id="0d829-770">4</span><span class="sxs-lookup"><span data-stu-id="0d829-770">4</span></span>      | <span data-ttu-id="0d829-771">Console</span><span class="sxs-lookup"><span data-stu-id="0d829-771">Console</span></span>       | <span data-ttu-id="0d829-772">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="0d829-772">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="0d829-773">Errore</span><span class="sxs-lookup"><span data-stu-id="0d829-773">Error</span></span>             |
| <span data-ttu-id="0d829-774">5</span><span class="sxs-lookup"><span data-stu-id="0d829-774">5</span></span>      | <span data-ttu-id="0d829-775">Console</span><span class="sxs-lookup"><span data-stu-id="0d829-775">Console</span></span>       | <span data-ttu-id="0d829-776">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="0d829-776">All categories</span></span>                          | <span data-ttu-id="0d829-777">Informazioni</span><span class="sxs-lookup"><span data-stu-id="0d829-777">Information</span></span>       |
| <span data-ttu-id="0d829-778">6</span><span class="sxs-lookup"><span data-stu-id="0d829-778">6</span></span>      | <span data-ttu-id="0d829-779">Tutti i provider</span><span class="sxs-lookup"><span data-stu-id="0d829-779">All providers</span></span> | <span data-ttu-id="0d829-780">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="0d829-780">All categories</span></span>                          | <span data-ttu-id="0d829-781">Debug</span><span class="sxs-lookup"><span data-stu-id="0d829-781">Debug</span></span>             |
| <span data-ttu-id="0d829-782">7</span><span class="sxs-lookup"><span data-stu-id="0d829-782">7</span></span>      | <span data-ttu-id="0d829-783">Tutti i provider</span><span class="sxs-lookup"><span data-stu-id="0d829-783">All providers</span></span> | <span data-ttu-id="0d829-784">Sistema</span><span class="sxs-lookup"><span data-stu-id="0d829-784">System</span></span>                                  | <span data-ttu-id="0d829-785">Debug</span><span class="sxs-lookup"><span data-stu-id="0d829-785">Debug</span></span>             |
| <span data-ttu-id="0d829-786">8</span><span class="sxs-lookup"><span data-stu-id="0d829-786">8</span></span>      | <span data-ttu-id="0d829-787">Debug</span><span class="sxs-lookup"><span data-stu-id="0d829-787">Debug</span></span>         | <span data-ttu-id="0d829-788">Microsoft</span><span class="sxs-lookup"><span data-stu-id="0d829-788">Microsoft</span></span>                               | <span data-ttu-id="0d829-789">Trace</span><span class="sxs-lookup"><span data-stu-id="0d829-789">Trace</span></span>             |

<span data-ttu-id="0d829-790">Quando viene creato un oggetto `ILogger`, l'oggetto `ILoggerFactory` seleziona una singola regola per ogni provider da applicare al logger.</span><span class="sxs-lookup"><span data-stu-id="0d829-790">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="0d829-791">Tutti i messaggi scritti da un'istanza di `ILogger` vengono filtrati in base alle regole selezionate.</span><span class="sxs-lookup"><span data-stu-id="0d829-791">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="0d829-792">Tra le regole disponibili viene selezionata la regola più specifica possibile per ogni coppia di categoria e provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-792">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="0d829-793">L'algoritmo seguente viene usato per ogni provider quando viene creato un `ILogger` per una determinata categoria:</span><span class="sxs-lookup"><span data-stu-id="0d829-793">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="0d829-794">Selezionare tutte le regole corrispondenti al provider o al relativo alias.</span><span class="sxs-lookup"><span data-stu-id="0d829-794">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="0d829-795">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole con un provider vuoto.</span><span class="sxs-lookup"><span data-stu-id="0d829-795">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="0d829-796">Dal risultato del passaggio precedente, selezionare le regole con il prefisso di categoria corrispondente più lungo.</span><span class="sxs-lookup"><span data-stu-id="0d829-796">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="0d829-797">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole che non specificano una categoria.</span><span class="sxs-lookup"><span data-stu-id="0d829-797">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="0d829-798">Se sono selezionate più regole, scegliere l'**ultima**.</span><span class="sxs-lookup"><span data-stu-id="0d829-798">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="0d829-799">Se non è selezionata alcuna regola, usare `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="0d829-799">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="0d829-800">Con l'elenco di regole precedente, si supponga di creare un oggetto `ILogger` per la categoria "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="0d829-800">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="0d829-801">Per il provider Debug si applicano le regole 1, 6 e 8.</span><span class="sxs-lookup"><span data-stu-id="0d829-801">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="0d829-802">La regola 8 è più specifica, così sarà quella selezionata.</span><span class="sxs-lookup"><span data-stu-id="0d829-802">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="0d829-803">Per il provider Console si applicano le regole 3, 4, 5 e 6.</span><span class="sxs-lookup"><span data-stu-id="0d829-803">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="0d829-804">La regola 3 è la più specifica.</span><span class="sxs-lookup"><span data-stu-id="0d829-804">Rule 3 is most specific.</span></span>

<span data-ttu-id="0d829-805">L'istanza di `ILogger` risultante invia i log di livello `Trace` o superiore al provider Debug.</span><span class="sxs-lookup"><span data-stu-id="0d829-805">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="0d829-806">I log di livello `Debug` o superiore vengono inviati al provider Console.</span><span class="sxs-lookup"><span data-stu-id="0d829-806">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="0d829-807">Alias dei provider</span><span class="sxs-lookup"><span data-stu-id="0d829-807">Provider aliases</span></span>

<span data-ttu-id="0d829-808">Ogni provider definisce un *alias* che può essere utilizzato nella configurazione al posto del nome completo di tipo.</span><span class="sxs-lookup"><span data-stu-id="0d829-808">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="0d829-809">Per i provider predefiniti, usare gli alias seguenti:</span><span class="sxs-lookup"><span data-stu-id="0d829-809">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="0d829-810">Console</span><span class="sxs-lookup"><span data-stu-id="0d829-810">Console</span></span>
* <span data-ttu-id="0d829-811">Debug</span><span class="sxs-lookup"><span data-stu-id="0d829-811">Debug</span></span>
* <span data-ttu-id="0d829-812">EventSource</span><span class="sxs-lookup"><span data-stu-id="0d829-812">EventSource</span></span>
* <span data-ttu-id="0d829-813">EventLog</span><span class="sxs-lookup"><span data-stu-id="0d829-813">EventLog</span></span>
* <span data-ttu-id="0d829-814">TraceSource</span><span class="sxs-lookup"><span data-stu-id="0d829-814">TraceSource</span></span>
* <span data-ttu-id="0d829-815">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="0d829-815">AzureAppServicesFile</span></span>
* <span data-ttu-id="0d829-816">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="0d829-816">AzureAppServicesBlob</span></span>
* <span data-ttu-id="0d829-817">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="0d829-817">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="0d829-818">Livello minimo predefinito</span><span class="sxs-lookup"><span data-stu-id="0d829-818">Default minimum level</span></span>

<span data-ttu-id="0d829-819">Esiste un'impostazione del livello minimo che diventa effettiva solo se non si applica alcuna regola di configurazione o codice per un provider e una categoria specifici.</span><span class="sxs-lookup"><span data-stu-id="0d829-819">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="0d829-820">L'esempio seguente illustra come impostare il livello minimo:</span><span class="sxs-lookup"><span data-stu-id="0d829-820">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="0d829-821">Se non si imposta in modo esplicito il livello minimo, il valore predefinito è `Information`, che indica che i log `Trace` e `Debug` vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="0d829-821">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="0d829-822">Funzioni di filtro</span><span class="sxs-lookup"><span data-stu-id="0d829-822">Filter functions</span></span>

<span data-ttu-id="0d829-823">Una funzione di filtro viene richiamata per tutti i provider e le categorie a cui non sono assegnate regole tramite la configurazione o il codice.</span><span class="sxs-lookup"><span data-stu-id="0d829-823">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="0d829-824">Il codice della funzione ha accesso al tipo di provider, alla categoria e al livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="0d829-824">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="0d829-825">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0d829-825">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="0d829-826">Livelli e categorie di sistema</span><span class="sxs-lookup"><span data-stu-id="0d829-826">System categories and levels</span></span>

<span data-ttu-id="0d829-827">Di seguito sono elencate alcune categorie usate da ASP.NET Core ed Entity Framework Core, con note sui log previsti per ognuna:</span><span class="sxs-lookup"><span data-stu-id="0d829-827">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="0d829-828">Category</span><span class="sxs-lookup"><span data-stu-id="0d829-828">Category</span></span>                            | <span data-ttu-id="0d829-829">Note</span><span class="sxs-lookup"><span data-stu-id="0d829-829">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="0d829-830">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="0d829-830">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="0d829-831">Diagnostica generale di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0d829-831">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="0d829-832">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="0d829-832">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="0d829-833">Chiavi considerate, trovate e usate.</span><span class="sxs-lookup"><span data-stu-id="0d829-833">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="0d829-834">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="0d829-834">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="0d829-835">Host consentiti.</span><span class="sxs-lookup"><span data-stu-id="0d829-835">Hosts allowed.</span></span> |
| <span data-ttu-id="0d829-836">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="0d829-836">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="0d829-837">Tempo impiegato per il completamento delle richieste HTTP e ora di inizio delle richieste.</span><span class="sxs-lookup"><span data-stu-id="0d829-837">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="0d829-838">Assembly di avvio dell'hosting caricati.</span><span class="sxs-lookup"><span data-stu-id="0d829-838">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="0d829-839">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="0d829-839">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="0d829-840">MVC e Razor diagnostica.</span><span class="sxs-lookup"><span data-stu-id="0d829-840">MVC and Razor diagnostics.</span></span> <span data-ttu-id="0d829-841">Associazione di modelli, esecuzione di filtri, compilazione di viste, selezione di azioni.</span><span class="sxs-lookup"><span data-stu-id="0d829-841">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="0d829-842">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="0d829-842">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="0d829-843">Informazioni sulla corrispondenza di route.</span><span class="sxs-lookup"><span data-stu-id="0d829-843">Route matching information.</span></span> |
| <span data-ttu-id="0d829-844">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="0d829-844">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="0d829-845">Risposte di avvio, arresto e keep-alive per la connessione.</span><span class="sxs-lookup"><span data-stu-id="0d829-845">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="0d829-846">Informazioni sui certificati HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0d829-846">HTTPS certificate information.</span></span> |
| <span data-ttu-id="0d829-847">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="0d829-847">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="0d829-848">File forniti.</span><span class="sxs-lookup"><span data-stu-id="0d829-848">Files served.</span></span> |
| <span data-ttu-id="0d829-849">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="0d829-849">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="0d829-850">Diagnostica generale di Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="0d829-850">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="0d829-851">Attività e configurazione del database, rilevamento delle modifiche, migrazioni.</span><span class="sxs-lookup"><span data-stu-id="0d829-851">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="0d829-852">Ambiti dei log</span><span class="sxs-lookup"><span data-stu-id="0d829-852">Log scopes</span></span>

 <span data-ttu-id="0d829-853">Un *ambito* può raggruppare un set di operazioni logiche.</span><span class="sxs-lookup"><span data-stu-id="0d829-853">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="0d829-854">Questo raggruppamento può essere usato per collegare gli stessi dati a ogni log creato come parte di un set.</span><span class="sxs-lookup"><span data-stu-id="0d829-854">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="0d829-855">Ad esempio, ogni log creato come parte dell'elaborazione di una transazione può includere l'ID transazione.</span><span class="sxs-lookup"><span data-stu-id="0d829-855">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="0d829-856">Un ambito è un tipo `IDisposable` restituito dal metodo <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> e viene mantenuto fino all'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="0d829-856">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="0d829-857">Un ambito si usa mediante il wrapping delle chiamate del logger in un blocco `using`:</span><span class="sxs-lookup"><span data-stu-id="0d829-857">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="0d829-858">Il codice seguente abilita gli ambiti per il provider Console:</span><span class="sxs-lookup"><span data-stu-id="0d829-858">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="0d829-859">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0d829-859">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="0d829-860">La configurazione dell'opzione del logger della console `IncludeScopes` è necessaria per abilitare la registrazione basata sull'ambito.</span><span class="sxs-lookup"><span data-stu-id="0d829-860">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="0d829-861">Per informazioni sulla configurazione, vedere la sezione [Configurazione](#configuration).</span><span class="sxs-lookup"><span data-stu-id="0d829-861">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="0d829-862">Ogni messaggio di registrazione include le informazioni sull'ambito:</span><span class="sxs-lookup"><span data-stu-id="0d829-862">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="0d829-863">Provider di registrazione predefiniti</span><span class="sxs-lookup"><span data-stu-id="0d829-863">Built-in logging providers</span></span>

<span data-ttu-id="0d829-864">ASP.NET Core include i provider seguenti:</span><span class="sxs-lookup"><span data-stu-id="0d829-864">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="0d829-865">Console</span><span class="sxs-lookup"><span data-stu-id="0d829-865">Console</span></span>](#console-provider)
* [<span data-ttu-id="0d829-866">Debug</span><span class="sxs-lookup"><span data-stu-id="0d829-866">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="0d829-867">EventSource</span><span class="sxs-lookup"><span data-stu-id="0d829-867">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="0d829-868">EventLog</span><span class="sxs-lookup"><span data-stu-id="0d829-868">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="0d829-869">TraceSource</span><span class="sxs-lookup"><span data-stu-id="0d829-869">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="0d829-870">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="0d829-870">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="0d829-871">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="0d829-871">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="0d829-872">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="0d829-872">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="0d829-873">Per informazioni su StdOut e la registrazione debug con il modulo ASP.NET Core, vedere <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="0d829-873">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="0d829-874">Provider Console</span><span class="sxs-lookup"><span data-stu-id="0d829-874">Console provider</span></span>

<span data-ttu-id="0d829-875">Il pacchetto del provider [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) invia l'output della registrazione alla console.</span><span class="sxs-lookup"><span data-stu-id="0d829-875">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="0d829-876">Per visualizzare l'output di registrazione del provider Console, aprire un prompt dei comandi nella cartella del progetto ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="0d829-876">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="0d829-877">Provider Debug</span><span class="sxs-lookup"><span data-stu-id="0d829-877">Debug provider</span></span>

<span data-ttu-id="0d829-878">Il pacchetto del provider [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) scrive l'output della registrazione usando la classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (chiamate del metodo `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="0d829-878">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="0d829-879">In Linux, questo provider scrive i log in */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="0d829-879">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="0d829-880">Provider origine evento</span><span class="sxs-lookup"><span data-stu-id="0d829-880">Event Source provider</span></span>

<span data-ttu-id="0d829-881">Il pacchetto del provider [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) scrive in un'origine evento multipiattaforma con il nome `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="0d829-881">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="0d829-882">In Windows, il provider utilizza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="0d829-882">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="0d829-883">Il provider di origine eventi viene aggiunto automaticamente `CreateDefaultBuilder` quando viene chiamato per compilare l'host.</span><span class="sxs-lookup"><span data-stu-id="0d829-883">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="0d829-884">Usare l' [utilità PerfView](https://github.com/Microsoft/perfview) per raccogliere e visualizzare i log.</span><span class="sxs-lookup"><span data-stu-id="0d829-884">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="0d829-885">Sono disponibili altri strumenti per la visualizzazione dei log ETW, ma PerfView fornisce un'esperienza ottimale per l'uso con gli eventi ETW generati da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0d829-885">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="0d829-886">Per configurare PerfView per la raccolta degli eventi registrati da questo provider, aggiungere la stringa `*Microsoft-Extensions-Logging` nell'elenco **Provider aggiuntivi**</span><span class="sxs-lookup"><span data-stu-id="0d829-886">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="0d829-887">(non dimenticare l'asterisco all'inizio della stringa).</span><span class="sxs-lookup"><span data-stu-id="0d829-887">(Don't miss the asterisk at the start of the string.)</span></span>

![Provider aggiuntivi di PerfView](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="0d829-889">Provider EventLog di Windows</span><span class="sxs-lookup"><span data-stu-id="0d829-889">Windows EventLog provider</span></span>

<span data-ttu-id="0d829-890">Il pacchetto di provider [Microsoft.Extensions.Logging.AventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) invia l'output del log al registro eventi di Windows.</span><span class="sxs-lookup"><span data-stu-id="0d829-890">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="0d829-891">Gli [overload di AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) consentono di passare <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="0d829-891">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="0d829-892">Se `null` o non è specificato, vengono usate le impostazioni predefinite seguenti:</span><span class="sxs-lookup"><span data-stu-id="0d829-892">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="0d829-893">`LogName`&ndash; "Applicazione"</span><span class="sxs-lookup"><span data-stu-id="0d829-893">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="0d829-894">`SourceName`&ndash; "Runtime .NET"</span><span class="sxs-lookup"><span data-stu-id="0d829-894">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="0d829-895">`MachineName` &ndash; computer locale</span><span class="sxs-lookup"><span data-stu-id="0d829-895">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="0d829-896">Gli eventi vengono registrati per un [livello di avviso e un livello superiore](#log-level).</span><span class="sxs-lookup"><span data-stu-id="0d829-896">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="0d829-897">Per registrare eventi inferiori a `Warning`, impostare in modo esplicito il livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="0d829-897">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="0d829-898">Aggiungere ad esempio il codice seguente al file *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="0d829-898">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="0d829-899">Provider TraceSource</span><span class="sxs-lookup"><span data-stu-id="0d829-899">TraceSource provider</span></span>

<span data-ttu-id="0d829-900">Il pacchetto di provider [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa le librerie e i provider <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="0d829-900">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="0d829-901">Gli [overload di AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) consentono di passare un cambio di origine e un listener di traccia.</span><span class="sxs-lookup"><span data-stu-id="0d829-901">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="0d829-902">Per usare questo provider, un'app deve essere in esecuzione in .NET Framework (invece che in .NET Core).</span><span class="sxs-lookup"><span data-stu-id="0d829-902">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="0d829-903">Il provider può instradare i messaggi a diversi [listener](/dotnet/framework/debug-trace-profile/trace-listeners), come <xref:System.Diagnostics.TextWriterTraceListener>, usato nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="0d829-903">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="0d829-904">Provider del Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="0d829-904">Azure App Service provider</span></span>

<span data-ttu-id="0d829-905">Il pacchetto di provider [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) scrive i log in file di testo nel file system di un'app del Servizio app di Azure e nell'[archivio di BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in un account di archiviazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="0d829-905">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="0d829-906">Il pacchetto del provider non è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="0d829-906">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="0d829-907">Quando la destinazione è .NET Framework o il riferimento è il metapacchetto `Microsoft.AspNetCore.App`, aggiungere il pacchetto del provider al progetto.</span><span class="sxs-lookup"><span data-stu-id="0d829-907">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="0d829-908">Un overload di <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> consente di passare <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="0d829-908">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="0d829-909">L'oggetto impostazioni può eseguire l'override delle impostazioni predefinite, ad esempio il modello di output di registrazione, il nome di BLOB e il limite di dimensioni di file.</span><span class="sxs-lookup"><span data-stu-id="0d829-909">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="0d829-910">Il *modello di output* è un modello di messaggio che viene applicato a tutti i log in aggiunta a quanto specificato quando si chiama un metodo `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="0d829-910">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="0d829-911">Quando si distribuisce un'app del servizio app, l'applicazione applica le impostazioni della sezione [Log del servizio app](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) della pagina **Servizio app** del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="0d829-911">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="0d829-912">Quando le impostazioni seguenti vengono aggiornate, le modifiche hanno effetto immediatamente senza richiedere un riavvio o la ridistribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-912">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="0d829-913">**Registrazione applicazioni (file system)**</span><span class="sxs-lookup"><span data-stu-id="0d829-913">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="0d829-914">**Registrazione applicazione (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="0d829-914">**Application Logging (Blob)**</span></span>

<span data-ttu-id="0d829-915">Il percorso predefinito per i file di log è nella cartella *D:\\home\\LogFiles\\Application* e il nome di file predefinito è *diagnostics-aaaammgg.txt*.</span><span class="sxs-lookup"><span data-stu-id="0d829-915">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="0d829-916">Il limite predefinito per le dimensioni del file è 10 MB e il numero massimo predefinito di file conservati è 2.</span><span class="sxs-lookup"><span data-stu-id="0d829-916">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="0d829-917">Il nome BLOB predefinito è *{nome-app}{timestamp}/aaaa/mm/gg/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="0d829-917">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="0d829-918">Il provider funziona solo quando il progetto viene eseguito nell'ambiente di Azure.</span><span class="sxs-lookup"><span data-stu-id="0d829-918">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="0d829-919">Non ha alcun effetto quando il progetto viene eseguito localmente, in quanto non scrive nulla in file locali o in archivi di sviluppo locali per i BLOB.</span><span class="sxs-lookup"><span data-stu-id="0d829-919">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="0d829-920">Flusso di registrazione di Azure</span><span class="sxs-lookup"><span data-stu-id="0d829-920">Azure log streaming</span></span>

<span data-ttu-id="0d829-921">Il flusso di registrazione di Azure consente di visualizzare l'attività di registrazione in tempo reale da:</span><span class="sxs-lookup"><span data-stu-id="0d829-921">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="0d829-922">Server applicazioni</span><span class="sxs-lookup"><span data-stu-id="0d829-922">The app server</span></span>
* <span data-ttu-id="0d829-923">Server Web</span><span class="sxs-lookup"><span data-stu-id="0d829-923">The web server</span></span>
* <span data-ttu-id="0d829-924">Traccia delle richieste non riuscite</span><span class="sxs-lookup"><span data-stu-id="0d829-924">Failed request tracing</span></span>

<span data-ttu-id="0d829-925">Per configurare il flusso di registrazione di Azure:</span><span class="sxs-lookup"><span data-stu-id="0d829-925">To configure Azure log streaming:</span></span>

* <span data-ttu-id="0d829-926">Passare alla pagina **Log del servizio app** dalla pagina del portale dell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-926">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="0d829-927">Impostare **Registrazione applicazioni (file system)** su **Attiva**.</span><span class="sxs-lookup"><span data-stu-id="0d829-927">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="0d829-928">Scegliere il livello di registrazione in **Livello**.</span><span class="sxs-lookup"><span data-stu-id="0d829-928">Choose the log **Level**.</span></span> <span data-ttu-id="0d829-929">Questa impostazione è valida solo per lo streaming dei log di Azure, non per altri provider di registrazione nell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-929">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="0d829-930">Passare alla pagina **Flusso di registrazione** per visualizzare i messaggi dell'app.</span><span class="sxs-lookup"><span data-stu-id="0d829-930">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="0d829-931">I messaggi vengono registrati dall'app tramite l'interfaccia `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="0d829-931">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="0d829-932">Registrazione di traccia di Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="0d829-932">Azure Application Insights trace logging</span></span>

<span data-ttu-id="0d829-933">Il pacchetto di provider [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) scrive log in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="0d829-933">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="0d829-934">Application Insights è un servizio che monitora un'app Web e fornisce gli strumenti per l'esecuzione di query sui dati di telemetria e la loro analisi.</span><span class="sxs-lookup"><span data-stu-id="0d829-934">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="0d829-935">Se si usa questo provider, è possibile eseguire query sui log e analizzarli usando gli strumenti di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="0d829-935">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="0d829-936">Il provider di registrazione è incluso come dipendenza di [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), ovvero il pacchetto che fornisce tutti i dati di telemetria disponibili per ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0d829-936">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="0d829-937">Se si usa questo pacchetto, non è necessario installare il pacchetto di provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-937">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="0d829-938">Non usare il pacchetto [](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)Microsoft.ApplicationInsights.Web&mdash;, che è per ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="0d829-938">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="0d829-939">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="0d829-939">For more information, see the following resources:</span></span>

* [<span data-ttu-id="0d829-940">Panoramica di Application Insights</span><span class="sxs-lookup"><span data-stu-id="0d829-940">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="0d829-941">[Application Insights per applicazioni ASP.NET Core](/azure/azure-monitor/app/asp-net-core): iniziare da qui se si vuole implementare l'intervallo completo dei dati di telemetria di Application Insights insieme alla registrazione.</span><span class="sxs-lookup"><span data-stu-id="0d829-941">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="0d829-942">[ApplicationInsightsLoggerProvider per log ILogger .NET Core](/azure/azure-monitor/app/ilogger): iniziare da qui se si vuole implementare il provider di registrazione senza gli altri dati di telemetria di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="0d829-942">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="0d829-943">[Adattatori di registrazione di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="0d829-943">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="0d829-944">[Installare, configurare e inizializzare Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): esercitazione interattiva nel sito Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="0d829-944">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="0d829-945">Provider di registrazione di terze parti</span><span class="sxs-lookup"><span data-stu-id="0d829-945">Third-party logging providers</span></span>

<span data-ttu-id="0d829-946">Framework di registrazione di terze parti che usano ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0d829-946">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="0d829-947">[elmah.io](https://elmah.io/) ([repository GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="0d829-947">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="0d829-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repository GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="0d829-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="0d829-949">[JSNLog](https://jsnlog.com/) ([repository GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="0d829-949">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="0d829-950">[KissLog.net](https://kisslog.net/) ([repository GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="0d829-950">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="0d829-951">[Log4net](https://logging.apache.org/log4net/) ([repository GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="0d829-951">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="0d829-952">[Loggr](https://loggr.net/) ([repository GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="0d829-952">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="0d829-953">[NLog](https://nlog-project.org/) ([repository GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="0d829-953">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="0d829-954">[Sentry](https://sentry.io/welcome/) ([repository GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="0d829-954">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="0d829-955">[Serilog](https://serilog.net/) ([repository GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="0d829-955">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="0d829-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repository Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="0d829-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="0d829-957">Alcuni framework di terze parti possono eseguire la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="0d829-957">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="0d829-958">L'uso di un framework di terze parti è simile a quello di uno dei provider predefiniti:</span><span class="sxs-lookup"><span data-stu-id="0d829-958">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="0d829-959">Aggiungere un pacchetto NuGet al progetto.</span><span class="sxs-lookup"><span data-stu-id="0d829-959">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="0d829-960">Chiamare un `ILoggerFactory` metodo di estensione fornito dal framework di registrazione.</span><span class="sxs-lookup"><span data-stu-id="0d829-960">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="0d829-961">Per altre informazioni, vedere la documentazione di ogni provider.</span><span class="sxs-lookup"><span data-stu-id="0d829-961">For more information, see each provider's documentation.</span></span> <span data-ttu-id="0d829-962">I provider di registrazione di terze parti non sono supportati da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="0d829-962">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0d829-963">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0d829-963">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
