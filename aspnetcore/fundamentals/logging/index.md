---
title: Registrazione in .NET Core e ASP.NET Core
author: rick-anderson
description: Informazioni su come usare il framework di registrazione fornito dal pacchetto NuGet Microsoft.Extensions.Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/23/2020
uid: fundamentals/logging/index
ms.openlocfilehash: 7be8cef3377132ed43efde209db67401d7bdb6dc
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110915"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="185ad-103">Registrazione in .NET Core e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="185ad-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="185ad-104">Di [Tom Dykstra](https://github.com/tdykstra) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="185ad-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="185ad-105">.NET Core supporta un'API di registrazione che funziona con un'ampia gamma di provider di registrazione predefiniti e di terze parti.</span><span class="sxs-lookup"><span data-stu-id="185ad-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="185ad-106">Questo articolo illustra come usare l'API di registrazione con i provider predefiniti.</span><span class="sxs-lookup"><span data-stu-id="185ad-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="185ad-107">La maggior parte degli esempi di codice illustrati in questo articolo proviene da app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="185ad-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="185ad-108">Le parti specifiche per la registrazione di questi frammenti di codice si applicano a qualsiasi app .NET Core che usa l' [host generico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="185ad-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="185ad-109">Per un esempio di come usare l'host generico in un'app console non Web, vedere il file *Program.cs* dell' [app di esempio attività in background](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span><span class="sxs-lookup"><span data-stu-id="185ad-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="185ad-110">Il codice di registrazione per le app senza host generico differisce per il modo in cui vengono [aggiunti i provider](#add-providers) e [creati i logger](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="185ad-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="185ad-111">Gli esempi di codice non host sono illustrati nelle sezioni dell'articolo in cui sono riportate queste procedure.</span><span class="sxs-lookup"><span data-stu-id="185ad-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="185ad-112">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="185ad-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="185ad-113">Aggiungere provider</span><span class="sxs-lookup"><span data-stu-id="185ad-113">Add providers</span></span>

<span data-ttu-id="185ad-114">Un provider di registrazione visualizza o archivia i log.</span><span class="sxs-lookup"><span data-stu-id="185ad-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="185ad-115">Il provider Console, ad esempio, visualizza i log nella console, mentre il provider Azure Application Insights li archivia in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="185ad-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="185ad-116">I log possono essere inviati a più destinazioni aggiungendo più provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="185ad-117">Per aggiungere un provider in un'app che usa l'host generico, chiamare il metodo di estensione `Add{provider name}` del provider in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="185ad-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="185ad-118">In un'app console non host chiamare il metodo di estensione `Add{provider name}` del provider durante la creazione di un oggetto `LoggerFactory`:</span><span class="sxs-lookup"><span data-stu-id="185ad-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="185ad-119">`LoggerFactory` e `AddConsole` richiedono un'istruzione `using` per `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="185ad-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="185ad-120">I modelli di progetto ASP.NET Core predefiniti chiamano <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, che aggiunge i provider di registrazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="185ad-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="185ad-121">Console</span><span class="sxs-lookup"><span data-stu-id="185ad-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="185ad-122">Debug</span><span class="sxs-lookup"><span data-stu-id="185ad-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="185ad-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="185ad-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="185ad-124">[EventLog](#windows-eventlog-provider) (solo in caso di esecuzione in Windows)</span><span class="sxs-lookup"><span data-stu-id="185ad-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="185ad-125">È possibile sostituire i provider predefiniti con quelli di propria scelta.</span><span class="sxs-lookup"><span data-stu-id="185ad-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="185ad-126">Chiamare <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e aggiungere i provider desiderati.</span><span class="sxs-lookup"><span data-stu-id="185ad-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="185ad-127">Altre informazioni sui [provider di registrazione predefiniti](#built-in-logging-providers) e sui [provider di registrazione di terze parti](#third-party-logging-providers) vengono fornite più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="185ad-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="185ad-128">Creare log</span><span class="sxs-lookup"><span data-stu-id="185ad-128">Create logs</span></span>

<span data-ttu-id="185ad-129">Per creare i log, usare un oggetto <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="185ad-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="185ad-130">In un'app Web o in un servizio ospitato, ottenere un oggetto `ILogger` da un inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="185ad-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="185ad-131">Nelle app console non host, usare `LoggerFactory` per creare un oggetto `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="185ad-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="185ad-132">L'esempio seguente di ASP.NET Core crea un logger con `TodoApiSample.Pages.AboutModel` come categoria.</span><span class="sxs-lookup"><span data-stu-id="185ad-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="185ad-133">La *categoria* del log è una stringa associata a ogni log.</span><span class="sxs-lookup"><span data-stu-id="185ad-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="185ad-134">L'istanza di `ILogger<T>` fornita dall'inserimento delle dipendenze crea log con nome completo di tipo `T` come categoria.</span><span class="sxs-lookup"><span data-stu-id="185ad-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="185ad-135">L'esempio seguente di app console non host crea un logger con `LoggingConsoleApp.Program` come categoria.</span><span class="sxs-lookup"><span data-stu-id="185ad-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="185ad-136">Negli esempi seguenti di ASP.NET Core e app console, il logger viene usato per creare log con `Information` come livello.</span><span class="sxs-lookup"><span data-stu-id="185ad-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="185ad-137">Il *livello* del log indica la gravità dell'evento registrato.</span><span class="sxs-lookup"><span data-stu-id="185ad-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="185ad-138">[Livelli](#log-level) e [categorie](#log-category) vengono illustrati in modo dettagliato più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="185ad-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="185ad-139">Creare log nella classe Program</span><span class="sxs-lookup"><span data-stu-id="185ad-139">Create logs in the Program class</span></span>

<span data-ttu-id="185ad-140">Per scrivere log nella classe `Program` di un'app ASP.NET Core, ottenere un'istanza di `ILogger` dall'inserimento delle dipendenze dopo aver creato l'host:</span><span class="sxs-lookup"><span data-stu-id="185ad-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="185ad-141">La registrazione durante la costruzione dell'host non è supportata direttamente.</span><span class="sxs-lookup"><span data-stu-id="185ad-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="185ad-142">Tuttavia, è possibile usare un logger separato.</span><span class="sxs-lookup"><span data-stu-id="185ad-142">However, a separate logger can be used.</span></span> <span data-ttu-id="185ad-143">Nell'esempio seguente viene usato un logger [Serilog](https://serilog.net/) per eseguire l'accesso `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="185ad-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="185ad-144">`AddSerilog`Usa la configurazione statica specificata in `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="185ad-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="185ad-145">Creare log nella classe Startup</span><span class="sxs-lookup"><span data-stu-id="185ad-145">Create logs in the Startup class</span></span>

<span data-ttu-id="185ad-146">Per scrivere log nel metodo `Startup.Configure` di un'app ASP.NET Core, includere un parametro `ILogger` nella firma del metodo:</span><span class="sxs-lookup"><span data-stu-id="185ad-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="185ad-147">La scrittura di log prima del completamento della configurazione del contenitore di inserimento delle dipendenze nel metodo `Startup.ConfigureServices` non è supportata:</span><span class="sxs-lookup"><span data-stu-id="185ad-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="185ad-148">L'inserimento del logger nel costruttore `Startup` non è supportato.</span><span class="sxs-lookup"><span data-stu-id="185ad-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="185ad-149">L'inserimento del logger nella firma del metodo `Startup.ConfigureServices` non è supportato</span><span class="sxs-lookup"><span data-stu-id="185ad-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="185ad-150">Questa restrizione è dovuta al fatto che la registrazione dipende dall'inserimento delle dipendenze e dalla configurazione, che a sua volta dipende dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="185ad-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="185ad-151">Il contenitore di inserimento delle dipendenze non viene configurato finché il metodo `ConfigureServices` non è completato.</span><span class="sxs-lookup"><span data-stu-id="185ad-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="185ad-152">L'inserimento di un logger nel costruttore `Startup` è possibile nelle versioni precedenti di ASP.NET Core perché viene creato un contenitore di inserimento delle dipendenze separato per l'host Web.</span><span class="sxs-lookup"><span data-stu-id="185ad-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="185ad-153">Per informazioni sul motivo per cui viene creato un solo contenitore per l'host generico, vedere l'[annuncio relativo alle modifiche di rilievo](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="185ad-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="185ad-154">Se è necessario configurare un servizio che dipende da `ILogger<T>`, è comunque possibile eseguire questa operazione usando l'inserimento nel costruttore o specificando un metodo factory.</span><span class="sxs-lookup"><span data-stu-id="185ad-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="185ad-155">L'approccio con il metodo factory è consigliato solo se non sono disponibili altre opzioni.</span><span class="sxs-lookup"><span data-stu-id="185ad-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="185ad-156">Si supponga, ad esempio, di dover specificare una proprietà con un servizio ottenuto dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="185ad-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="185ad-157">Il precedente codice evidenziato è un oggetto `Func` che viene eseguito la prima volta che il contenitore di inserimento delle dipendenze deve creare un'istanza di `MyService`.</span><span class="sxs-lookup"><span data-stu-id="185ad-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="185ad-158">È possibile accedere a uno dei servizi registrati in questo modo.</span><span class="sxs-lookup"><span data-stu-id="185ad-158">You can access any of the registered services in this way.</span></span>

### <a name="create-logs-in-blazor"></a><span data-ttu-id="185ad-159">Creare log in blazer</span><span class="sxs-lookup"><span data-stu-id="185ad-159">Create logs in Blazor</span></span>

#### <a name="blazor-webassembly"></a><span data-ttu-id="185ad-160">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="185ad-160">Blazor WebAssembly</span></span>

<span data-ttu-id="185ad-161">Configurare la registrazione nelle app webassembly Blazer con `WebAssemblyHostBuilder.Logging` la proprietà `Program.Main`in:</span><span class="sxs-lookup"><span data-stu-id="185ad-161">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="185ad-162">La `Logging` proprietà è di tipo <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, pertanto tutti i metodi di estensione disponibili in <xref:Microsoft.Extensions.Logging.ILoggingBuilder> sono disponibili anche in `Logging`.</span><span class="sxs-lookup"><span data-stu-id="185ad-162">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

#### <a name="log-in-razor-components"></a><span data-ttu-id="185ad-163">Accedi ai componenti Razor</span><span class="sxs-lookup"><span data-stu-id="185ad-163">Log in Razor components</span></span>

<span data-ttu-id="185ad-164">I logger rispettano la configurazione di avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-164">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="185ad-165">La `using` direttiva per <xref:Microsoft.Extensions.Logging> è necessaria per supportare i completamenti IntelliSense per <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> le API, ad <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>esempio e.</span><span class="sxs-lookup"><span data-stu-id="185ad-165">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="185ad-166">Nell'esempio seguente viene illustrata la <xref:Microsoft.Extensions.Logging.ILogger> registrazione con un nei componenti Razor:</span><span class="sxs-lookup"><span data-stu-id="185ad-166">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

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

<span data-ttu-id="185ad-167">Nell'esempio seguente viene illustrata la <xref:Microsoft.Extensions.Logging.ILoggerFactory> registrazione con un nei componenti Razor:</span><span class="sxs-lookup"><span data-stu-id="185ad-167">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="185ad-168">Evitare l'uso di metodi logger asincroni</span><span class="sxs-lookup"><span data-stu-id="185ad-168">No asynchronous logger methods</span></span>

<span data-ttu-id="185ad-169">La registrazione deve essere così rapida da non giustificare l'impatto sulle prestazioni del codice asincrono.</span><span class="sxs-lookup"><span data-stu-id="185ad-169">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="185ad-170">Se l'archivio dati di registrazione è lento, non scrivere direttamente al suo interno.</span><span class="sxs-lookup"><span data-stu-id="185ad-170">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="185ad-171">Scrivere invece i messaggi di log prima in un archivio veloce e quindi spostarli nell'archivio lento in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="185ad-171">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="185ad-172">Ad esempio, se la registrazione viene eseguita in SQL Server, è preferibile non farlo direttamente in un metodo `Log`, poiché i metodi `Log` sono sincroni.</span><span class="sxs-lookup"><span data-stu-id="185ad-172">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="185ad-173">Al contrario, aggiungere i messaggi di log in modo sincrono a una coda in memoria e usare un ruolo di lavoro in background per eseguire il pull dei messaggi dalla coda per eseguire le operazioni asincrone di push dei dati in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="185ad-173">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="185ad-174">Per altre informazioni, vedere [questo](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problema di GitHub.</span><span class="sxs-lookup"><span data-stu-id="185ad-174">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="185ad-175">Configurazione</span><span class="sxs-lookup"><span data-stu-id="185ad-175">Configuration</span></span>

<span data-ttu-id="185ad-176">La configurazione dei provider di registrazione viene fornita da uno o più provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="185ad-176">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="185ad-177">Formati di file (INI, JSON e XML).</span><span class="sxs-lookup"><span data-stu-id="185ad-177">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="185ad-178">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="185ad-178">Command-line arguments.</span></span>
* <span data-ttu-id="185ad-179">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="185ad-179">Environment variables.</span></span>
* <span data-ttu-id="185ad-180">Oggetti .NET in memoria.</span><span class="sxs-lookup"><span data-stu-id="185ad-180">In-memory .NET objects.</span></span>
* <span data-ttu-id="185ad-181">Archiviazione con [Secret Manager](xref:security/app-secrets) senza crittografia.</span><span class="sxs-lookup"><span data-stu-id="185ad-181">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="185ad-182">Un archivio utente non crittografato, come [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="185ad-182">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="185ad-183">Provider personalizzati (installati o creati).</span><span class="sxs-lookup"><span data-stu-id="185ad-183">Custom providers (installed or created).</span></span>

<span data-ttu-id="185ad-184">Ad esempio, la configurazione di registrazione viene comunemente fornita dalla sezione `Logging` del file di impostazioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-184">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="185ad-185">L'esempio seguente mostra il contenuto di un file *appsettings.Development.json* tipico:</span><span class="sxs-lookup"><span data-stu-id="185ad-185">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="185ad-186">La proprietà `Logging` può avere le proprietà `LogLevel` e quella del provider di log (nell'esempio, Console).</span><span class="sxs-lookup"><span data-stu-id="185ad-186">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="185ad-187">La proprietà `LogLevel` in `Logging` specifica il [livello](#log-level) minimo per la registrazione per determinate categorie.</span><span class="sxs-lookup"><span data-stu-id="185ad-187">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="185ad-188">Nell'esempio, le categorie `System` e `Microsoft` eseguono la registrazione al livello `Information`, mentre tutte le altre la eseguono al livello `Debug`.</span><span class="sxs-lookup"><span data-stu-id="185ad-188">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="185ad-189">Altre proprietà in `Logging` specificano i provider di registrazione.</span><span class="sxs-lookup"><span data-stu-id="185ad-189">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="185ad-190">L'esempio si riferisce al provider Console.</span><span class="sxs-lookup"><span data-stu-id="185ad-190">The example is for the Console provider.</span></span> <span data-ttu-id="185ad-191">Se un provider supporta gli [ambiti](#log-scopes)del `IncludeScopes` log, indica se sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="185ad-191">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="185ad-192">Una proprietà del provider (ad esempio `Console` nell'esempio) può specificare anche una proprietà `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="185ad-192">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="185ad-193">`LogLevel` in un provider specifica i livelli di registrazione per tale provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-193">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="185ad-194">Se i livelli sono specificati in `Logging.{providername}.LogLevel`, eseguono l'override di eventuali valori impostati in `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="185ad-194">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="185ad-195">L'API di registrazione non include uno scenario per modificare i livelli di registrazione mentre un'app è in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="185ad-195">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="185ad-196">Alcuni provider di configurazione, tuttavia, sono in grado di ricaricare la configurazione, operazione che ha effetto immediato sulla configurazione della registrazione.</span><span class="sxs-lookup"><span data-stu-id="185ad-196">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="185ad-197">Ad esempio, il [provider di configurazione file](xref:fundamentals/configuration/index#file-configuration-provider), aggiunto da `CreateDefaultBuilder` per leggere i file di impostazioni, ricarica la configurazione di registrazione per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="185ad-197">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="185ad-198">Se la configurazione viene modificata nel codice mentre un'app è in esecuzione, l'app può chiamare [IConfigurationRoot. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) per aggiornare la configurazione di registrazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-198">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="185ad-199">Per informazioni sull'implementazione dei provider di configurazione, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="185ad-199">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="185ad-200">Esempio di output di registrazione</span><span class="sxs-lookup"><span data-stu-id="185ad-200">Sample logging output</span></span>

<span data-ttu-id="185ad-201">Con il codice di esempio illustrato nella sezione precedente i log vengono visualizzati nella console quando l'app viene eseguita dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="185ad-201">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="185ad-202">Ecco un esempio di output della console:</span><span class="sxs-lookup"><span data-stu-id="185ad-202">Here's an example of console output:</span></span>

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

<span data-ttu-id="185ad-203">I log precedenti sono stati generati inviando una richiesta HTTP Get all'app di esempio all'indirizzo `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="185ad-203">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="185ad-204">Ecco un esempio degli stessi log visualizzati nella finestra Debug quando si esegue l'app di esempio in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="185ad-204">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

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

<span data-ttu-id="185ad-205">I log creati dalle chiamate a `ILogger` illustrate nella sezione precedente iniziano con "TodoApiSample".</span><span class="sxs-lookup"><span data-stu-id="185ad-205">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="185ad-206">I log che iniziano con le categorie "Microsoft" provengono dal codice del framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="185ad-206">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="185ad-207">ASP.NET Core e il codice dell'applicazione usano la stessa API di registrazione e gli stessi provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-207">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="185ad-208">Il resto di questo articolo spiega alcuni dettagli e le opzioni per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="185ad-208">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="185ad-209">Pacchetti NuGet</span><span class="sxs-lookup"><span data-stu-id="185ad-209">NuGet packages</span></span>

<span data-ttu-id="185ad-210">Le interfacce `ILogger` e `ILoggerFactory` si trovano in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) e le loro implementazioni predefinite si trovano in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="185ad-210">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="185ad-211">Categoria di log</span><span class="sxs-lookup"><span data-stu-id="185ad-211">Log category</span></span>

<span data-ttu-id="185ad-212">Quando viene creato un oggetto `ILogger`, viene specificata la relativa *categoria*.</span><span class="sxs-lookup"><span data-stu-id="185ad-212">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="185ad-213">La categoria è inclusa in ogni messaggio di log creato da tale istanza di `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="185ad-213">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="185ad-214">La categoria può essere qualsiasi stringa, ma per convenzione si usa il nome della classe, ad esempio "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="185ad-214">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="185ad-215">Usare `ILogger<T>` per ottenere un'istanza di `ILogger` che usa il nome completo di tipo `T` come categoria:</span><span class="sxs-lookup"><span data-stu-id="185ad-215">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="185ad-216">Per specificare in modo esplicito la categoria, chiamare `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="185ad-216">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="185ad-217">L'uso di `ILogger<T>` equivale a chiamare `CreateLogger` con il nome completo di tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="185ad-217">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="185ad-218">Livello di registrazione</span><span class="sxs-lookup"><span data-stu-id="185ad-218">Log level</span></span>

<span data-ttu-id="185ad-219">Ogni log specifica un valore <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="185ad-219">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="185ad-220">Il livello di registrazione indica la gravità o l'importanza.</span><span class="sxs-lookup"><span data-stu-id="185ad-220">The log level indicates the severity or importance.</span></span> <span data-ttu-id="185ad-221">È ad esempio possibile scrivere un log `Information` quando un metodo termina normalmente e un log `Warning` quando un metodo restituisce un codice di stato *404 Non trovato*.</span><span class="sxs-lookup"><span data-stu-id="185ad-221">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="185ad-222">Il codice seguente crea i log `Information` e `Warning`:</span><span class="sxs-lookup"><span data-stu-id="185ad-222">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="185ad-223">Nel codice precedente il primo parametro è l'[ID dell'evento di log](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="185ad-223">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="185ad-224">Il secondo parametro è un modello di messaggio con segnaposto per i valori degli argomenti forniti dai parametri dei metodi rimanenti.</span><span class="sxs-lookup"><span data-stu-id="185ad-224">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="185ad-225">I parametri dei metodi sono descritti nella [sezione relativa al modello di messaggio](#log-message-template) più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="185ad-225">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="185ad-226">I metodi di registrazione che includono il livello nel nome del metodo (ad esempio `LogInformation` e `LogWarning`) sono [metodi di estensione per ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="185ad-226">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="185ad-227">Questi metodi chiamano un metodo `Log` che accetta un parametro `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="185ad-227">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="185ad-228">È possibile chiamare il metodo `Log` direttamente anziché chiamare uno di questi metodi di estensione, ma la sintassi è relativamente complessa.</span><span class="sxs-lookup"><span data-stu-id="185ad-228">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="185ad-229">Per altre informazioni, vedere <xref:Microsoft.Extensions.Logging.ILogger> e il [codice sorgente delle estensioni del logger](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="185ad-229">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="185ad-230">ASP.NET Core definisce i livelli di registrazione seguenti, ordinati dal meno grave al più grave.</span><span class="sxs-lookup"><span data-stu-id="185ad-230">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="185ad-231">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="185ad-231">Trace = 0</span></span>

  <span data-ttu-id="185ad-232">Per informazioni in genere utili solo per il debug.</span><span class="sxs-lookup"><span data-stu-id="185ad-232">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="185ad-233">Questi messaggi possono contenere dati sensibili dell'applicazione e pertanto non devono essere abilitati in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="185ad-233">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="185ad-234">*Disattivato per impostazione predefinita.*</span><span class="sxs-lookup"><span data-stu-id="185ad-234">*Disabled by default.*</span></span>

* <span data-ttu-id="185ad-235">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="185ad-235">Debug = 1</span></span>

  <span data-ttu-id="185ad-236">Per informazioni che possono essere utili per lo sviluppo e il debug.</span><span class="sxs-lookup"><span data-stu-id="185ad-236">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="185ad-237">Esempio: `Entering method Configure with flag set to true.` Abilitare i livelli di registrazione `Debug` nell'ambiente di produzione solo in fase di risoluzione dei problemi, per non fare aumentare eccessivamente il volume dei log.</span><span class="sxs-lookup"><span data-stu-id="185ad-237">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="185ad-238">Information = 2</span><span class="sxs-lookup"><span data-stu-id="185ad-238">Information = 2</span></span>

  <span data-ttu-id="185ad-239">Per tenere traccia del flusso generale dell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-239">For tracking the general flow of the app.</span></span> <span data-ttu-id="185ad-240">Queste registrazioni hanno in genere un valore a lungo termine.</span><span class="sxs-lookup"><span data-stu-id="185ad-240">These logs typically have some long-term value.</span></span> <span data-ttu-id="185ad-241">Esempio: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="185ad-241">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="185ad-242">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="185ad-242">Warning = 3</span></span>

  <span data-ttu-id="185ad-243">Per gli eventi imprevisti o anomali nel flusso dell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-243">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="185ad-244">Tali eventi potrebbero includere errori o altre condizioni che non provocano l'arresto dell'app, ma che potrebbe essere necessario analizzare.</span><span class="sxs-lookup"><span data-stu-id="185ad-244">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="185ad-245">Le eccezioni gestite sono una situazione comune in cui usare il livello di registrazione `Warning`.</span><span class="sxs-lookup"><span data-stu-id="185ad-245">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="185ad-246">Esempio: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="185ad-246">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="185ad-247">Error = 4</span><span class="sxs-lookup"><span data-stu-id="185ad-247">Error = 4</span></span>

  <span data-ttu-id="185ad-248">Per errori ed eccezioni che non possono essere gestiti.</span><span class="sxs-lookup"><span data-stu-id="185ad-248">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="185ad-249">Questi messaggi indicano un errore nell'operazione o nell'attività in corso (ad esempio la richiesta HTTP corrente), non un errore a livello di app.</span><span class="sxs-lookup"><span data-stu-id="185ad-249">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="185ad-250">Messaggio di registrazione di esempio:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="185ad-250">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="185ad-251">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="185ad-251">Critical = 5</span></span>

  <span data-ttu-id="185ad-252">Per gli errori che richiedono attenzione immediata.</span><span class="sxs-lookup"><span data-stu-id="185ad-252">For failures that require immediate attention.</span></span> <span data-ttu-id="185ad-253">Esempi: scenari di perdita di dati, spazio su disco insufficiente.</span><span class="sxs-lookup"><span data-stu-id="185ad-253">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="185ad-254">Usare il livello di registrazione per controllare la quantità di output di log scritto in un supporto di archiviazione specifico o in una finestra.</span><span class="sxs-lookup"><span data-stu-id="185ad-254">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="185ad-255">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="185ad-255">For example:</span></span>

* <span data-ttu-id="185ad-256">In produzione:</span><span class="sxs-lookup"><span data-stu-id="185ad-256">In production:</span></span>
  * <span data-ttu-id="185ad-257">La `Trace` registrazione ai livelli `Information` attraverso produce un volume elevato di messaggi di log dettagliati.</span><span class="sxs-lookup"><span data-stu-id="185ad-257">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="185ad-258">Per controllare i costi e non superare i limiti di archiviazione `Trace` dei `Information` dati, accedere ai messaggi di livello elevato a un archivio dati a basso costo.</span><span class="sxs-lookup"><span data-stu-id="185ad-258">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="185ad-259">L'accesso `Warning` ai `Critical` livelli in genere produce un minor numero di messaggi di log più piccoli.</span><span class="sxs-lookup"><span data-stu-id="185ad-259">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="185ad-260">Pertanto, i costi e i limiti di archiviazione in genere non rappresentano un problema, il che comporta una maggiore flessibilità di scelta dell'archivio dati.</span><span class="sxs-lookup"><span data-stu-id="185ad-260">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="185ad-261">Durante lo sviluppo:</span><span class="sxs-lookup"><span data-stu-id="185ad-261">During development:</span></span>
  * <span data-ttu-id="185ad-262">Consente `Warning` di `Critical` accedere ai messaggi nella console.</span><span class="sxs-lookup"><span data-stu-id="185ad-262">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="185ad-263">Aggiungere `Trace` `Information` messaggi durante la risoluzione dei problemi.</span><span class="sxs-lookup"><span data-stu-id="185ad-263">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="185ad-264">La sezione [Filtro dei log](#log-filtering) più avanti in questo articolo descrive come controllare quali livelli di registrazione gestisce un provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-264">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="185ad-265">ASP.NET Core scrive log per gli eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="185ad-265">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="185ad-266">Gli esempi di log illustrati in precedenza in questo articolo escludono i log al di sotto del livello `Information`, quindi non vengono creati log di livello `Debug` o `Trace`.</span><span class="sxs-lookup"><span data-stu-id="185ad-266">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="185ad-267">Ecco un esempio di log della console prodotti eseguendo l'app di esempio configurata in modo da mostrare i log di livello `Debug`:</span><span class="sxs-lookup"><span data-stu-id="185ad-267">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="185ad-268">ID evento di registrazione</span><span class="sxs-lookup"><span data-stu-id="185ad-268">Log event ID</span></span>

<span data-ttu-id="185ad-269">Ogni log può specificare un *ID evento*.</span><span class="sxs-lookup"><span data-stu-id="185ad-269">Each log can specify an *event ID*.</span></span> <span data-ttu-id="185ad-270">A tale scopo, l'app di esempio usa una classe `LoggingEvents` definita a livello locale:</span><span class="sxs-lookup"><span data-stu-id="185ad-270">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="185ad-271">Un ID evento associa un set di eventi.</span><span class="sxs-lookup"><span data-stu-id="185ad-271">An event ID associates a set of events.</span></span> <span data-ttu-id="185ad-272">Ad esempio, tutti i log correlati alla visualizzazione di un elenco di elementi in una pagina potrebbero essere indicati con 1001.</span><span class="sxs-lookup"><span data-stu-id="185ad-272">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="185ad-273">Il provider di log può archiviare l'ID evento in un campo ID o nel messaggio di registrazione oppure non archiviarlo.</span><span class="sxs-lookup"><span data-stu-id="185ad-273">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="185ad-274">Il provider Debug non visualizza gli ID evento.</span><span class="sxs-lookup"><span data-stu-id="185ad-274">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="185ad-275">Il provider Console visualizza gli ID evento tra parentesi quadre dopo la categoria:</span><span class="sxs-lookup"><span data-stu-id="185ad-275">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="185ad-276">Modello di messaggio di registrazione</span><span class="sxs-lookup"><span data-stu-id="185ad-276">Log message template</span></span>

<span data-ttu-id="185ad-277">Ogni log specifica un modello di messaggio.</span><span class="sxs-lookup"><span data-stu-id="185ad-277">Each log specifies a message template.</span></span> <span data-ttu-id="185ad-278">Il modello di messaggio può contenere segnaposto per i quali vengono forniti argomenti.</span><span class="sxs-lookup"><span data-stu-id="185ad-278">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="185ad-279">Usare nomi per i segnaposto, non numeri.</span><span class="sxs-lookup"><span data-stu-id="185ad-279">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="185ad-280">L'ordine dei segnaposto, non il loro nome, determina i parametri da usare per fornire i valori corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="185ad-280">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="185ad-281">Nel codice seguente, si noti che i nomi dei parametri non sono in sequenza nel modello di messaggio:</span><span class="sxs-lookup"><span data-stu-id="185ad-281">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="185ad-282">Questo codice crea un messaggio di log con i valori dei parametri in sequenza:</span><span class="sxs-lookup"><span data-stu-id="185ad-282">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="185ad-283">Il framework di registrazione funziona in questo modo per consentire ai provider di registrazione di implementare la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="185ad-283">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="185ad-284">Al sistema di registrazione vengono passati gli argomenti e non solo il modello di messaggio formattato.</span><span class="sxs-lookup"><span data-stu-id="185ad-284">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="185ad-285">Queste informazioni consentono ai provider di registrazione di archiviare i valori dei parametri come campi.</span><span class="sxs-lookup"><span data-stu-id="185ad-285">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="185ad-286">Si supponga, ad esempio, che il metodo logger esegua chiamate simili alla seguente:</span><span class="sxs-lookup"><span data-stu-id="185ad-286">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="185ad-287">Se si inviano i log all'archiviazione tabelle di Azure, ogni entità tabella di Azure può avere le proprietà `ID` e `RequestTime`, il che semplifica le query sui dati dei log.</span><span class="sxs-lookup"><span data-stu-id="185ad-287">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="185ad-288">Una query può trovare tutti i log entro un determinato intervallo `RequestTime` senza che sia necessario analizzare il messaggio di testo per determinare l'intervallo di tempo.</span><span class="sxs-lookup"><span data-stu-id="185ad-288">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="185ad-289">Registrazione delle eccezioni</span><span class="sxs-lookup"><span data-stu-id="185ad-289">Logging exceptions</span></span>

<span data-ttu-id="185ad-290">I metodi logger dispongono di overload che consentono di passare un'eccezione, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="185ad-290">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="185ad-291">Provider diversi gestiscono le informazioni sulle eccezioni in modi diversi.</span><span class="sxs-lookup"><span data-stu-id="185ad-291">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="185ad-292">Ecco un esempio di output del provider Debug dal codice sopra riportato.</span><span class="sxs-lookup"><span data-stu-id="185ad-292">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="185ad-293">Filtro del log</span><span class="sxs-lookup"><span data-stu-id="185ad-293">Log filtering</span></span>

<span data-ttu-id="185ad-294">È possibile specificare un livello di registrazione minimo per un provider e una categoria specifici oppure per tutti i provider o tutte le categorie.</span><span class="sxs-lookup"><span data-stu-id="185ad-294">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="185ad-295">Tutti i log sotto il livello minimo non sono passati al provider, quindi non vengono visualizzati o archiviati.</span><span class="sxs-lookup"><span data-stu-id="185ad-295">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="185ad-296">Per eliminare tutti i log, specificare `LogLevel.None` come livello di registrazione minimo.</span><span class="sxs-lookup"><span data-stu-id="185ad-296">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="185ad-297">Il valore intero di `LogLevel.None` è 6, maggiore di `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="185ad-297">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="185ad-298">Creare regole di filtro nella configurazione</span><span class="sxs-lookup"><span data-stu-id="185ad-298">Create filter rules in configuration</span></span>

<span data-ttu-id="185ad-299">Il codice del modello di `CreateDefaultBuilder` progetto chiama per impostare la registrazione per i provider console, debug e EventSource (ASP.NET Core 2,2 o versione successiva).</span><span class="sxs-lookup"><span data-stu-id="185ad-299">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="185ad-300">Il metodo `CreateDefaultBuilder` imposta inoltre la registrazione per la ricerca della configurazione in una sezione `Logging`, come illustrato [in precedenza in questo articolo](#configuration).</span><span class="sxs-lookup"><span data-stu-id="185ad-300">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="185ad-301">I dati di configurazione specificano i livelli di registrazione minimi in base al provider e alla categoria, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="185ad-301">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="185ad-302">Questo codice JSON crea sei regole di filtro, una per il provider Debug, quattro per il provider Console e una per tutti i provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-302">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="185ad-303">Quando viene creato un oggetto `ILogger`, viene scelta una singola regola per ogni provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-303">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="185ad-304">Regole di filtro nel codice</span><span class="sxs-lookup"><span data-stu-id="185ad-304">Filter rules in code</span></span>

<span data-ttu-id="185ad-305">L'esempio seguente illustra come registrare le regole di filtro nel codice:</span><span class="sxs-lookup"><span data-stu-id="185ad-305">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="185ad-306">Il secondo `AddFilter` specifica il provider Debug tramite il nome del tipo.</span><span class="sxs-lookup"><span data-stu-id="185ad-306">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="185ad-307">Il primo `AddFilter` si applica a tutti i provider in quanto non consente di specificare un tipo di provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-307">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="185ad-308">Applicazione delle regole di filtro</span><span class="sxs-lookup"><span data-stu-id="185ad-308">How filtering rules are applied</span></span>

<span data-ttu-id="185ad-309">I dati di configurazione e il codice `AddFilter` illustrato negli esempi precedenti creano le regole indicate nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="185ad-309">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="185ad-310">I primi sei provengono dall'esempio di configurazione e gli ultimi due dall'esempio di codice.</span><span class="sxs-lookup"><span data-stu-id="185ad-310">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="185ad-311">Number</span><span class="sxs-lookup"><span data-stu-id="185ad-311">Number</span></span> | <span data-ttu-id="185ad-312">Provider</span><span class="sxs-lookup"><span data-stu-id="185ad-312">Provider</span></span>      | <span data-ttu-id="185ad-313">Categorie che iniziano con...</span><span class="sxs-lookup"><span data-stu-id="185ad-313">Categories that begin with ...</span></span>          | <span data-ttu-id="185ad-314">Livello di registrazione minimo</span><span class="sxs-lookup"><span data-stu-id="185ad-314">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="185ad-315">1</span><span class="sxs-lookup"><span data-stu-id="185ad-315">1</span></span>      | <span data-ttu-id="185ad-316">Debug</span><span class="sxs-lookup"><span data-stu-id="185ad-316">Debug</span></span>         | <span data-ttu-id="185ad-317">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="185ad-317">All categories</span></span>                          | <span data-ttu-id="185ad-318">Informazioni</span><span class="sxs-lookup"><span data-stu-id="185ad-318">Information</span></span>       |
| <span data-ttu-id="185ad-319">2</span><span class="sxs-lookup"><span data-stu-id="185ad-319">2</span></span>      | <span data-ttu-id="185ad-320">Console</span><span class="sxs-lookup"><span data-stu-id="185ad-320">Console</span></span>       | <span data-ttu-id="185ad-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="185ad-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="185ad-322">Avviso</span><span class="sxs-lookup"><span data-stu-id="185ad-322">Warning</span></span>           |
| <span data-ttu-id="185ad-323">3</span><span class="sxs-lookup"><span data-stu-id="185ad-323">3</span></span>      | <span data-ttu-id="185ad-324">Console</span><span class="sxs-lookup"><span data-stu-id="185ad-324">Console</span></span>       | <span data-ttu-id="185ad-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="185ad-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="185ad-326">Debug</span><span class="sxs-lookup"><span data-stu-id="185ad-326">Debug</span></span>             |
| <span data-ttu-id="185ad-327">4</span><span class="sxs-lookup"><span data-stu-id="185ad-327">4</span></span>      | <span data-ttu-id="185ad-328">Console</span><span class="sxs-lookup"><span data-stu-id="185ad-328">Console</span></span>       | <span data-ttu-id="185ad-329">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="185ad-329">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="185ad-330">Errore</span><span class="sxs-lookup"><span data-stu-id="185ad-330">Error</span></span>             |
| <span data-ttu-id="185ad-331">5</span><span class="sxs-lookup"><span data-stu-id="185ad-331">5</span></span>      | <span data-ttu-id="185ad-332">Console</span><span class="sxs-lookup"><span data-stu-id="185ad-332">Console</span></span>       | <span data-ttu-id="185ad-333">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="185ad-333">All categories</span></span>                          | <span data-ttu-id="185ad-334">Informazioni</span><span class="sxs-lookup"><span data-stu-id="185ad-334">Information</span></span>       |
| <span data-ttu-id="185ad-335">6</span><span class="sxs-lookup"><span data-stu-id="185ad-335">6</span></span>      | <span data-ttu-id="185ad-336">Tutti i provider</span><span class="sxs-lookup"><span data-stu-id="185ad-336">All providers</span></span> | <span data-ttu-id="185ad-337">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="185ad-337">All categories</span></span>                          | <span data-ttu-id="185ad-338">Debug</span><span class="sxs-lookup"><span data-stu-id="185ad-338">Debug</span></span>             |
| <span data-ttu-id="185ad-339">7</span><span class="sxs-lookup"><span data-stu-id="185ad-339">7</span></span>      | <span data-ttu-id="185ad-340">Tutti i provider</span><span class="sxs-lookup"><span data-stu-id="185ad-340">All providers</span></span> | <span data-ttu-id="185ad-341">System</span><span class="sxs-lookup"><span data-stu-id="185ad-341">System</span></span>                                  | <span data-ttu-id="185ad-342">Debug</span><span class="sxs-lookup"><span data-stu-id="185ad-342">Debug</span></span>             |
| <span data-ttu-id="185ad-343">8</span><span class="sxs-lookup"><span data-stu-id="185ad-343">8</span></span>      | <span data-ttu-id="185ad-344">Debug</span><span class="sxs-lookup"><span data-stu-id="185ad-344">Debug</span></span>         | <span data-ttu-id="185ad-345">Microsoft</span><span class="sxs-lookup"><span data-stu-id="185ad-345">Microsoft</span></span>                               | <span data-ttu-id="185ad-346">Trace</span><span class="sxs-lookup"><span data-stu-id="185ad-346">Trace</span></span>             |

<span data-ttu-id="185ad-347">Quando viene creato un oggetto `ILogger`, l'oggetto `ILoggerFactory` seleziona una singola regola per ogni provider da applicare al logger.</span><span class="sxs-lookup"><span data-stu-id="185ad-347">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="185ad-348">Tutti i messaggi scritti da un'istanza di `ILogger` vengono filtrati in base alle regole selezionate.</span><span class="sxs-lookup"><span data-stu-id="185ad-348">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="185ad-349">Tra le regole disponibili viene selezionata la regola più specifica possibile per ogni coppia di categoria e provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-349">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="185ad-350">L'algoritmo seguente viene usato per ogni provider quando viene creato un `ILogger` per una determinata categoria:</span><span class="sxs-lookup"><span data-stu-id="185ad-350">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="185ad-351">Selezionare tutte le regole corrispondenti al provider o al relativo alias.</span><span class="sxs-lookup"><span data-stu-id="185ad-351">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="185ad-352">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole con un provider vuoto.</span><span class="sxs-lookup"><span data-stu-id="185ad-352">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="185ad-353">Dal risultato del passaggio precedente, selezionare le regole con il prefisso di categoria corrispondente più lungo.</span><span class="sxs-lookup"><span data-stu-id="185ad-353">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="185ad-354">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole che non specificano una categoria.</span><span class="sxs-lookup"><span data-stu-id="185ad-354">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="185ad-355">Se sono selezionate più regole, scegliere l'**ultima**.</span><span class="sxs-lookup"><span data-stu-id="185ad-355">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="185ad-356">Se non è selezionata alcuna regola, usare `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="185ad-356">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="185ad-357">Con l'elenco di regole precedente, si supponga di creare un oggetto `ILogger` per la categoria "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="185ad-357">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="185ad-358">Per il provider Debug si applicano le regole 1, 6 e 8.</span><span class="sxs-lookup"><span data-stu-id="185ad-358">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="185ad-359">La regola 8 è più specifica, così sarà quella selezionata.</span><span class="sxs-lookup"><span data-stu-id="185ad-359">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="185ad-360">Per il provider Console si applicano le regole 3, 4, 5 e 6.</span><span class="sxs-lookup"><span data-stu-id="185ad-360">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="185ad-361">La regola 3 è la più specifica.</span><span class="sxs-lookup"><span data-stu-id="185ad-361">Rule 3 is most specific.</span></span>

<span data-ttu-id="185ad-362">L'istanza di `ILogger` risultante invia i log di livello `Trace` o superiore al provider Debug.</span><span class="sxs-lookup"><span data-stu-id="185ad-362">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="185ad-363">I log di livello `Debug` o superiore vengono inviati al provider Console.</span><span class="sxs-lookup"><span data-stu-id="185ad-363">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="185ad-364">Alias dei provider</span><span class="sxs-lookup"><span data-stu-id="185ad-364">Provider aliases</span></span>

<span data-ttu-id="185ad-365">Ogni provider definisce un *alias* che può essere utilizzato nella configurazione al posto del nome completo di tipo.</span><span class="sxs-lookup"><span data-stu-id="185ad-365">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="185ad-366">Per i provider predefiniti, usare gli alias seguenti:</span><span class="sxs-lookup"><span data-stu-id="185ad-366">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="185ad-367">Console</span><span class="sxs-lookup"><span data-stu-id="185ad-367">Console</span></span>
* <span data-ttu-id="185ad-368">Debug</span><span class="sxs-lookup"><span data-stu-id="185ad-368">Debug</span></span>
* <span data-ttu-id="185ad-369">EventSource</span><span class="sxs-lookup"><span data-stu-id="185ad-369">EventSource</span></span>
* <span data-ttu-id="185ad-370">EventLog</span><span class="sxs-lookup"><span data-stu-id="185ad-370">EventLog</span></span>
* <span data-ttu-id="185ad-371">TraceSource</span><span class="sxs-lookup"><span data-stu-id="185ad-371">TraceSource</span></span>
* <span data-ttu-id="185ad-372">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="185ad-372">AzureAppServicesFile</span></span>
* <span data-ttu-id="185ad-373">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="185ad-373">AzureAppServicesBlob</span></span>
* <span data-ttu-id="185ad-374">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="185ad-374">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="185ad-375">Livello minimo predefinito</span><span class="sxs-lookup"><span data-stu-id="185ad-375">Default minimum level</span></span>

<span data-ttu-id="185ad-376">Esiste un'impostazione del livello minimo che diventa effettiva solo se non si applica alcuna regola di configurazione o codice per un provider e una categoria specifici.</span><span class="sxs-lookup"><span data-stu-id="185ad-376">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="185ad-377">L'esempio seguente illustra come impostare il livello minimo:</span><span class="sxs-lookup"><span data-stu-id="185ad-377">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="185ad-378">Se non si imposta in modo esplicito il livello minimo, il valore predefinito è `Information`, che indica che i log `Trace` e `Debug` vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="185ad-378">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="185ad-379">Funzioni di filtro</span><span class="sxs-lookup"><span data-stu-id="185ad-379">Filter functions</span></span>

<span data-ttu-id="185ad-380">Una funzione di filtro viene richiamata per tutti i provider e le categorie a cui non sono assegnate regole tramite la configurazione o il codice.</span><span class="sxs-lookup"><span data-stu-id="185ad-380">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="185ad-381">Il codice della funzione ha accesso al tipo di provider, alla categoria e al livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="185ad-381">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="185ad-382">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="185ad-382">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="185ad-383">Livelli e categorie di sistema</span><span class="sxs-lookup"><span data-stu-id="185ad-383">System categories and levels</span></span>

<span data-ttu-id="185ad-384">Di seguito sono elencate alcune categorie usate da ASP.NET Core ed Entity Framework Core, con note sui log previsti per ognuna:</span><span class="sxs-lookup"><span data-stu-id="185ad-384">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="185ad-385">Category</span><span class="sxs-lookup"><span data-stu-id="185ad-385">Category</span></span>                            | <span data-ttu-id="185ad-386">Note</span><span class="sxs-lookup"><span data-stu-id="185ad-386">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="185ad-387">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="185ad-387">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="185ad-388">Diagnostica generale di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="185ad-388">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="185ad-389">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="185ad-389">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="185ad-390">Chiavi considerate, trovate e usate.</span><span class="sxs-lookup"><span data-stu-id="185ad-390">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="185ad-391">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="185ad-391">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="185ad-392">Host consentiti.</span><span class="sxs-lookup"><span data-stu-id="185ad-392">Hosts allowed.</span></span> |
| <span data-ttu-id="185ad-393">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="185ad-393">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="185ad-394">Tempo impiegato per il completamento delle richieste HTTP e ora di inizio delle richieste.</span><span class="sxs-lookup"><span data-stu-id="185ad-394">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="185ad-395">Assembly di avvio dell'hosting caricati.</span><span class="sxs-lookup"><span data-stu-id="185ad-395">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="185ad-396">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="185ad-396">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="185ad-397">Diagnostica di MVC e Razor.</span><span class="sxs-lookup"><span data-stu-id="185ad-397">MVC and Razor diagnostics.</span></span> <span data-ttu-id="185ad-398">Associazione di modelli, esecuzione di filtri, compilazione di viste, selezione di azioni.</span><span class="sxs-lookup"><span data-stu-id="185ad-398">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="185ad-399">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="185ad-399">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="185ad-400">Informazioni sulla corrispondenza di route.</span><span class="sxs-lookup"><span data-stu-id="185ad-400">Route matching information.</span></span> |
| <span data-ttu-id="185ad-401">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="185ad-401">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="185ad-402">Risposte di avvio, arresto e keep-alive per la connessione.</span><span class="sxs-lookup"><span data-stu-id="185ad-402">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="185ad-403">Informazioni sui certificati HTTPS.</span><span class="sxs-lookup"><span data-stu-id="185ad-403">HTTPS certificate information.</span></span> |
| <span data-ttu-id="185ad-404">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="185ad-404">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="185ad-405">File forniti.</span><span class="sxs-lookup"><span data-stu-id="185ad-405">Files served.</span></span> |
| <span data-ttu-id="185ad-406">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="185ad-406">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="185ad-407">Diagnostica generale di Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="185ad-407">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="185ad-408">Attività e configurazione del database, rilevamento delle modifiche, migrazioni.</span><span class="sxs-lookup"><span data-stu-id="185ad-408">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="185ad-409">Ambiti dei log</span><span class="sxs-lookup"><span data-stu-id="185ad-409">Log scopes</span></span>

 <span data-ttu-id="185ad-410">Un *ambito* può raggruppare un set di operazioni logiche.</span><span class="sxs-lookup"><span data-stu-id="185ad-410">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="185ad-411">Questo raggruppamento può essere usato per collegare gli stessi dati a ogni log creato come parte di un set.</span><span class="sxs-lookup"><span data-stu-id="185ad-411">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="185ad-412">Ad esempio, ogni log creato come parte dell'elaborazione di una transazione può includere l'ID transazione.</span><span class="sxs-lookup"><span data-stu-id="185ad-412">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="185ad-413">Un ambito è un tipo `IDisposable` restituito dal metodo <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> e viene mantenuto fino all'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="185ad-413">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="185ad-414">Un ambito si usa mediante il wrapping delle chiamate del logger in un blocco `using`:</span><span class="sxs-lookup"><span data-stu-id="185ad-414">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="185ad-415">Il codice seguente abilita gli ambiti per il provider Console:</span><span class="sxs-lookup"><span data-stu-id="185ad-415">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="185ad-416">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="185ad-416">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="185ad-417">La configurazione dell'opzione del logger della console `IncludeScopes` è necessaria per abilitare la registrazione basata sull'ambito.</span><span class="sxs-lookup"><span data-stu-id="185ad-417">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="185ad-418">Per informazioni sulla configurazione, vedere la sezione [Configurazione](#configuration).</span><span class="sxs-lookup"><span data-stu-id="185ad-418">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="185ad-419">Ogni messaggio di registrazione include le informazioni sull'ambito:</span><span class="sxs-lookup"><span data-stu-id="185ad-419">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="185ad-420">Provider di registrazione predefiniti</span><span class="sxs-lookup"><span data-stu-id="185ad-420">Built-in logging providers</span></span>

<span data-ttu-id="185ad-421">ASP.NET Core include i provider seguenti:</span><span class="sxs-lookup"><span data-stu-id="185ad-421">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="185ad-422">Console</span><span class="sxs-lookup"><span data-stu-id="185ad-422">Console</span></span>](#console-provider)
* [<span data-ttu-id="185ad-423">Debug</span><span class="sxs-lookup"><span data-stu-id="185ad-423">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="185ad-424">EventSource</span><span class="sxs-lookup"><span data-stu-id="185ad-424">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="185ad-425">EventLog</span><span class="sxs-lookup"><span data-stu-id="185ad-425">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="185ad-426">TraceSource</span><span class="sxs-lookup"><span data-stu-id="185ad-426">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="185ad-427">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="185ad-427">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="185ad-428">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="185ad-428">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="185ad-429">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="185ad-429">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="185ad-430">Per informazioni su StdOut e la registrazione debug con il modulo ASP.NET Core, vedere <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="185ad-430">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="185ad-431">Provider Console</span><span class="sxs-lookup"><span data-stu-id="185ad-431">Console provider</span></span>

<span data-ttu-id="185ad-432">Il pacchetto del provider [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) invia l'output della registrazione alla console.</span><span class="sxs-lookup"><span data-stu-id="185ad-432">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="185ad-433">Per visualizzare l'output di registrazione del provider Console, aprire un prompt dei comandi nella cartella del progetto ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="185ad-433">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="185ad-434">Provider Debug</span><span class="sxs-lookup"><span data-stu-id="185ad-434">Debug provider</span></span>

<span data-ttu-id="185ad-435">Il pacchetto del provider [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) scrive l'output della registrazione usando la classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (chiamate del metodo `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="185ad-435">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="185ad-436">In Linux, questo provider scrive i log in */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="185ad-436">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="185ad-437">Provider origine evento</span><span class="sxs-lookup"><span data-stu-id="185ad-437">Event Source provider</span></span>

<span data-ttu-id="185ad-438">Il pacchetto del provider [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) scrive in un'origine evento multipiattaforma con il nome `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="185ad-438">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="185ad-439">In Windows, il provider utilizza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="185ad-439">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="185ad-440">Il provider di origine eventi viene aggiunto automaticamente `CreateDefaultBuilder` quando viene chiamato per compilare l'host.</span><span class="sxs-lookup"><span data-stu-id="185ad-440">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="185ad-441">strumenti di traccia DotNet</span><span class="sxs-lookup"><span data-stu-id="185ad-441">dotnet trace tooling</span></span>

<span data-ttu-id="185ad-442">Lo strumento [DotNet-Trace](/dotnet/core/diagnostics/dotnet-trace) è uno strumento globale dell'interfaccia della riga di comando multipiattaforma che consente la raccolta di tracce .NET Core di un processo in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="185ad-442">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="185ad-443">Lo strumento raccoglie i <xref:Microsoft.Extensions.Logging.EventSource> dati del provider utilizzando <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>un.</span><span class="sxs-lookup"><span data-stu-id="185ad-443">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="185ad-444">Installare gli strumenti di traccia DotNet con il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="185ad-444">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="185ad-445">Usare gli strumenti di traccia DotNet per raccogliere una traccia da un'app:</span><span class="sxs-lookup"><span data-stu-id="185ad-445">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="185ad-446">Se l'app non compila l'host con `CreateDefaultBuilder`, aggiungere il [provider di origine eventi](#event-source-provider) alla configurazione di registrazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-446">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="185ad-447">Eseguire l'app con il `dotnet run` comando.</span><span class="sxs-lookup"><span data-stu-id="185ad-447">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="185ad-448">Determinare l'identificatore di processo (PID) dell'app .NET Core:</span><span class="sxs-lookup"><span data-stu-id="185ad-448">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="185ad-449">In Windows usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="185ad-449">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="185ad-450">Gestione attività (CTRL + ALT + CANC)</span><span class="sxs-lookup"><span data-stu-id="185ad-450">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="185ad-451">comando tasklist</span><span class="sxs-lookup"><span data-stu-id="185ad-451">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="185ad-452">Comando PowerShell Get-Process</span><span class="sxs-lookup"><span data-stu-id="185ad-452">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="185ad-453">In Linux usare il [comando pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="185ad-453">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="185ad-454">Trovare il PID per il processo con lo stesso nome dell'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-454">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="185ad-455">Eseguire il `dotnet trace` comando.</span><span class="sxs-lookup"><span data-stu-id="185ad-455">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="185ad-456">Sintassi generale del comando:</span><span class="sxs-lookup"><span data-stu-id="185ad-456">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="185ad-457">Quando si usa una shell dei comandi di PowerShell, `--providers` racchiudere il valore tra`'`virgolette singole ():</span><span class="sxs-lookup"><span data-stu-id="185ad-457">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="185ad-458">Nelle piattaforme non Windows aggiungere l' `-f speedscope` opzione per modificare il formato del file di traccia di output in. `speedscope`</span><span class="sxs-lookup"><span data-stu-id="185ad-458">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="185ad-459">Parola chiave</span><span class="sxs-lookup"><span data-stu-id="185ad-459">Keyword</span></span> | <span data-ttu-id="185ad-460">Descrizione</span><span class="sxs-lookup"><span data-stu-id="185ad-460">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="185ad-461">1</span><span class="sxs-lookup"><span data-stu-id="185ad-461">1</span></span>       | <span data-ttu-id="185ad-462">Registra i `LoggingEventSource`metadati relativi a.</span><span class="sxs-lookup"><span data-stu-id="185ad-462">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="185ad-463">Non registra gli eventi `ILogger`da).</span><span class="sxs-lookup"><span data-stu-id="185ad-463">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="185ad-464">2</span><span class="sxs-lookup"><span data-stu-id="185ad-464">2</span></span>       | <span data-ttu-id="185ad-465">Attiva l'evento `Message` quando `ILogger.Log()` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="185ad-465">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="185ad-466">Fornisce informazioni in un metodo programmatico (non formattato).</span><span class="sxs-lookup"><span data-stu-id="185ad-466">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="185ad-467">4</span><span class="sxs-lookup"><span data-stu-id="185ad-467">4</span></span>       | <span data-ttu-id="185ad-468">Attiva l'evento `FormatMessage` quando `ILogger.Log()` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="185ad-468">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="185ad-469">Fornisce la versione di stringa formattata delle informazioni.</span><span class="sxs-lookup"><span data-stu-id="185ad-469">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="185ad-470">8</span><span class="sxs-lookup"><span data-stu-id="185ad-470">8</span></span>       | <span data-ttu-id="185ad-471">Attiva l'evento `MessageJson` quando `ILogger.Log()` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="185ad-471">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="185ad-472">Fornisce una rappresentazione JSON degli argomenti.</span><span class="sxs-lookup"><span data-stu-id="185ad-472">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="185ad-473">Livello dell'evento</span><span class="sxs-lookup"><span data-stu-id="185ad-473">Event Level</span></span> | <span data-ttu-id="185ad-474">Description</span><span class="sxs-lookup"><span data-stu-id="185ad-474">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="185ad-475">0</span><span class="sxs-lookup"><span data-stu-id="185ad-475">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="185ad-476">1</span><span class="sxs-lookup"><span data-stu-id="185ad-476">1</span></span>           | `Critical`      |
   | <span data-ttu-id="185ad-477">2</span><span class="sxs-lookup"><span data-stu-id="185ad-477">2</span></span>           | `Error`         |
   | <span data-ttu-id="185ad-478">3</span><span class="sxs-lookup"><span data-stu-id="185ad-478">3</span></span>           | `Warning`       |
   | <span data-ttu-id="185ad-479">4</span><span class="sxs-lookup"><span data-stu-id="185ad-479">4</span></span>           | `Informational` |
   | <span data-ttu-id="185ad-480">5</span><span class="sxs-lookup"><span data-stu-id="185ad-480">5</span></span>           | `Verbose`       |

   <span data-ttu-id="185ad-481">`FilterSpecs`le voci `{Logger Category}` per `{Event Level}` e rappresentano altre condizioni di filtro del log.</span><span class="sxs-lookup"><span data-stu-id="185ad-481">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="185ad-482">Separare `FilterSpecs` le voci con un punto`;`e virgola ().</span><span class="sxs-lookup"><span data-stu-id="185ad-482">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="185ad-483">Esempio di utilizzo di una shell dei**no** comandi di Windows (senza `--providers` virgolette singole intorno al valore):</span><span class="sxs-lookup"><span data-stu-id="185ad-483">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="185ad-484">Il comando precedente attiva:</span><span class="sxs-lookup"><span data-stu-id="185ad-484">The preceding command activates:</span></span>

   * <span data-ttu-id="185ad-485">Il logger dell'origine evento per produrre stringhe formattate (`4`) per`2`gli errori ().</span><span class="sxs-lookup"><span data-stu-id="185ad-485">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="185ad-486">`Microsoft.AspNetCore.Hosting`registrazione a livello `Informational` di registrazione (`4`).</span><span class="sxs-lookup"><span data-stu-id="185ad-486">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="185ad-487">Arrestare gli strumenti di traccia DotNet premendo il tasto invio o CTRL + C.</span><span class="sxs-lookup"><span data-stu-id="185ad-487">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="185ad-488">La traccia viene salvata con il nome *Trace. NetTrace* nella cartella in cui `dotnet trace` viene eseguito il comando.</span><span class="sxs-lookup"><span data-stu-id="185ad-488">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="185ad-489">Aprire la traccia con [PerfView](#perfview).</span><span class="sxs-lookup"><span data-stu-id="185ad-489">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="185ad-490">Aprire il file *Trace. NetTrace* ed esplorare gli eventi di traccia.</span><span class="sxs-lookup"><span data-stu-id="185ad-490">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="185ad-491">Per altre informazioni, vedi:</span><span class="sxs-lookup"><span data-stu-id="185ad-491">For more information, see:</span></span>

* <span data-ttu-id="185ad-492">[Trace for Performance Analysis Utility (DotNet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (documentazione di .NET Core)</span><span class="sxs-lookup"><span data-stu-id="185ad-492">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="185ad-493">[Trace for Performance Analysis Utility (DotNet-Trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (documentazione del repository GitHub DotNet/Diagnostics)</span><span class="sxs-lookup"><span data-stu-id="185ad-493">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="185ad-494">[Classe LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (browser API .NET)</span><span class="sxs-lookup"><span data-stu-id="185ad-494">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="185ad-495">[Origine riferimento LoggingEventSource (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; per ottenere un'origine riferimento per una versione diversa, modificare il ramo `release/{Version}`in, `{Version}` dove è la versione di ASP.NET Core desiderata.</span><span class="sxs-lookup"><span data-stu-id="185ad-495">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="185ad-496">[PerfView](#perfview) &ndash; utile per la visualizzazione delle tracce dell'origine evento.</span><span class="sxs-lookup"><span data-stu-id="185ad-496">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="185ad-497">PerfView</span><span class="sxs-lookup"><span data-stu-id="185ad-497">Perfview</span></span>

<span data-ttu-id="185ad-498">Usare l' [utilità PerfView](https://github.com/Microsoft/perfview) per raccogliere e visualizzare i log.</span><span class="sxs-lookup"><span data-stu-id="185ad-498">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="185ad-499">Sono disponibili altri strumenti per la visualizzazione dei log ETW, ma PerfView fornisce un'esperienza ottimale per l'uso con gli eventi ETW generati da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="185ad-499">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="185ad-500">Per configurare PerfView per la raccolta degli eventi registrati da questo provider, aggiungere la stringa `*Microsoft-Extensions-Logging` nell'elenco **Provider aggiuntivi**</span><span class="sxs-lookup"><span data-stu-id="185ad-500">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="185ad-501">(non dimenticare l'asterisco all'inizio della stringa).</span><span class="sxs-lookup"><span data-stu-id="185ad-501">(Don't miss the asterisk at the start of the string.)</span></span>

![Provider aggiuntivi di PerfView](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="185ad-503">Provider EventLog di Windows</span><span class="sxs-lookup"><span data-stu-id="185ad-503">Windows EventLog provider</span></span>

<span data-ttu-id="185ad-504">Il pacchetto di provider [Microsoft.Extensions.Logging.AventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) invia l'output del log al registro eventi di Windows.</span><span class="sxs-lookup"><span data-stu-id="185ad-504">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="185ad-505">Gli [overload di AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) consentono di passare <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="185ad-505">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="185ad-506">Se `null` o non è specificato, vengono usate le impostazioni predefinite seguenti:</span><span class="sxs-lookup"><span data-stu-id="185ad-506">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="185ad-507">`LogName`&ndash; "Applicazione"</span><span class="sxs-lookup"><span data-stu-id="185ad-507">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="185ad-508">`SourceName`&ndash; "Runtime .NET"</span><span class="sxs-lookup"><span data-stu-id="185ad-508">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="185ad-509">`MachineName` &ndash; computer locale</span><span class="sxs-lookup"><span data-stu-id="185ad-509">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="185ad-510">Gli eventi vengono registrati per un [livello di avviso e un livello superiore](#log-level).</span><span class="sxs-lookup"><span data-stu-id="185ad-510">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="185ad-511">Per registrare eventi inferiori a `Warning`, impostare in modo esplicito il livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="185ad-511">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="185ad-512">Aggiungere ad esempio il codice seguente al file *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="185ad-512">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="185ad-513">Provider TraceSource</span><span class="sxs-lookup"><span data-stu-id="185ad-513">TraceSource provider</span></span>

<span data-ttu-id="185ad-514">Il pacchetto di provider [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa le librerie e i provider <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="185ad-514">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="185ad-515">Gli [overload di AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) consentono di passare un cambio di origine e un listener di traccia.</span><span class="sxs-lookup"><span data-stu-id="185ad-515">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="185ad-516">Per usare questo provider, un'app deve essere in esecuzione in .NET Framework (invece che in .NET Core).</span><span class="sxs-lookup"><span data-stu-id="185ad-516">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="185ad-517">Il provider può instradare i messaggi a diversi [listener](/dotnet/framework/debug-trace-profile/trace-listeners), come <xref:System.Diagnostics.TextWriterTraceListener>, usato nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="185ad-517">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="185ad-518">Provider del Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="185ad-518">Azure App Service provider</span></span>

<span data-ttu-id="185ad-519">Il pacchetto di provider [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) scrive i log in file di testo nel file system di un'app del Servizio app di Azure e nell'[archivio di BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in un account di archiviazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="185ad-519">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="185ad-520">Il pacchetto del provider non è incluso nel framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="185ad-520">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="185ad-521">Per usare il provider, aggiungere il relativo pacchetto al progetto.</span><span class="sxs-lookup"><span data-stu-id="185ad-521">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="185ad-522">Per configurare le impostazioni del provider, usare <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> e <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="185ad-522">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="185ad-523">Quando si distribuisce un'app del servizio app, l'applicazione applica le impostazioni della sezione [Log del servizio app](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) della pagina **Servizio app** del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="185ad-523">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="185ad-524">Quando le impostazioni seguenti vengono aggiornate, le modifiche hanno effetto immediatamente senza richiedere un riavvio o la ridistribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-524">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="185ad-525">**Registrazione applicazioni (file system)**</span><span class="sxs-lookup"><span data-stu-id="185ad-525">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="185ad-526">**Registrazione applicazione (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="185ad-526">**Application Logging (Blob)**</span></span>

<span data-ttu-id="185ad-527">Il percorso predefinito per i file di log è nella cartella *D:\\home\\LogFiles\\Application* e il nome di file predefinito è *diagnostics-aaaammgg.txt*.</span><span class="sxs-lookup"><span data-stu-id="185ad-527">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="185ad-528">Il limite predefinito per le dimensioni del file è 10 MB e il numero massimo predefinito di file conservati è 2.</span><span class="sxs-lookup"><span data-stu-id="185ad-528">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="185ad-529">Il nome BLOB predefinito è *{nome-app}{timestamp}/aaaa/mm/gg/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="185ad-529">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="185ad-530">Il provider funziona solo quando il progetto viene eseguito nell'ambiente di Azure.</span><span class="sxs-lookup"><span data-stu-id="185ad-530">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="185ad-531">Non ha alcun effetto quando il progetto viene eseguito localmente, in quanto non scrive nulla in file locali o in archivi di sviluppo locali per i BLOB.</span><span class="sxs-lookup"><span data-stu-id="185ad-531">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="185ad-532">Flusso di registrazione di Azure</span><span class="sxs-lookup"><span data-stu-id="185ad-532">Azure log streaming</span></span>

<span data-ttu-id="185ad-533">Il flusso di registrazione di Azure consente di visualizzare l'attività di registrazione in tempo reale da:</span><span class="sxs-lookup"><span data-stu-id="185ad-533">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="185ad-534">Server applicazioni</span><span class="sxs-lookup"><span data-stu-id="185ad-534">The app server</span></span>
* <span data-ttu-id="185ad-535">Server Web</span><span class="sxs-lookup"><span data-stu-id="185ad-535">The web server</span></span>
* <span data-ttu-id="185ad-536">Traccia delle richieste non riuscite</span><span class="sxs-lookup"><span data-stu-id="185ad-536">Failed request tracing</span></span>

<span data-ttu-id="185ad-537">Per configurare il flusso di registrazione di Azure:</span><span class="sxs-lookup"><span data-stu-id="185ad-537">To configure Azure log streaming:</span></span>

* <span data-ttu-id="185ad-538">Passare alla pagina **Log del servizio app** dalla pagina del portale dell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-538">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="185ad-539">Impostare **Registrazione applicazioni (file system)** su **Attiva**.</span><span class="sxs-lookup"><span data-stu-id="185ad-539">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="185ad-540">Scegliere il livello di registrazione in **Livello**.</span><span class="sxs-lookup"><span data-stu-id="185ad-540">Choose the log **Level**.</span></span> <span data-ttu-id="185ad-541">Questa impostazione è valida solo per lo streaming dei log di Azure, non per altri provider di registrazione nell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-541">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="185ad-542">Passare alla pagina **Flusso di registrazione** per visualizzare i messaggi dell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-542">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="185ad-543">I messaggi vengono registrati dall'app tramite l'interfaccia `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="185ad-543">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="185ad-544">Registrazione di traccia di Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="185ad-544">Azure Application Insights trace logging</span></span>

<span data-ttu-id="185ad-545">Il pacchetto di provider [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) scrive log in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="185ad-545">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="185ad-546">Application Insights è un servizio che monitora un'app Web e fornisce gli strumenti per l'esecuzione di query sui dati di telemetria e la loro analisi.</span><span class="sxs-lookup"><span data-stu-id="185ad-546">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="185ad-547">Se si usa questo provider, è possibile eseguire query sui log e analizzarli usando gli strumenti di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="185ad-547">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="185ad-548">Il provider di registrazione è incluso come dipendenza di [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), ovvero il pacchetto che fornisce tutti i dati di telemetria disponibili per ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="185ad-548">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="185ad-549">Se si usa questo pacchetto, non è necessario installare il pacchetto di provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-549">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="185ad-550">Non usare il pacchetto [](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)Microsoft.ApplicationInsights.Web&mdash;, che è per ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="185ad-550">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="185ad-551">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="185ad-551">For more information, see the following resources:</span></span>

* [<span data-ttu-id="185ad-552">Panoramica di Application Insights</span><span class="sxs-lookup"><span data-stu-id="185ad-552">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="185ad-553">[Application Insights per applicazioni ASP.NET Core](/azure/azure-monitor/app/asp-net-core): iniziare da qui se si vuole implementare l'intervallo completo dei dati di telemetria di Application Insights insieme alla registrazione.</span><span class="sxs-lookup"><span data-stu-id="185ad-553">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="185ad-554">[ApplicationInsightsLoggerProvider per log ILogger .NET Core](/azure/azure-monitor/app/ilogger): iniziare da qui se si vuole implementare il provider di registrazione senza gli altri dati di telemetria di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="185ad-554">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="185ad-555">[Adattatori di registrazione di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="185ad-555">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="185ad-556">[Installare, configurare e inizializzare Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): esercitazione interattiva nel sito Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="185ad-556">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="185ad-557">Provider di registrazione di terze parti</span><span class="sxs-lookup"><span data-stu-id="185ad-557">Third-party logging providers</span></span>

<span data-ttu-id="185ad-558">Framework di registrazione di terze parti che usano ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="185ad-558">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="185ad-559">[elmah.io](https://elmah.io/) ([repository GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="185ad-559">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="185ad-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repository GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="185ad-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="185ad-561">[JSNLog](https://jsnlog.com/) ([repository GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="185ad-561">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="185ad-562">[KissLog.net](https://kisslog.net/) ([repository GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="185ad-562">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="185ad-563">[Log4net](https://logging.apache.org/log4net/) ([repository GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="185ad-563">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="185ad-564">[Loggr](https://loggr.net/) ([repository GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="185ad-564">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="185ad-565">[NLog](https://nlog-project.org/) ([repository GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="185ad-565">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="185ad-566">[Sentry](https://sentry.io/welcome/) ([repository GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="185ad-566">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="185ad-567">[Serilog](https://serilog.net/) ([repository GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="185ad-567">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="185ad-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repository Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="185ad-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="185ad-569">Alcuni framework di terze parti possono eseguire la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="185ad-569">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="185ad-570">L'uso di un framework di terze parti è simile a quello di uno dei provider predefiniti:</span><span class="sxs-lookup"><span data-stu-id="185ad-570">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="185ad-571">Aggiungere un pacchetto NuGet al progetto.</span><span class="sxs-lookup"><span data-stu-id="185ad-571">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="185ad-572">Chiamare un `ILoggerFactory` metodo di estensione fornito dal framework di registrazione.</span><span class="sxs-lookup"><span data-stu-id="185ad-572">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="185ad-573">Per altre informazioni, vedere la documentazione di ogni provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-573">For more information, see each provider's documentation.</span></span> <span data-ttu-id="185ad-574">I provider di registrazione di terze parti non sono supportati da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="185ad-574">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="185ad-575">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="185ad-575">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="185ad-576">Di [Tom Dykstra](https://github.com/tdykstra) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="185ad-576">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="185ad-577">.NET Core supporta un'API di registrazione che funziona con un'ampia gamma di provider di registrazione predefiniti e di terze parti.</span><span class="sxs-lookup"><span data-stu-id="185ad-577">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="185ad-578">Questo articolo illustra come usare l'API di registrazione con i provider predefiniti.</span><span class="sxs-lookup"><span data-stu-id="185ad-578">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="185ad-579">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="185ad-579">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="185ad-580">Aggiungere provider</span><span class="sxs-lookup"><span data-stu-id="185ad-580">Add providers</span></span>

<span data-ttu-id="185ad-581">Un provider di registrazione visualizza o archivia i log.</span><span class="sxs-lookup"><span data-stu-id="185ad-581">A logging provider displays or stores logs.</span></span> <span data-ttu-id="185ad-582">Il provider Console, ad esempio, visualizza i log nella console, mentre il provider Azure Application Insights li archivia in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="185ad-582">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="185ad-583">I log possono essere inviati a più destinazioni aggiungendo più provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-583">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="185ad-584">Per aggiungere un provider, chiamare il metodo di estensione `Add{provider name}` del provider in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="185ad-584">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="185ad-585">Il codice precedente richiede riferimenti a `Microsoft.Extensions.Logging` e `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="185ad-585">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="185ad-586">Il modello di progetto predefinito chiama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, che aggiunge i provider di registrazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="185ad-586">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="185ad-587">Console</span><span class="sxs-lookup"><span data-stu-id="185ad-587">Console</span></span>
* <span data-ttu-id="185ad-588">Debug</span><span class="sxs-lookup"><span data-stu-id="185ad-588">Debug</span></span>
* <span data-ttu-id="185ad-589">EventSource (a partire da ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="185ad-589">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="185ad-590">Se si usa `CreateDefaultBuilder`, è possibile sostituire i provider predefiniti con quelli di propria scelta.</span><span class="sxs-lookup"><span data-stu-id="185ad-590">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="185ad-591">Chiamare <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e aggiungere i provider desiderati.</span><span class="sxs-lookup"><span data-stu-id="185ad-591">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="185ad-592">Altre informazioni sui [provider di registrazione predefiniti](#built-in-logging-providers) e sui [provider di registrazione di terze parti](#third-party-logging-providers) vengono fornite più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="185ad-592">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="185ad-593">Creare log</span><span class="sxs-lookup"><span data-stu-id="185ad-593">Create logs</span></span>

<span data-ttu-id="185ad-594">Per creare i log, usare un oggetto <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="185ad-594">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="185ad-595">In un'app Web o in un servizio ospitato, ottenere un oggetto `ILogger` da un inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="185ad-595">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="185ad-596">Nelle app console non host, usare `LoggerFactory` per creare un oggetto `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="185ad-596">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="185ad-597">L'esempio seguente di ASP.NET Core crea un logger con `TodoApiSample.Pages.AboutModel` come categoria.</span><span class="sxs-lookup"><span data-stu-id="185ad-597">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="185ad-598">La *categoria* del log è una stringa associata a ogni log.</span><span class="sxs-lookup"><span data-stu-id="185ad-598">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="185ad-599">L'istanza di `ILogger<T>` fornita dall'inserimento delle dipendenze crea log con nome completo di tipo `T` come categoria.</span><span class="sxs-lookup"><span data-stu-id="185ad-599">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="185ad-600">Negli esempi seguenti di ASP.NET Core e app console, il logger viene usato per creare log con `Information` come livello.</span><span class="sxs-lookup"><span data-stu-id="185ad-600">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="185ad-601">Il *livello* del log indica la gravità dell'evento registrato.</span><span class="sxs-lookup"><span data-stu-id="185ad-601">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="185ad-602">[Livelli](#log-level) e [categorie](#log-category) vengono illustrati in modo dettagliato più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="185ad-602">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="185ad-603">Creare log nella classe Startup</span><span class="sxs-lookup"><span data-stu-id="185ad-603">Create logs in Startup</span></span>

<span data-ttu-id="185ad-604">Per scrivere log nella classe `Startup`, includere un parametro `ILogger` nella firma del costruttore:</span><span class="sxs-lookup"><span data-stu-id="185ad-604">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="185ad-605">Creare log nella classe Program</span><span class="sxs-lookup"><span data-stu-id="185ad-605">Create logs in the Program class</span></span>

<span data-ttu-id="185ad-606">Per scrivere log nella classe `Program`, ottenere un'istanza di `ILogger` dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="185ad-606">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="185ad-607">La registrazione durante la costruzione dell'host non è supportata direttamente.</span><span class="sxs-lookup"><span data-stu-id="185ad-607">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="185ad-608">Tuttavia, è possibile usare un logger separato.</span><span class="sxs-lookup"><span data-stu-id="185ad-608">However, a separate logger can be used.</span></span> <span data-ttu-id="185ad-609">Nell'esempio seguente viene usato un logger [Serilog](https://serilog.net/) per eseguire l'accesso `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="185ad-609">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="185ad-610">`AddSerilog`Usa la configurazione statica specificata in `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="185ad-610">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="185ad-611">Evitare l'uso di metodi logger asincroni</span><span class="sxs-lookup"><span data-stu-id="185ad-611">No asynchronous logger methods</span></span>

<span data-ttu-id="185ad-612">La registrazione deve essere così rapida da non giustificare l'impatto sulle prestazioni del codice asincrono.</span><span class="sxs-lookup"><span data-stu-id="185ad-612">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="185ad-613">Se l'archivio dati di registrazione è lento, non scrivere direttamente al suo interno.</span><span class="sxs-lookup"><span data-stu-id="185ad-613">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="185ad-614">Scrivere invece i messaggi di log prima in un archivio veloce e quindi spostarli nell'archivio lento in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="185ad-614">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="185ad-615">Ad esempio, se la registrazione viene eseguita in SQL Server, è preferibile non farlo direttamente in un metodo `Log`, poiché i metodi `Log` sono sincroni.</span><span class="sxs-lookup"><span data-stu-id="185ad-615">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="185ad-616">Al contrario, aggiungere i messaggi di log in modo sincrono a una coda in memoria e usare un ruolo di lavoro in background per eseguire il pull dei messaggi dalla coda per eseguire le operazioni asincrone di push dei dati in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="185ad-616">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="185ad-617">Per altre informazioni, vedere [questo](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problema di GitHub.</span><span class="sxs-lookup"><span data-stu-id="185ad-617">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="185ad-618">Configurazione</span><span class="sxs-lookup"><span data-stu-id="185ad-618">Configuration</span></span>

<span data-ttu-id="185ad-619">La configurazione dei provider di registrazione viene fornita da uno o più provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="185ad-619">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="185ad-620">Formati di file (INI, JSON e XML).</span><span class="sxs-lookup"><span data-stu-id="185ad-620">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="185ad-621">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="185ad-621">Command-line arguments.</span></span>
* <span data-ttu-id="185ad-622">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="185ad-622">Environment variables.</span></span>
* <span data-ttu-id="185ad-623">Oggetti .NET in memoria.</span><span class="sxs-lookup"><span data-stu-id="185ad-623">In-memory .NET objects.</span></span>
* <span data-ttu-id="185ad-624">Archiviazione con [Secret Manager](xref:security/app-secrets) senza crittografia.</span><span class="sxs-lookup"><span data-stu-id="185ad-624">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="185ad-625">Un archivio utente non crittografato, come [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="185ad-625">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="185ad-626">Provider personalizzati (installati o creati).</span><span class="sxs-lookup"><span data-stu-id="185ad-626">Custom providers (installed or created).</span></span>

<span data-ttu-id="185ad-627">Ad esempio, la configurazione di registrazione viene comunemente fornita dalla sezione `Logging` del file di impostazioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-627">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="185ad-628">L'esempio seguente mostra il contenuto di un file *appsettings.Development.json* tipico:</span><span class="sxs-lookup"><span data-stu-id="185ad-628">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="185ad-629">La proprietà `Logging` può avere le proprietà `LogLevel` e quella del provider di log (nell'esempio, Console).</span><span class="sxs-lookup"><span data-stu-id="185ad-629">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="185ad-630">La proprietà `LogLevel` in `Logging` specifica il [livello](#log-level) minimo per la registrazione per determinate categorie.</span><span class="sxs-lookup"><span data-stu-id="185ad-630">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="185ad-631">Nell'esempio, le categorie `System` e `Microsoft` eseguono la registrazione al livello `Information`, mentre tutte le altre la eseguono al livello `Debug`.</span><span class="sxs-lookup"><span data-stu-id="185ad-631">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="185ad-632">Altre proprietà in `Logging` specificano i provider di registrazione.</span><span class="sxs-lookup"><span data-stu-id="185ad-632">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="185ad-633">L'esempio si riferisce al provider Console.</span><span class="sxs-lookup"><span data-stu-id="185ad-633">The example is for the Console provider.</span></span> <span data-ttu-id="185ad-634">Se un provider supporta gli [ambiti](#log-scopes)del `IncludeScopes` log, indica se sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="185ad-634">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="185ad-635">Una proprietà del provider (ad esempio `Console` nell'esempio) può specificare anche una proprietà `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="185ad-635">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="185ad-636">`LogLevel` in un provider specifica i livelli di registrazione per tale provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-636">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="185ad-637">Se i livelli sono specificati in `Logging.{providername}.LogLevel`, eseguono l'override di eventuali valori impostati in `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="185ad-637">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="185ad-638">L'API di registrazione non include uno scenario per modificare i livelli di registrazione mentre un'app è in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="185ad-638">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="185ad-639">Alcuni provider di configurazione, tuttavia, sono in grado di ricaricare la configurazione, operazione che ha effetto immediato sulla configurazione della registrazione.</span><span class="sxs-lookup"><span data-stu-id="185ad-639">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="185ad-640">Ad esempio, il [provider di configurazione file](xref:fundamentals/configuration/index#file-configuration-provider), aggiunto da `CreateDefaultBuilder` per leggere i file di impostazioni, ricarica la configurazione di registrazione per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="185ad-640">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="185ad-641">Se la configurazione viene modificata nel codice mentre un'app è in esecuzione, l'app può chiamare [IConfigurationRoot. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) per aggiornare la configurazione di registrazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-641">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="185ad-642">Per informazioni sull'implementazione dei provider di configurazione, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="185ad-642">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="185ad-643">Esempio di output di registrazione</span><span class="sxs-lookup"><span data-stu-id="185ad-643">Sample logging output</span></span>

<span data-ttu-id="185ad-644">Con il codice di esempio illustrato nella sezione precedente i log vengono visualizzati nella console quando l'app viene eseguita dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="185ad-644">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="185ad-645">Ecco un esempio di output della console:</span><span class="sxs-lookup"><span data-stu-id="185ad-645">Here's an example of console output:</span></span>

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

<span data-ttu-id="185ad-646">I log precedenti sono stati generati inviando una richiesta HTTP Get all'app di esempio all'indirizzo `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="185ad-646">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="185ad-647">Ecco un esempio degli stessi log visualizzati nella finestra Debug quando si esegue l'app di esempio in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="185ad-647">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="185ad-648">I log creati dalle chiamate a `ILogger` illustrate nella sezione precedente iniziano con "TodoApi".</span><span class="sxs-lookup"><span data-stu-id="185ad-648">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="185ad-649">I log che iniziano con le categorie "Microsoft" provengono dal codice del framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="185ad-649">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="185ad-650">ASP.NET Core e il codice dell'applicazione usano la stessa API di registrazione e gli stessi provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-650">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="185ad-651">Il resto di questo articolo spiega alcuni dettagli e le opzioni per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="185ad-651">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="185ad-652">Pacchetti NuGet</span><span class="sxs-lookup"><span data-stu-id="185ad-652">NuGet packages</span></span>

<span data-ttu-id="185ad-653">Le interfacce `ILogger` e `ILoggerFactory` si trovano in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) e le loro implementazioni predefinite si trovano in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="185ad-653">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="185ad-654">Categoria di log</span><span class="sxs-lookup"><span data-stu-id="185ad-654">Log category</span></span>

<span data-ttu-id="185ad-655">Quando viene creato un oggetto `ILogger`, viene specificata la relativa *categoria*.</span><span class="sxs-lookup"><span data-stu-id="185ad-655">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="185ad-656">La categoria è inclusa in ogni messaggio di log creato da tale istanza di `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="185ad-656">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="185ad-657">La categoria può essere qualsiasi stringa, ma per convenzione si usa il nome della classe, ad esempio "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="185ad-657">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="185ad-658">Usare `ILogger<T>` per ottenere un'istanza di `ILogger` che usa il nome completo di tipo `T` come categoria:</span><span class="sxs-lookup"><span data-stu-id="185ad-658">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="185ad-659">Per specificare in modo esplicito la categoria, chiamare `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="185ad-659">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="185ad-660">L'uso di `ILogger<T>` equivale a chiamare `CreateLogger` con il nome completo di tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="185ad-660">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="185ad-661">Livello di registrazione</span><span class="sxs-lookup"><span data-stu-id="185ad-661">Log level</span></span>

<span data-ttu-id="185ad-662">Ogni log specifica un valore <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="185ad-662">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="185ad-663">Il livello di registrazione indica la gravità o l'importanza.</span><span class="sxs-lookup"><span data-stu-id="185ad-663">The log level indicates the severity or importance.</span></span> <span data-ttu-id="185ad-664">È ad esempio possibile scrivere un log `Information` quando un metodo termina normalmente e un log `Warning` quando un metodo restituisce un codice di stato *404 Non trovato*.</span><span class="sxs-lookup"><span data-stu-id="185ad-664">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="185ad-665">Il codice seguente crea i log `Information` e `Warning`:</span><span class="sxs-lookup"><span data-stu-id="185ad-665">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="185ad-666">Nel codice precedente il primo parametro è l'[ID dell'evento di log](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="185ad-666">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="185ad-667">Il secondo parametro è un modello di messaggio con segnaposto per i valori degli argomenti forniti dai parametri dei metodi rimanenti.</span><span class="sxs-lookup"><span data-stu-id="185ad-667">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="185ad-668">I parametri dei metodi sono descritti nella [sezione relativa al modello di messaggio](#log-message-template) più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="185ad-668">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="185ad-669">I metodi di registrazione che includono il livello nel nome del metodo (ad esempio `LogInformation` e `LogWarning`) sono [metodi di estensione per ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="185ad-669">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="185ad-670">Questi metodi chiamano un metodo `Log` che accetta un parametro `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="185ad-670">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="185ad-671">È possibile chiamare il metodo `Log` direttamente anziché chiamare uno di questi metodi di estensione, ma la sintassi è relativamente complessa.</span><span class="sxs-lookup"><span data-stu-id="185ad-671">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="185ad-672">Per altre informazioni, vedere <xref:Microsoft.Extensions.Logging.ILogger> e il [codice sorgente delle estensioni del logger](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="185ad-672">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="185ad-673">ASP.NET Core definisce i livelli di registrazione seguenti, ordinati dal meno grave al più grave.</span><span class="sxs-lookup"><span data-stu-id="185ad-673">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="185ad-674">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="185ad-674">Trace = 0</span></span>

  <span data-ttu-id="185ad-675">Per informazioni in genere utili solo per il debug.</span><span class="sxs-lookup"><span data-stu-id="185ad-675">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="185ad-676">Questi messaggi possono contenere dati sensibili dell'applicazione e pertanto non devono essere abilitati in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="185ad-676">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="185ad-677">*Disattivato per impostazione predefinita.*</span><span class="sxs-lookup"><span data-stu-id="185ad-677">*Disabled by default.*</span></span>

* <span data-ttu-id="185ad-678">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="185ad-678">Debug = 1</span></span>

  <span data-ttu-id="185ad-679">Per informazioni che possono essere utili per lo sviluppo e il debug.</span><span class="sxs-lookup"><span data-stu-id="185ad-679">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="185ad-680">Esempio: `Entering method Configure with flag set to true.` Abilitare i livelli di registrazione `Debug` nell'ambiente di produzione solo in fase di risoluzione dei problemi, per non fare aumentare eccessivamente il volume dei log.</span><span class="sxs-lookup"><span data-stu-id="185ad-680">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="185ad-681">Information = 2</span><span class="sxs-lookup"><span data-stu-id="185ad-681">Information = 2</span></span>

  <span data-ttu-id="185ad-682">Per tenere traccia del flusso generale dell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-682">For tracking the general flow of the app.</span></span> <span data-ttu-id="185ad-683">Queste registrazioni hanno in genere un valore a lungo termine.</span><span class="sxs-lookup"><span data-stu-id="185ad-683">These logs typically have some long-term value.</span></span> <span data-ttu-id="185ad-684">Esempio: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="185ad-684">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="185ad-685">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="185ad-685">Warning = 3</span></span>

  <span data-ttu-id="185ad-686">Per gli eventi imprevisti o anomali nel flusso dell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-686">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="185ad-687">Tali eventi potrebbero includere errori o altre condizioni che non provocano l'arresto dell'app, ma che potrebbe essere necessario analizzare.</span><span class="sxs-lookup"><span data-stu-id="185ad-687">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="185ad-688">Le eccezioni gestite sono una situazione comune in cui usare il livello di registrazione `Warning`.</span><span class="sxs-lookup"><span data-stu-id="185ad-688">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="185ad-689">Esempio: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="185ad-689">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="185ad-690">Error = 4</span><span class="sxs-lookup"><span data-stu-id="185ad-690">Error = 4</span></span>

  <span data-ttu-id="185ad-691">Per errori ed eccezioni che non possono essere gestiti.</span><span class="sxs-lookup"><span data-stu-id="185ad-691">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="185ad-692">Questi messaggi indicano un errore nell'operazione o nell'attività in corso (ad esempio la richiesta HTTP corrente), non un errore a livello di app.</span><span class="sxs-lookup"><span data-stu-id="185ad-692">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="185ad-693">Messaggio di registrazione di esempio:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="185ad-693">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="185ad-694">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="185ad-694">Critical = 5</span></span>

  <span data-ttu-id="185ad-695">Per gli errori che richiedono attenzione immediata.</span><span class="sxs-lookup"><span data-stu-id="185ad-695">For failures that require immediate attention.</span></span> <span data-ttu-id="185ad-696">Esempi: scenari di perdita di dati, spazio su disco insufficiente.</span><span class="sxs-lookup"><span data-stu-id="185ad-696">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="185ad-697">Usare il livello di registrazione per controllare la quantità di output di log scritto in un supporto di archiviazione specifico o in una finestra.</span><span class="sxs-lookup"><span data-stu-id="185ad-697">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="185ad-698">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="185ad-698">For example:</span></span>

* <span data-ttu-id="185ad-699">In produzione:</span><span class="sxs-lookup"><span data-stu-id="185ad-699">In production:</span></span>
  * <span data-ttu-id="185ad-700">La `Trace` registrazione ai livelli `Information` attraverso produce un volume elevato di messaggi di log dettagliati.</span><span class="sxs-lookup"><span data-stu-id="185ad-700">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="185ad-701">Per controllare i costi e non superare i limiti di archiviazione `Trace` dei `Information` dati, accedere ai messaggi di livello elevato a un archivio dati a basso costo.</span><span class="sxs-lookup"><span data-stu-id="185ad-701">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="185ad-702">L'accesso `Warning` ai `Critical` livelli in genere produce un minor numero di messaggi di log più piccoli.</span><span class="sxs-lookup"><span data-stu-id="185ad-702">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="185ad-703">Pertanto, i costi e i limiti di archiviazione in genere non rappresentano un problema, il che comporta una maggiore flessibilità di scelta dell'archivio dati.</span><span class="sxs-lookup"><span data-stu-id="185ad-703">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="185ad-704">Durante lo sviluppo:</span><span class="sxs-lookup"><span data-stu-id="185ad-704">During development:</span></span>
  * <span data-ttu-id="185ad-705">Consente `Warning` di `Critical` accedere ai messaggi nella console.</span><span class="sxs-lookup"><span data-stu-id="185ad-705">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="185ad-706">Aggiungere `Trace` `Information` messaggi durante la risoluzione dei problemi.</span><span class="sxs-lookup"><span data-stu-id="185ad-706">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="185ad-707">La sezione [Filtro dei log](#log-filtering) più avanti in questo articolo descrive come controllare quali livelli di registrazione gestisce un provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-707">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="185ad-708">ASP.NET Core scrive log per gli eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="185ad-708">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="185ad-709">Gli esempi di log illustrati in precedenza in questo articolo escludono i log al di sotto del livello `Information`, quindi non vengono creati log di livello `Debug` o `Trace`.</span><span class="sxs-lookup"><span data-stu-id="185ad-709">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="185ad-710">Ecco un esempio di log della console prodotti eseguendo l'app di esempio configurata in modo da mostrare i log di livello `Debug`:</span><span class="sxs-lookup"><span data-stu-id="185ad-710">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="185ad-711">ID evento di registrazione</span><span class="sxs-lookup"><span data-stu-id="185ad-711">Log event ID</span></span>

<span data-ttu-id="185ad-712">Ogni log può specificare un *ID evento*.</span><span class="sxs-lookup"><span data-stu-id="185ad-712">Each log can specify an *event ID*.</span></span> <span data-ttu-id="185ad-713">A tale scopo, l'app di esempio usa una classe `LoggingEvents` definita a livello locale:</span><span class="sxs-lookup"><span data-stu-id="185ad-713">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="185ad-714">Un ID evento associa un set di eventi.</span><span class="sxs-lookup"><span data-stu-id="185ad-714">An event ID associates a set of events.</span></span> <span data-ttu-id="185ad-715">Ad esempio, tutti i log correlati alla visualizzazione di un elenco di elementi in una pagina potrebbero essere indicati con 1001.</span><span class="sxs-lookup"><span data-stu-id="185ad-715">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="185ad-716">Il provider di log può archiviare l'ID evento in un campo ID o nel messaggio di registrazione oppure non archiviarlo.</span><span class="sxs-lookup"><span data-stu-id="185ad-716">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="185ad-717">Il provider Debug non visualizza gli ID evento.</span><span class="sxs-lookup"><span data-stu-id="185ad-717">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="185ad-718">Il provider Console visualizza gli ID evento tra parentesi quadre dopo la categoria:</span><span class="sxs-lookup"><span data-stu-id="185ad-718">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="185ad-719">Modello di messaggio di registrazione</span><span class="sxs-lookup"><span data-stu-id="185ad-719">Log message template</span></span>

<span data-ttu-id="185ad-720">Ogni log specifica un modello di messaggio.</span><span class="sxs-lookup"><span data-stu-id="185ad-720">Each log specifies a message template.</span></span> <span data-ttu-id="185ad-721">Il modello di messaggio può contenere segnaposto per i quali vengono forniti argomenti.</span><span class="sxs-lookup"><span data-stu-id="185ad-721">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="185ad-722">Usare nomi per i segnaposto, non numeri.</span><span class="sxs-lookup"><span data-stu-id="185ad-722">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="185ad-723">L'ordine dei segnaposto, non il loro nome, determina i parametri da usare per fornire i valori corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="185ad-723">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="185ad-724">Nel codice seguente, si noti che i nomi dei parametri non sono in sequenza nel modello di messaggio:</span><span class="sxs-lookup"><span data-stu-id="185ad-724">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="185ad-725">Questo codice crea un messaggio di log con i valori dei parametri in sequenza:</span><span class="sxs-lookup"><span data-stu-id="185ad-725">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="185ad-726">Il framework di registrazione funziona in questo modo per consentire ai provider di registrazione di implementare la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="185ad-726">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="185ad-727">Al sistema di registrazione vengono passati gli argomenti e non solo il modello di messaggio formattato.</span><span class="sxs-lookup"><span data-stu-id="185ad-727">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="185ad-728">Queste informazioni consentono ai provider di registrazione di archiviare i valori dei parametri come campi.</span><span class="sxs-lookup"><span data-stu-id="185ad-728">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="185ad-729">Si supponga, ad esempio, che il metodo logger esegua chiamate simili alla seguente:</span><span class="sxs-lookup"><span data-stu-id="185ad-729">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="185ad-730">Se si inviano i log all'archiviazione tabelle di Azure, ogni entità tabella di Azure può avere le proprietà `ID` e `RequestTime`, il che semplifica le query sui dati dei log.</span><span class="sxs-lookup"><span data-stu-id="185ad-730">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="185ad-731">Una query può trovare tutti i log entro un determinato intervallo `RequestTime` senza che sia necessario analizzare il messaggio di testo per determinare l'intervallo di tempo.</span><span class="sxs-lookup"><span data-stu-id="185ad-731">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="185ad-732">Registrazione delle eccezioni</span><span class="sxs-lookup"><span data-stu-id="185ad-732">Logging exceptions</span></span>

<span data-ttu-id="185ad-733">I metodi logger dispongono di overload che consentono di passare un'eccezione, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="185ad-733">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="185ad-734">Provider diversi gestiscono le informazioni sulle eccezioni in modi diversi.</span><span class="sxs-lookup"><span data-stu-id="185ad-734">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="185ad-735">Ecco un esempio di output del provider Debug dal codice sopra riportato.</span><span class="sxs-lookup"><span data-stu-id="185ad-735">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="185ad-736">Filtro del log</span><span class="sxs-lookup"><span data-stu-id="185ad-736">Log filtering</span></span>

<span data-ttu-id="185ad-737">È possibile specificare un livello di registrazione minimo per un provider e una categoria specifici oppure per tutti i provider o tutte le categorie.</span><span class="sxs-lookup"><span data-stu-id="185ad-737">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="185ad-738">Tutti i log sotto il livello minimo non sono passati al provider, quindi non vengono visualizzati o archiviati.</span><span class="sxs-lookup"><span data-stu-id="185ad-738">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="185ad-739">Per eliminare tutti i log, specificare `LogLevel.None` come livello di registrazione minimo.</span><span class="sxs-lookup"><span data-stu-id="185ad-739">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="185ad-740">Il valore intero di `LogLevel.None` è 6, maggiore di `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="185ad-740">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="185ad-741">Creare regole di filtro nella configurazione</span><span class="sxs-lookup"><span data-stu-id="185ad-741">Create filter rules in configuration</span></span>

<span data-ttu-id="185ad-742">Il codice del modello di `CreateDefaultBuilder` progetto chiama per impostare la registrazione per i provider console, debug e EventSource (ASP.NET Core 2,2 o versione successiva).</span><span class="sxs-lookup"><span data-stu-id="185ad-742">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="185ad-743">Il metodo `CreateDefaultBuilder` imposta inoltre la registrazione per la ricerca della configurazione in una sezione `Logging`, come illustrato [in precedenza in questo articolo](#configuration).</span><span class="sxs-lookup"><span data-stu-id="185ad-743">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="185ad-744">I dati di configurazione specificano i livelli di registrazione minimi in base al provider e alla categoria, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="185ad-744">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="185ad-745">Questo codice JSON crea sei regole di filtro, una per il provider Debug, quattro per il provider Console e una per tutti i provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-745">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="185ad-746">Quando viene creato un oggetto `ILogger`, viene scelta una singola regola per ogni provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-746">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="185ad-747">Regole di filtro nel codice</span><span class="sxs-lookup"><span data-stu-id="185ad-747">Filter rules in code</span></span>

<span data-ttu-id="185ad-748">L'esempio seguente illustra come registrare le regole di filtro nel codice:</span><span class="sxs-lookup"><span data-stu-id="185ad-748">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="185ad-749">Il secondo `AddFilter` specifica il provider Debug tramite il nome del tipo.</span><span class="sxs-lookup"><span data-stu-id="185ad-749">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="185ad-750">Il primo `AddFilter` si applica a tutti i provider in quanto non consente di specificare un tipo di provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-750">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="185ad-751">Applicazione delle regole di filtro</span><span class="sxs-lookup"><span data-stu-id="185ad-751">How filtering rules are applied</span></span>

<span data-ttu-id="185ad-752">I dati di configurazione e il codice `AddFilter` illustrato negli esempi precedenti creano le regole indicate nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="185ad-752">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="185ad-753">I primi sei provengono dall'esempio di configurazione e gli ultimi due dall'esempio di codice.</span><span class="sxs-lookup"><span data-stu-id="185ad-753">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="185ad-754">Number</span><span class="sxs-lookup"><span data-stu-id="185ad-754">Number</span></span> | <span data-ttu-id="185ad-755">Provider</span><span class="sxs-lookup"><span data-stu-id="185ad-755">Provider</span></span>      | <span data-ttu-id="185ad-756">Categorie che iniziano con...</span><span class="sxs-lookup"><span data-stu-id="185ad-756">Categories that begin with ...</span></span>          | <span data-ttu-id="185ad-757">Livello di registrazione minimo</span><span class="sxs-lookup"><span data-stu-id="185ad-757">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="185ad-758">1</span><span class="sxs-lookup"><span data-stu-id="185ad-758">1</span></span>      | <span data-ttu-id="185ad-759">Debug</span><span class="sxs-lookup"><span data-stu-id="185ad-759">Debug</span></span>         | <span data-ttu-id="185ad-760">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="185ad-760">All categories</span></span>                          | <span data-ttu-id="185ad-761">Informazioni</span><span class="sxs-lookup"><span data-stu-id="185ad-761">Information</span></span>       |
| <span data-ttu-id="185ad-762">2</span><span class="sxs-lookup"><span data-stu-id="185ad-762">2</span></span>      | <span data-ttu-id="185ad-763">Console</span><span class="sxs-lookup"><span data-stu-id="185ad-763">Console</span></span>       | <span data-ttu-id="185ad-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="185ad-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="185ad-765">Avviso</span><span class="sxs-lookup"><span data-stu-id="185ad-765">Warning</span></span>           |
| <span data-ttu-id="185ad-766">3</span><span class="sxs-lookup"><span data-stu-id="185ad-766">3</span></span>      | <span data-ttu-id="185ad-767">Console</span><span class="sxs-lookup"><span data-stu-id="185ad-767">Console</span></span>       | <span data-ttu-id="185ad-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="185ad-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="185ad-769">Debug</span><span class="sxs-lookup"><span data-stu-id="185ad-769">Debug</span></span>             |
| <span data-ttu-id="185ad-770">4</span><span class="sxs-lookup"><span data-stu-id="185ad-770">4</span></span>      | <span data-ttu-id="185ad-771">Console</span><span class="sxs-lookup"><span data-stu-id="185ad-771">Console</span></span>       | <span data-ttu-id="185ad-772">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="185ad-772">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="185ad-773">Errore</span><span class="sxs-lookup"><span data-stu-id="185ad-773">Error</span></span>             |
| <span data-ttu-id="185ad-774">5</span><span class="sxs-lookup"><span data-stu-id="185ad-774">5</span></span>      | <span data-ttu-id="185ad-775">Console</span><span class="sxs-lookup"><span data-stu-id="185ad-775">Console</span></span>       | <span data-ttu-id="185ad-776">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="185ad-776">All categories</span></span>                          | <span data-ttu-id="185ad-777">Informazioni</span><span class="sxs-lookup"><span data-stu-id="185ad-777">Information</span></span>       |
| <span data-ttu-id="185ad-778">6</span><span class="sxs-lookup"><span data-stu-id="185ad-778">6</span></span>      | <span data-ttu-id="185ad-779">Tutti i provider</span><span class="sxs-lookup"><span data-stu-id="185ad-779">All providers</span></span> | <span data-ttu-id="185ad-780">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="185ad-780">All categories</span></span>                          | <span data-ttu-id="185ad-781">Debug</span><span class="sxs-lookup"><span data-stu-id="185ad-781">Debug</span></span>             |
| <span data-ttu-id="185ad-782">7</span><span class="sxs-lookup"><span data-stu-id="185ad-782">7</span></span>      | <span data-ttu-id="185ad-783">Tutti i provider</span><span class="sxs-lookup"><span data-stu-id="185ad-783">All providers</span></span> | <span data-ttu-id="185ad-784">System</span><span class="sxs-lookup"><span data-stu-id="185ad-784">System</span></span>                                  | <span data-ttu-id="185ad-785">Debug</span><span class="sxs-lookup"><span data-stu-id="185ad-785">Debug</span></span>             |
| <span data-ttu-id="185ad-786">8</span><span class="sxs-lookup"><span data-stu-id="185ad-786">8</span></span>      | <span data-ttu-id="185ad-787">Debug</span><span class="sxs-lookup"><span data-stu-id="185ad-787">Debug</span></span>         | <span data-ttu-id="185ad-788">Microsoft</span><span class="sxs-lookup"><span data-stu-id="185ad-788">Microsoft</span></span>                               | <span data-ttu-id="185ad-789">Trace</span><span class="sxs-lookup"><span data-stu-id="185ad-789">Trace</span></span>             |

<span data-ttu-id="185ad-790">Quando viene creato un oggetto `ILogger`, l'oggetto `ILoggerFactory` seleziona una singola regola per ogni provider da applicare al logger.</span><span class="sxs-lookup"><span data-stu-id="185ad-790">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="185ad-791">Tutti i messaggi scritti da un'istanza di `ILogger` vengono filtrati in base alle regole selezionate.</span><span class="sxs-lookup"><span data-stu-id="185ad-791">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="185ad-792">Tra le regole disponibili viene selezionata la regola più specifica possibile per ogni coppia di categoria e provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-792">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="185ad-793">L'algoritmo seguente viene usato per ogni provider quando viene creato un `ILogger` per una determinata categoria:</span><span class="sxs-lookup"><span data-stu-id="185ad-793">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="185ad-794">Selezionare tutte le regole corrispondenti al provider o al relativo alias.</span><span class="sxs-lookup"><span data-stu-id="185ad-794">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="185ad-795">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole con un provider vuoto.</span><span class="sxs-lookup"><span data-stu-id="185ad-795">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="185ad-796">Dal risultato del passaggio precedente, selezionare le regole con il prefisso di categoria corrispondente più lungo.</span><span class="sxs-lookup"><span data-stu-id="185ad-796">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="185ad-797">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole che non specificano una categoria.</span><span class="sxs-lookup"><span data-stu-id="185ad-797">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="185ad-798">Se sono selezionate più regole, scegliere l'**ultima**.</span><span class="sxs-lookup"><span data-stu-id="185ad-798">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="185ad-799">Se non è selezionata alcuna regola, usare `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="185ad-799">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="185ad-800">Con l'elenco di regole precedente, si supponga di creare un oggetto `ILogger` per la categoria "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="185ad-800">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="185ad-801">Per il provider Debug si applicano le regole 1, 6 e 8.</span><span class="sxs-lookup"><span data-stu-id="185ad-801">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="185ad-802">La regola 8 è più specifica, così sarà quella selezionata.</span><span class="sxs-lookup"><span data-stu-id="185ad-802">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="185ad-803">Per il provider Console si applicano le regole 3, 4, 5 e 6.</span><span class="sxs-lookup"><span data-stu-id="185ad-803">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="185ad-804">La regola 3 è la più specifica.</span><span class="sxs-lookup"><span data-stu-id="185ad-804">Rule 3 is most specific.</span></span>

<span data-ttu-id="185ad-805">L'istanza di `ILogger` risultante invia i log di livello `Trace` o superiore al provider Debug.</span><span class="sxs-lookup"><span data-stu-id="185ad-805">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="185ad-806">I log di livello `Debug` o superiore vengono inviati al provider Console.</span><span class="sxs-lookup"><span data-stu-id="185ad-806">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="185ad-807">Alias dei provider</span><span class="sxs-lookup"><span data-stu-id="185ad-807">Provider aliases</span></span>

<span data-ttu-id="185ad-808">Ogni provider definisce un *alias* che può essere utilizzato nella configurazione al posto del nome completo di tipo.</span><span class="sxs-lookup"><span data-stu-id="185ad-808">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="185ad-809">Per i provider predefiniti, usare gli alias seguenti:</span><span class="sxs-lookup"><span data-stu-id="185ad-809">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="185ad-810">Console</span><span class="sxs-lookup"><span data-stu-id="185ad-810">Console</span></span>
* <span data-ttu-id="185ad-811">Debug</span><span class="sxs-lookup"><span data-stu-id="185ad-811">Debug</span></span>
* <span data-ttu-id="185ad-812">EventSource</span><span class="sxs-lookup"><span data-stu-id="185ad-812">EventSource</span></span>
* <span data-ttu-id="185ad-813">EventLog</span><span class="sxs-lookup"><span data-stu-id="185ad-813">EventLog</span></span>
* <span data-ttu-id="185ad-814">TraceSource</span><span class="sxs-lookup"><span data-stu-id="185ad-814">TraceSource</span></span>
* <span data-ttu-id="185ad-815">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="185ad-815">AzureAppServicesFile</span></span>
* <span data-ttu-id="185ad-816">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="185ad-816">AzureAppServicesBlob</span></span>
* <span data-ttu-id="185ad-817">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="185ad-817">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="185ad-818">Livello minimo predefinito</span><span class="sxs-lookup"><span data-stu-id="185ad-818">Default minimum level</span></span>

<span data-ttu-id="185ad-819">Esiste un'impostazione del livello minimo che diventa effettiva solo se non si applica alcuna regola di configurazione o codice per un provider e una categoria specifici.</span><span class="sxs-lookup"><span data-stu-id="185ad-819">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="185ad-820">L'esempio seguente illustra come impostare il livello minimo:</span><span class="sxs-lookup"><span data-stu-id="185ad-820">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="185ad-821">Se non si imposta in modo esplicito il livello minimo, il valore predefinito è `Information`, che indica che i log `Trace` e `Debug` vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="185ad-821">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="185ad-822">Funzioni di filtro</span><span class="sxs-lookup"><span data-stu-id="185ad-822">Filter functions</span></span>

<span data-ttu-id="185ad-823">Una funzione di filtro viene richiamata per tutti i provider e le categorie a cui non sono assegnate regole tramite la configurazione o il codice.</span><span class="sxs-lookup"><span data-stu-id="185ad-823">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="185ad-824">Il codice della funzione ha accesso al tipo di provider, alla categoria e al livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="185ad-824">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="185ad-825">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="185ad-825">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="185ad-826">Livelli e categorie di sistema</span><span class="sxs-lookup"><span data-stu-id="185ad-826">System categories and levels</span></span>

<span data-ttu-id="185ad-827">Di seguito sono elencate alcune categorie usate da ASP.NET Core ed Entity Framework Core, con note sui log previsti per ognuna:</span><span class="sxs-lookup"><span data-stu-id="185ad-827">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="185ad-828">Category</span><span class="sxs-lookup"><span data-stu-id="185ad-828">Category</span></span>                            | <span data-ttu-id="185ad-829">Note</span><span class="sxs-lookup"><span data-stu-id="185ad-829">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="185ad-830">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="185ad-830">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="185ad-831">Diagnostica generale di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="185ad-831">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="185ad-832">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="185ad-832">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="185ad-833">Chiavi considerate, trovate e usate.</span><span class="sxs-lookup"><span data-stu-id="185ad-833">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="185ad-834">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="185ad-834">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="185ad-835">Host consentiti.</span><span class="sxs-lookup"><span data-stu-id="185ad-835">Hosts allowed.</span></span> |
| <span data-ttu-id="185ad-836">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="185ad-836">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="185ad-837">Tempo impiegato per il completamento delle richieste HTTP e ora di inizio delle richieste.</span><span class="sxs-lookup"><span data-stu-id="185ad-837">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="185ad-838">Assembly di avvio dell'hosting caricati.</span><span class="sxs-lookup"><span data-stu-id="185ad-838">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="185ad-839">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="185ad-839">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="185ad-840">Diagnostica di MVC e Razor.</span><span class="sxs-lookup"><span data-stu-id="185ad-840">MVC and Razor diagnostics.</span></span> <span data-ttu-id="185ad-841">Associazione di modelli, esecuzione di filtri, compilazione di viste, selezione di azioni.</span><span class="sxs-lookup"><span data-stu-id="185ad-841">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="185ad-842">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="185ad-842">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="185ad-843">Informazioni sulla corrispondenza di route.</span><span class="sxs-lookup"><span data-stu-id="185ad-843">Route matching information.</span></span> |
| <span data-ttu-id="185ad-844">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="185ad-844">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="185ad-845">Risposte di avvio, arresto e keep-alive per la connessione.</span><span class="sxs-lookup"><span data-stu-id="185ad-845">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="185ad-846">Informazioni sui certificati HTTPS.</span><span class="sxs-lookup"><span data-stu-id="185ad-846">HTTPS certificate information.</span></span> |
| <span data-ttu-id="185ad-847">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="185ad-847">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="185ad-848">File forniti.</span><span class="sxs-lookup"><span data-stu-id="185ad-848">Files served.</span></span> |
| <span data-ttu-id="185ad-849">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="185ad-849">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="185ad-850">Diagnostica generale di Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="185ad-850">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="185ad-851">Attività e configurazione del database, rilevamento delle modifiche, migrazioni.</span><span class="sxs-lookup"><span data-stu-id="185ad-851">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="185ad-852">Ambiti dei log</span><span class="sxs-lookup"><span data-stu-id="185ad-852">Log scopes</span></span>

 <span data-ttu-id="185ad-853">Un *ambito* può raggruppare un set di operazioni logiche.</span><span class="sxs-lookup"><span data-stu-id="185ad-853">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="185ad-854">Questo raggruppamento può essere usato per collegare gli stessi dati a ogni log creato come parte di un set.</span><span class="sxs-lookup"><span data-stu-id="185ad-854">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="185ad-855">Ad esempio, ogni log creato come parte dell'elaborazione di una transazione può includere l'ID transazione.</span><span class="sxs-lookup"><span data-stu-id="185ad-855">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="185ad-856">Un ambito è un tipo `IDisposable` restituito dal metodo <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> e viene mantenuto fino all'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="185ad-856">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="185ad-857">Un ambito si usa mediante il wrapping delle chiamate del logger in un blocco `using`:</span><span class="sxs-lookup"><span data-stu-id="185ad-857">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="185ad-858">Il codice seguente abilita gli ambiti per il provider Console:</span><span class="sxs-lookup"><span data-stu-id="185ad-858">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="185ad-859">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="185ad-859">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="185ad-860">La configurazione dell'opzione del logger della console `IncludeScopes` è necessaria per abilitare la registrazione basata sull'ambito.</span><span class="sxs-lookup"><span data-stu-id="185ad-860">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="185ad-861">Per informazioni sulla configurazione, vedere la sezione [Configurazione](#configuration).</span><span class="sxs-lookup"><span data-stu-id="185ad-861">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="185ad-862">Ogni messaggio di registrazione include le informazioni sull'ambito:</span><span class="sxs-lookup"><span data-stu-id="185ad-862">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="185ad-863">Provider di registrazione predefiniti</span><span class="sxs-lookup"><span data-stu-id="185ad-863">Built-in logging providers</span></span>

<span data-ttu-id="185ad-864">ASP.NET Core include i provider seguenti:</span><span class="sxs-lookup"><span data-stu-id="185ad-864">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="185ad-865">Console</span><span class="sxs-lookup"><span data-stu-id="185ad-865">Console</span></span>](#console-provider)
* [<span data-ttu-id="185ad-866">Debug</span><span class="sxs-lookup"><span data-stu-id="185ad-866">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="185ad-867">EventSource</span><span class="sxs-lookup"><span data-stu-id="185ad-867">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="185ad-868">EventLog</span><span class="sxs-lookup"><span data-stu-id="185ad-868">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="185ad-869">TraceSource</span><span class="sxs-lookup"><span data-stu-id="185ad-869">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="185ad-870">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="185ad-870">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="185ad-871">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="185ad-871">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="185ad-872">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="185ad-872">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="185ad-873">Per informazioni su StdOut e la registrazione debug con il modulo ASP.NET Core, vedere <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="185ad-873">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="185ad-874">Provider Console</span><span class="sxs-lookup"><span data-stu-id="185ad-874">Console provider</span></span>

<span data-ttu-id="185ad-875">Il pacchetto del provider [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) invia l'output della registrazione alla console.</span><span class="sxs-lookup"><span data-stu-id="185ad-875">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="185ad-876">Per visualizzare l'output di registrazione del provider Console, aprire un prompt dei comandi nella cartella del progetto ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="185ad-876">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="185ad-877">Provider Debug</span><span class="sxs-lookup"><span data-stu-id="185ad-877">Debug provider</span></span>

<span data-ttu-id="185ad-878">Il pacchetto del provider [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) scrive l'output della registrazione usando la classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (chiamate del metodo `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="185ad-878">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="185ad-879">In Linux, questo provider scrive i log in */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="185ad-879">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="185ad-880">Provider origine evento</span><span class="sxs-lookup"><span data-stu-id="185ad-880">Event Source provider</span></span>

<span data-ttu-id="185ad-881">Il pacchetto del provider [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) scrive in un'origine evento multipiattaforma con il nome `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="185ad-881">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="185ad-882">In Windows, il provider utilizza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="185ad-882">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="185ad-883">Il provider di origine eventi viene aggiunto automaticamente `CreateDefaultBuilder` quando viene chiamato per compilare l'host.</span><span class="sxs-lookup"><span data-stu-id="185ad-883">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="185ad-884">Usare l' [utilità PerfView](https://github.com/Microsoft/perfview) per raccogliere e visualizzare i log.</span><span class="sxs-lookup"><span data-stu-id="185ad-884">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="185ad-885">Sono disponibili altri strumenti per la visualizzazione dei log ETW, ma PerfView fornisce un'esperienza ottimale per l'uso con gli eventi ETW generati da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="185ad-885">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="185ad-886">Per configurare PerfView per la raccolta degli eventi registrati da questo provider, aggiungere la stringa `*Microsoft-Extensions-Logging` nell'elenco **Provider aggiuntivi**</span><span class="sxs-lookup"><span data-stu-id="185ad-886">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="185ad-887">(non dimenticare l'asterisco all'inizio della stringa).</span><span class="sxs-lookup"><span data-stu-id="185ad-887">(Don't miss the asterisk at the start of the string.)</span></span>

![Provider aggiuntivi di PerfView](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="185ad-889">Provider EventLog di Windows</span><span class="sxs-lookup"><span data-stu-id="185ad-889">Windows EventLog provider</span></span>

<span data-ttu-id="185ad-890">Il pacchetto di provider [Microsoft.Extensions.Logging.AventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) invia l'output del log al registro eventi di Windows.</span><span class="sxs-lookup"><span data-stu-id="185ad-890">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="185ad-891">Gli [overload di AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) consentono di passare <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="185ad-891">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="185ad-892">Se `null` o non è specificato, vengono usate le impostazioni predefinite seguenti:</span><span class="sxs-lookup"><span data-stu-id="185ad-892">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="185ad-893">`LogName`&ndash; "Applicazione"</span><span class="sxs-lookup"><span data-stu-id="185ad-893">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="185ad-894">`SourceName`&ndash; "Runtime .NET"</span><span class="sxs-lookup"><span data-stu-id="185ad-894">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="185ad-895">`MachineName` &ndash; computer locale</span><span class="sxs-lookup"><span data-stu-id="185ad-895">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="185ad-896">Gli eventi vengono registrati per un [livello di avviso e un livello superiore](#log-level).</span><span class="sxs-lookup"><span data-stu-id="185ad-896">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="185ad-897">Per registrare eventi inferiori a `Warning`, impostare in modo esplicito il livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="185ad-897">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="185ad-898">Aggiungere ad esempio il codice seguente al file *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="185ad-898">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="185ad-899">Provider TraceSource</span><span class="sxs-lookup"><span data-stu-id="185ad-899">TraceSource provider</span></span>

<span data-ttu-id="185ad-900">Il pacchetto di provider [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa le librerie e i provider <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="185ad-900">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="185ad-901">Gli [overload di AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) consentono di passare un cambio di origine e un listener di traccia.</span><span class="sxs-lookup"><span data-stu-id="185ad-901">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="185ad-902">Per usare questo provider, un'app deve essere in esecuzione in .NET Framework (invece che in .NET Core).</span><span class="sxs-lookup"><span data-stu-id="185ad-902">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="185ad-903">Il provider può instradare i messaggi a diversi [listener](/dotnet/framework/debug-trace-profile/trace-listeners), come <xref:System.Diagnostics.TextWriterTraceListener>, usato nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="185ad-903">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="185ad-904">Provider del Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="185ad-904">Azure App Service provider</span></span>

<span data-ttu-id="185ad-905">Il pacchetto di provider [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) scrive i log in file di testo nel file system di un'app del Servizio app di Azure e nell'[archivio di BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in un account di archiviazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="185ad-905">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="185ad-906">Il pacchetto del provider non è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="185ad-906">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="185ad-907">Quando la destinazione è .NET Framework o il riferimento è il metapacchetto `Microsoft.AspNetCore.App`, aggiungere il pacchetto del provider al progetto.</span><span class="sxs-lookup"><span data-stu-id="185ad-907">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="185ad-908">Un overload di <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> consente di passare <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="185ad-908">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="185ad-909">L'oggetto impostazioni può eseguire l'override delle impostazioni predefinite, ad esempio il modello di output di registrazione, il nome di BLOB e il limite di dimensioni di file.</span><span class="sxs-lookup"><span data-stu-id="185ad-909">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="185ad-910">Il *modello di output* è un modello di messaggio che viene applicato a tutti i log in aggiunta a quanto specificato quando si chiama un metodo `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="185ad-910">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="185ad-911">Quando si distribuisce un'app del servizio app, l'applicazione applica le impostazioni della sezione [Log del servizio app](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) della pagina **Servizio app** del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="185ad-911">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="185ad-912">Quando le impostazioni seguenti vengono aggiornate, le modifiche hanno effetto immediatamente senza richiedere un riavvio o la ridistribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-912">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="185ad-913">**Registrazione applicazioni (file system)**</span><span class="sxs-lookup"><span data-stu-id="185ad-913">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="185ad-914">**Registrazione applicazione (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="185ad-914">**Application Logging (Blob)**</span></span>

<span data-ttu-id="185ad-915">Il percorso predefinito per i file di log è nella cartella *D:\\home\\LogFiles\\Application* e il nome di file predefinito è *diagnostics-aaaammgg.txt*.</span><span class="sxs-lookup"><span data-stu-id="185ad-915">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="185ad-916">Il limite predefinito per le dimensioni del file è 10 MB e il numero massimo predefinito di file conservati è 2.</span><span class="sxs-lookup"><span data-stu-id="185ad-916">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="185ad-917">Il nome BLOB predefinito è *{nome-app}{timestamp}/aaaa/mm/gg/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="185ad-917">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="185ad-918">Il provider funziona solo quando il progetto viene eseguito nell'ambiente di Azure.</span><span class="sxs-lookup"><span data-stu-id="185ad-918">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="185ad-919">Non ha alcun effetto quando il progetto viene eseguito localmente, in quanto non scrive nulla in file locali o in archivi di sviluppo locali per i BLOB.</span><span class="sxs-lookup"><span data-stu-id="185ad-919">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="185ad-920">Flusso di registrazione di Azure</span><span class="sxs-lookup"><span data-stu-id="185ad-920">Azure log streaming</span></span>

<span data-ttu-id="185ad-921">Il flusso di registrazione di Azure consente di visualizzare l'attività di registrazione in tempo reale da:</span><span class="sxs-lookup"><span data-stu-id="185ad-921">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="185ad-922">Server applicazioni</span><span class="sxs-lookup"><span data-stu-id="185ad-922">The app server</span></span>
* <span data-ttu-id="185ad-923">Server Web</span><span class="sxs-lookup"><span data-stu-id="185ad-923">The web server</span></span>
* <span data-ttu-id="185ad-924">Traccia delle richieste non riuscite</span><span class="sxs-lookup"><span data-stu-id="185ad-924">Failed request tracing</span></span>

<span data-ttu-id="185ad-925">Per configurare il flusso di registrazione di Azure:</span><span class="sxs-lookup"><span data-stu-id="185ad-925">To configure Azure log streaming:</span></span>

* <span data-ttu-id="185ad-926">Passare alla pagina **Log del servizio app** dalla pagina del portale dell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-926">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="185ad-927">Impostare **Registrazione applicazioni (file system)** su **Attiva**.</span><span class="sxs-lookup"><span data-stu-id="185ad-927">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="185ad-928">Scegliere il livello di registrazione in **Livello**.</span><span class="sxs-lookup"><span data-stu-id="185ad-928">Choose the log **Level**.</span></span> <span data-ttu-id="185ad-929">Questa impostazione è valida solo per lo streaming dei log di Azure, non per altri provider di registrazione nell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-929">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="185ad-930">Passare alla pagina **Flusso di registrazione** per visualizzare i messaggi dell'app.</span><span class="sxs-lookup"><span data-stu-id="185ad-930">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="185ad-931">I messaggi vengono registrati dall'app tramite l'interfaccia `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="185ad-931">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="185ad-932">Registrazione di traccia di Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="185ad-932">Azure Application Insights trace logging</span></span>

<span data-ttu-id="185ad-933">Il pacchetto di provider [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) scrive log in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="185ad-933">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="185ad-934">Application Insights è un servizio che monitora un'app Web e fornisce gli strumenti per l'esecuzione di query sui dati di telemetria e la loro analisi.</span><span class="sxs-lookup"><span data-stu-id="185ad-934">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="185ad-935">Se si usa questo provider, è possibile eseguire query sui log e analizzarli usando gli strumenti di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="185ad-935">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="185ad-936">Il provider di registrazione è incluso come dipendenza di [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), ovvero il pacchetto che fornisce tutti i dati di telemetria disponibili per ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="185ad-936">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="185ad-937">Se si usa questo pacchetto, non è necessario installare il pacchetto di provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-937">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="185ad-938">Non usare il pacchetto [](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)Microsoft.ApplicationInsights.Web&mdash;, che è per ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="185ad-938">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="185ad-939">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="185ad-939">For more information, see the following resources:</span></span>

* [<span data-ttu-id="185ad-940">Panoramica di Application Insights</span><span class="sxs-lookup"><span data-stu-id="185ad-940">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="185ad-941">[Application Insights per applicazioni ASP.NET Core](/azure/azure-monitor/app/asp-net-core): iniziare da qui se si vuole implementare l'intervallo completo dei dati di telemetria di Application Insights insieme alla registrazione.</span><span class="sxs-lookup"><span data-stu-id="185ad-941">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="185ad-942">[ApplicationInsightsLoggerProvider per log ILogger .NET Core](/azure/azure-monitor/app/ilogger): iniziare da qui se si vuole implementare il provider di registrazione senza gli altri dati di telemetria di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="185ad-942">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="185ad-943">[Adattatori di registrazione di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="185ad-943">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="185ad-944">[Installare, configurare e inizializzare Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): esercitazione interattiva nel sito Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="185ad-944">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="185ad-945">Provider di registrazione di terze parti</span><span class="sxs-lookup"><span data-stu-id="185ad-945">Third-party logging providers</span></span>

<span data-ttu-id="185ad-946">Framework di registrazione di terze parti che usano ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="185ad-946">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="185ad-947">[elmah.io](https://elmah.io/) ([repository GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="185ad-947">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="185ad-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repository GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="185ad-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="185ad-949">[JSNLog](https://jsnlog.com/) ([repository GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="185ad-949">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="185ad-950">[KissLog.net](https://kisslog.net/) ([repository GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="185ad-950">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="185ad-951">[Log4net](https://logging.apache.org/log4net/) ([repository GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="185ad-951">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="185ad-952">[Loggr](https://loggr.net/) ([repository GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="185ad-952">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="185ad-953">[NLog](https://nlog-project.org/) ([repository GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="185ad-953">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="185ad-954">[Sentry](https://sentry.io/welcome/) ([repository GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="185ad-954">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="185ad-955">[Serilog](https://serilog.net/) ([repository GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="185ad-955">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="185ad-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repository Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="185ad-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="185ad-957">Alcuni framework di terze parti possono eseguire la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="185ad-957">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="185ad-958">L'uso di un framework di terze parti è simile a quello di uno dei provider predefiniti:</span><span class="sxs-lookup"><span data-stu-id="185ad-958">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="185ad-959">Aggiungere un pacchetto NuGet al progetto.</span><span class="sxs-lookup"><span data-stu-id="185ad-959">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="185ad-960">Chiamare un `ILoggerFactory` metodo di estensione fornito dal framework di registrazione.</span><span class="sxs-lookup"><span data-stu-id="185ad-960">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="185ad-961">Per altre informazioni, vedere la documentazione di ogni provider.</span><span class="sxs-lookup"><span data-stu-id="185ad-961">For more information, see each provider's documentation.</span></span> <span data-ttu-id="185ad-962">I provider di registrazione di terze parti non sono supportati da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="185ad-962">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="185ad-963">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="185ad-963">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
