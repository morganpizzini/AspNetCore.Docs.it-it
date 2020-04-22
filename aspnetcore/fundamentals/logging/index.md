---
title: Registrazione in .NET Core e ASP.NET Core
author: rick-anderson
description: Informazioni su come usare il framework di registrazione fornito dal pacchetto NuGet Microsoft.Extensions.Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/logging/index
ms.openlocfilehash: b897d0d775da62a11f01a64f39b47b6c5abebc8b
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791568"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="5aae7-103">Registrazione in .NET Core e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5aae7-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5aae7-104">Di [Tom Dykstra](https://github.com/tdykstra) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5aae7-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5aae7-105">.NET Core supporta un'API di registrazione che funziona con un'ampia gamma di provider di registrazione predefiniti e di terze parti.</span><span class="sxs-lookup"><span data-stu-id="5aae7-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="5aae7-106">Questo articolo illustra come usare l'API di registrazione con i provider predefiniti.</span><span class="sxs-lookup"><span data-stu-id="5aae7-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="5aae7-107">La maggior parte degli esempi di codice illustrati in questo articolo proviene da app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5aae7-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="5aae7-108">Le parti specifiche della registrazione di questi frammenti di codice si applicano a qualsiasi app .NET Core che usa [l'host generico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="5aae7-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="5aae7-109">Per un esempio di come usare l'host generico in un'app non console<xref:fundamentals/host/hosted-services>Web, vedere il file *Program.cs* dell'app di esempio Attività in [background](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) ( ).</span><span class="sxs-lookup"><span data-stu-id="5aae7-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="5aae7-110">Il codice di registrazione per le app senza host generico differisce per il modo in cui vengono [aggiunti i provider](#add-providers) e [creati i logger](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="5aae7-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="5aae7-111">Gli esempi di codice non host sono illustrati nelle sezioni dell'articolo in cui sono riportate queste procedure.</span><span class="sxs-lookup"><span data-stu-id="5aae7-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="5aae7-112">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5aae7-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="5aae7-113">Aggiungere provider</span><span class="sxs-lookup"><span data-stu-id="5aae7-113">Add providers</span></span>

<span data-ttu-id="5aae7-114">Un provider di registrazione visualizza o archivia i log.</span><span class="sxs-lookup"><span data-stu-id="5aae7-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="5aae7-115">Il provider Console, ad esempio, visualizza i log nella console, mentre il provider Azure Application Insights li archivia in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="5aae7-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="5aae7-116">I log possono essere inviati a più destinazioni aggiungendo più provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="5aae7-117">Per aggiungere un provider in un'app che usa l'host generico, chiamare il metodo di estensione `Add{provider name}` del provider in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5aae7-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="5aae7-118">In un'app console non host chiamare il metodo di estensione `Add{provider name}` del provider durante la creazione di un oggetto `LoggerFactory`:</span><span class="sxs-lookup"><span data-stu-id="5aae7-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="5aae7-119">`LoggerFactory` e `AddConsole` richiedono un'istruzione `using` per `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="5aae7-120">I modelli di progetto ASP.NET Core predefiniti chiamano <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, che aggiunge i provider di registrazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="5aae7-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="5aae7-121">Console</span><span class="sxs-lookup"><span data-stu-id="5aae7-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="5aae7-122">Debug</span><span class="sxs-lookup"><span data-stu-id="5aae7-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="5aae7-123">Eventsource</span><span class="sxs-lookup"><span data-stu-id="5aae7-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="5aae7-124">[EventLog](#windows-eventlog-provider) (solo quando è in esecuzione in Windows)</span><span class="sxs-lookup"><span data-stu-id="5aae7-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="5aae7-125">È possibile sostituire i provider predefiniti con quelli di propria scelta.</span><span class="sxs-lookup"><span data-stu-id="5aae7-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="5aae7-126">Chiamare <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e aggiungere i provider desiderati.</span><span class="sxs-lookup"><span data-stu-id="5aae7-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="5aae7-127">Altre informazioni sui [provider di registrazione predefiniti](#built-in-logging-providers) e sui [provider di registrazione di terze parti](#third-party-logging-providers) vengono fornite più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="5aae7-128">Creare log</span><span class="sxs-lookup"><span data-stu-id="5aae7-128">Create logs</span></span>

<span data-ttu-id="5aae7-129">Per creare i log, usare un oggetto <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="5aae7-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="5aae7-130">In un'app Web o in un servizio ospitato, ottenere un oggetto `ILogger` da un inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="5aae7-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="5aae7-131">Nelle app console non host, usare `LoggerFactory` per creare un oggetto `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="5aae7-132">L'esempio seguente di ASP.NET Core crea un logger con `TodoApiSample.Pages.AboutModel` come categoria.</span><span class="sxs-lookup"><span data-stu-id="5aae7-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="5aae7-133">La *categoria* del log è una stringa associata a ogni log.</span><span class="sxs-lookup"><span data-stu-id="5aae7-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="5aae7-134">L'istanza di `ILogger<T>` fornita dall'inserimento delle dipendenze crea log con nome completo di tipo `T` come categoria.</span><span class="sxs-lookup"><span data-stu-id="5aae7-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="5aae7-135">L'esempio seguente di app console non host crea un logger con `LoggingConsoleApp.Program` come categoria.</span><span class="sxs-lookup"><span data-stu-id="5aae7-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="5aae7-136">Negli esempi seguenti di ASP.NET Core e app console, il logger viene usato per creare log con `Information` come livello.</span><span class="sxs-lookup"><span data-stu-id="5aae7-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="5aae7-137">Il *livello* del log indica la gravità dell'evento registrato.</span><span class="sxs-lookup"><span data-stu-id="5aae7-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="5aae7-138">[Livelli](#log-level) e [categorie](#log-category) vengono illustrati in modo dettagliato più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="5aae7-139">Creare log nella classe Program</span><span class="sxs-lookup"><span data-stu-id="5aae7-139">Create logs in the Program class</span></span>

<span data-ttu-id="5aae7-140">Per scrivere log nella classe `Program` di un'app ASP.NET Core, ottenere un'istanza di `ILogger` dall'inserimento delle dipendenze dopo aver creato l'host:</span><span class="sxs-lookup"><span data-stu-id="5aae7-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="5aae7-141">La registrazione durante la costruzione dell'host non è supportata direttamente.</span><span class="sxs-lookup"><span data-stu-id="5aae7-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="5aae7-142">Tuttavia, è possibile utilizzare un logger separato.</span><span class="sxs-lookup"><span data-stu-id="5aae7-142">However, a separate logger can be used.</span></span> <span data-ttu-id="5aae7-143">Nell'esempio seguente viene utilizzato un logger [Serilog](https://serilog.net/) per l'accesso `CreateHostBuilder`a .</span><span class="sxs-lookup"><span data-stu-id="5aae7-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="5aae7-144">`AddSerilog`utilizza la configurazione `Log.Logger`statica specificata in :</span><span class="sxs-lookup"><span data-stu-id="5aae7-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="5aae7-145">Creare log nella classe Startup</span><span class="sxs-lookup"><span data-stu-id="5aae7-145">Create logs in the Startup class</span></span>

<span data-ttu-id="5aae7-146">Per scrivere log nel metodo `Startup.Configure` di un'app ASP.NET Core, includere un parametro `ILogger` nella firma del metodo:</span><span class="sxs-lookup"><span data-stu-id="5aae7-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="5aae7-147">La scrittura di log prima del completamento della configurazione del contenitore di inserimento delle dipendenze nel metodo `Startup.ConfigureServices` non è supportata:</span><span class="sxs-lookup"><span data-stu-id="5aae7-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="5aae7-148">L'inserimento del logger nel costruttore `Startup` non è supportato.</span><span class="sxs-lookup"><span data-stu-id="5aae7-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="5aae7-149">L'inserimento del logger nella firma del metodo `Startup.ConfigureServices` non è supportato</span><span class="sxs-lookup"><span data-stu-id="5aae7-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="5aae7-150">Questa restrizione è dovuta al fatto che la registrazione dipende dall'inserimento delle dipendenze e dalla configurazione, che a sua volta dipende dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="5aae7-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="5aae7-151">Il contenitore di inserimento delle dipendenze non viene configurato finché il metodo `ConfigureServices` non è completato.</span><span class="sxs-lookup"><span data-stu-id="5aae7-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="5aae7-152">L'inserimento di un logger nel costruttore `Startup` è possibile nelle versioni precedenti di ASP.NET Core perché viene creato un contenitore di inserimento delle dipendenze separato per l'host Web.</span><span class="sxs-lookup"><span data-stu-id="5aae7-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="5aae7-153">Per informazioni sul motivo per cui viene creato un solo contenitore per l'host generico, vedere l'[annuncio relativo alle modifiche di rilievo](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="5aae7-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="5aae7-154">Se è necessario configurare un servizio che dipende da `ILogger<T>`, è comunque possibile eseguire questa operazione usando l'inserimento nel costruttore o specificando un metodo factory.</span><span class="sxs-lookup"><span data-stu-id="5aae7-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="5aae7-155">L'approccio con il metodo factory è consigliato solo se non sono disponibili altre opzioni.</span><span class="sxs-lookup"><span data-stu-id="5aae7-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="5aae7-156">Si supponga, ad esempio, di dover specificare una proprietà con un servizio ottenuto dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="5aae7-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="5aae7-157">Il precedente codice evidenziato è un oggetto `Func` che viene eseguito la prima volta che il contenitore di inserimento delle dipendenze deve creare un'istanza di `MyService`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="5aae7-158">È possibile accedere a uno dei servizi registrati in questo modo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-158">You can access any of the registered services in this way.</span></span>

### <a name="create-logs-in-blazor-webassembly"></a><span data-ttu-id="5aae7-159">Creare registri in Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="5aae7-159">Create logs in Blazor WebAssembly</span></span>

<span data-ttu-id="5aae7-160">Configurare la registrazione nelle app Blazor WebAssembly con la `WebAssemblyHostBuilder.Logging` proprietà in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="5aae7-160">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="5aae7-161">La `Logging` proprietà è <xref:Microsoft.Extensions.Logging.ILoggingBuilder>di tipo , pertanto <xref:Microsoft.Extensions.Logging.ILoggingBuilder> tutti i `Logging`metodi di estensione disponibili in .</span><span class="sxs-lookup"><span data-stu-id="5aae7-161">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="5aae7-162">Evitare l'uso di metodi logger asincroni</span><span class="sxs-lookup"><span data-stu-id="5aae7-162">No asynchronous logger methods</span></span>

<span data-ttu-id="5aae7-163">La registrazione deve essere così rapida da non giustificare l'impatto sulle prestazioni del codice asincrono.</span><span class="sxs-lookup"><span data-stu-id="5aae7-163">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="5aae7-164">Se l'archivio dati di registrazione è lento, non scrivere direttamente al suo interno.</span><span class="sxs-lookup"><span data-stu-id="5aae7-164">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="5aae7-165">Scrivere invece i messaggi di log prima in un archivio veloce e quindi spostarli nell'archivio lento in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="5aae7-165">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="5aae7-166">Ad esempio, se la registrazione viene eseguita in SQL Server, è preferibile non farlo direttamente in un metodo `Log`, poiché i metodi `Log` sono sincroni.</span><span class="sxs-lookup"><span data-stu-id="5aae7-166">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="5aae7-167">Al contrario, aggiungere i messaggi di log in modo sincrono a una coda in memoria e usare un ruolo di lavoro in background per eseguire il pull dei messaggi dalla coda per eseguire le operazioni asincrone di push dei dati in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="5aae7-167">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="5aae7-168">Per altre informazioni, vedere questo problema di GitHub.For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span><span class="sxs-lookup"><span data-stu-id="5aae7-168">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="5aae7-169">Configurazione</span><span class="sxs-lookup"><span data-stu-id="5aae7-169">Configuration</span></span>

<span data-ttu-id="5aae7-170">La configurazione dei provider di registrazione viene fornita da uno o più provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="5aae7-170">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="5aae7-171">Formati di file (INI, JSON e XML).</span><span class="sxs-lookup"><span data-stu-id="5aae7-171">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="5aae7-172">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="5aae7-172">Command-line arguments.</span></span>
* <span data-ttu-id="5aae7-173">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="5aae7-173">Environment variables.</span></span>
* <span data-ttu-id="5aae7-174">Oggetti .NET in memoria.</span><span class="sxs-lookup"><span data-stu-id="5aae7-174">In-memory .NET objects.</span></span>
* <span data-ttu-id="5aae7-175">Archiviazione con [Secret Manager](xref:security/app-secrets) senza crittografia.</span><span class="sxs-lookup"><span data-stu-id="5aae7-175">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="5aae7-176">Un archivio utente non crittografato, come [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="5aae7-176">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="5aae7-177">Provider personalizzati (installati o creati).</span><span class="sxs-lookup"><span data-stu-id="5aae7-177">Custom providers (installed or created).</span></span>

<span data-ttu-id="5aae7-178">Ad esempio, la configurazione di registrazione viene comunemente fornita dalla sezione `Logging` del file di impostazioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-178">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="5aae7-179">L'esempio seguente mostra il contenuto di un file *appsettings.Development.json* tipico:</span><span class="sxs-lookup"><span data-stu-id="5aae7-179">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="5aae7-180">La proprietà `Logging` può avere le proprietà `LogLevel` e quella del provider di log (nell'esempio, Console).</span><span class="sxs-lookup"><span data-stu-id="5aae7-180">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="5aae7-181">La proprietà `LogLevel` in `Logging` specifica il [livello](#log-level) minimo per la registrazione per determinate categorie.</span><span class="sxs-lookup"><span data-stu-id="5aae7-181">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="5aae7-182">Nell'esempio, le categorie `System` e `Microsoft` eseguono la registrazione al livello `Information`, mentre tutte le altre la eseguono al livello `Debug`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-182">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="5aae7-183">Altre proprietà in `Logging` specificano i provider di registrazione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-183">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="5aae7-184">L'esempio si riferisce al provider Console.</span><span class="sxs-lookup"><span data-stu-id="5aae7-184">The example is for the Console provider.</span></span> <span data-ttu-id="5aae7-185">Se un provider supporta gli `IncludeScopes` ambiti di [log](#log-scopes), indica se sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="5aae7-185">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="5aae7-186">Una proprietà del provider (ad esempio `Console` nell'esempio) può specificare anche una proprietà `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-186">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="5aae7-187">`LogLevel` in un provider specifica i livelli di registrazione per tale provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-187">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="5aae7-188">Se i livelli sono specificati in `Logging.{providername}.LogLevel`, eseguono l'override di eventuali valori impostati in `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-188">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="5aae7-189">L'API di registrazione non include uno scenario per modificare i livelli di log mentre un'app è in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-189">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="5aae7-190">Tuttavia, alcuni provider di configurazione sono in grado di ricaricare la configurazione, che ha effetto immediato sulla configurazione della registrazione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-190">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="5aae7-191">Ad esempio, il provider di configurazione `CreateDefaultBuilder` [file](xref:fundamentals/configuration/index#file-configuration-provider), che viene aggiunto da per leggere i file di impostazioni, ricarica la configurazione di registrazione per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="5aae7-191">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="5aae7-192">Se la configurazione viene modificata nel codice mentre un'app è in esecuzione, l'app può chiamare [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) per aggiornare la configurazione di registrazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-192">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="5aae7-193">Per informazioni sull'implementazione dei provider di configurazione, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="5aae7-193">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="5aae7-194">Esempio di output di registrazione</span><span class="sxs-lookup"><span data-stu-id="5aae7-194">Sample logging output</span></span>

<span data-ttu-id="5aae7-195">Con il codice di esempio illustrato nella sezione precedente i log vengono visualizzati nella console quando l'app viene eseguita dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="5aae7-195">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="5aae7-196">Ecco un esempio di output della console:</span><span class="sxs-lookup"><span data-stu-id="5aae7-196">Here's an example of console output:</span></span>

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

<span data-ttu-id="5aae7-197">I log precedenti sono stati generati inviando una richiesta HTTP Get all'app di esempio all'indirizzo `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-197">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="5aae7-198">Ecco un esempio degli stessi log visualizzati nella finestra Debug quando si esegue l'app di esempio in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="5aae7-198">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

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

<span data-ttu-id="5aae7-199">I log creati dalle chiamate a `ILogger` illustrate nella sezione precedente iniziano con "TodoApiSample".</span><span class="sxs-lookup"><span data-stu-id="5aae7-199">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="5aae7-200">I log che iniziano con le categorie "Microsoft" provengono dal codice del framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5aae7-200">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="5aae7-201">ASP.NET Core e il codice dell'applicazione usano la stessa API di registrazione e gli stessi provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-201">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="5aae7-202">Il resto di questo articolo spiega alcuni dettagli e le opzioni per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-202">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="5aae7-203">Pacchetti NuGet</span><span class="sxs-lookup"><span data-stu-id="5aae7-203">NuGet packages</span></span>

<span data-ttu-id="5aae7-204">Le interfacce `ILogger` e `ILoggerFactory` si trovano in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) e le loro implementazioni predefinite si trovano in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="5aae7-204">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="5aae7-205">Categoria di log</span><span class="sxs-lookup"><span data-stu-id="5aae7-205">Log category</span></span>

<span data-ttu-id="5aae7-206">Quando viene creato un oggetto `ILogger`, viene specificata la relativa *categoria*.</span><span class="sxs-lookup"><span data-stu-id="5aae7-206">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="5aae7-207">La categoria è inclusa in ogni messaggio di log creato da tale istanza di `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-207">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="5aae7-208">La categoria può essere qualsiasi stringa, ma per convenzione si usa il nome della classe, ad esempio "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="5aae7-208">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="5aae7-209">Usare `ILogger<T>` per ottenere un'istanza di `ILogger` che usa il nome completo di tipo `T` come categoria:</span><span class="sxs-lookup"><span data-stu-id="5aae7-209">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="5aae7-210">Per specificare in modo esplicito la categoria, chiamare `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="5aae7-210">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="5aae7-211">L'uso di `ILogger<T>` equivale a chiamare `CreateLogger` con il nome completo di tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-211">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="5aae7-212">Livello di registrazione</span><span class="sxs-lookup"><span data-stu-id="5aae7-212">Log level</span></span>

<span data-ttu-id="5aae7-213">Ogni log specifica un valore <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="5aae7-213">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="5aae7-214">Il livello di registrazione indica la gravità o l'importanza.</span><span class="sxs-lookup"><span data-stu-id="5aae7-214">The log level indicates the severity or importance.</span></span> <span data-ttu-id="5aae7-215">È ad esempio possibile scrivere un log `Information` quando un metodo termina normalmente e un log `Warning` quando un metodo restituisce un codice di stato *404 Non trovato*.</span><span class="sxs-lookup"><span data-stu-id="5aae7-215">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="5aae7-216">Il codice seguente crea i log `Information` e `Warning`:</span><span class="sxs-lookup"><span data-stu-id="5aae7-216">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="5aae7-217">Nel codice precedente il primo parametro è l'[ID dell'evento di log](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="5aae7-217">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="5aae7-218">Il secondo parametro è un modello di messaggio con segnaposto per i valori degli argomenti forniti dai parametri dei metodi rimanenti.</span><span class="sxs-lookup"><span data-stu-id="5aae7-218">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="5aae7-219">I parametri dei metodi sono descritti nella [sezione relativa al modello di messaggio](#log-message-template) più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-219">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="5aae7-220">I metodi di registrazione che includono il livello nel nome del metodo (ad esempio `LogInformation` e `LogWarning`) sono [metodi di estensione per ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="5aae7-220">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="5aae7-221">Questi metodi chiamano un metodo `Log` che accetta un parametro `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-221">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="5aae7-222">È possibile chiamare il metodo `Log` direttamente anziché chiamare uno di questi metodi di estensione, ma la sintassi è relativamente complessa.</span><span class="sxs-lookup"><span data-stu-id="5aae7-222">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="5aae7-223">Per altre informazioni, vedere <xref:Microsoft.Extensions.Logging.ILogger> e il [codice sorgente delle estensioni del logger](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="5aae7-223">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="5aae7-224">ASP.NET Core definisce i livelli di registrazione seguenti, ordinati dal meno grave al più grave.</span><span class="sxs-lookup"><span data-stu-id="5aae7-224">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="5aae7-225">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="5aae7-225">Trace = 0</span></span>

  <span data-ttu-id="5aae7-226">Per informazioni in genere utili solo per il debug.</span><span class="sxs-lookup"><span data-stu-id="5aae7-226">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="5aae7-227">Questi messaggi possono contenere dati sensibili dell'applicazione e pertanto non devono essere abilitati in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-227">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="5aae7-228">*Disattivato per impostazione predefinita.*</span><span class="sxs-lookup"><span data-stu-id="5aae7-228">*Disabled by default.*</span></span>

* <span data-ttu-id="5aae7-229">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="5aae7-229">Debug = 1</span></span>

  <span data-ttu-id="5aae7-230">Per informazioni che possono essere utili per lo sviluppo e il debug.</span><span class="sxs-lookup"><span data-stu-id="5aae7-230">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="5aae7-231">Esempio: `Entering method Configure with flag set to true.` Abilitare i livelli di registrazione `Debug` nell'ambiente di produzione solo in fase di risoluzione dei problemi, per non fare aumentare eccessivamente il volume dei log.</span><span class="sxs-lookup"><span data-stu-id="5aae7-231">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="5aae7-232">Information = 2</span><span class="sxs-lookup"><span data-stu-id="5aae7-232">Information = 2</span></span>

  <span data-ttu-id="5aae7-233">Per tenere traccia del flusso generale dell'app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-233">For tracking the general flow of the app.</span></span> <span data-ttu-id="5aae7-234">Queste registrazioni hanno in genere un valore a lungo termine.</span><span class="sxs-lookup"><span data-stu-id="5aae7-234">These logs typically have some long-term value.</span></span> <span data-ttu-id="5aae7-235">Esempio: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="5aae7-235">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="5aae7-236">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="5aae7-236">Warning = 3</span></span>

  <span data-ttu-id="5aae7-237">Per gli eventi imprevisti o anomali nel flusso dell'app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-237">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="5aae7-238">Tali eventi potrebbero includere errori o altre condizioni che non provocano l'arresto dell'app, ma che potrebbe essere necessario analizzare.</span><span class="sxs-lookup"><span data-stu-id="5aae7-238">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="5aae7-239">Le eccezioni gestite sono una situazione comune in cui usare il livello di registrazione `Warning`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-239">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="5aae7-240">Esempio: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="5aae7-240">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="5aae7-241">Error = 4</span><span class="sxs-lookup"><span data-stu-id="5aae7-241">Error = 4</span></span>

  <span data-ttu-id="5aae7-242">Per errori ed eccezioni che non possono essere gestiti.</span><span class="sxs-lookup"><span data-stu-id="5aae7-242">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="5aae7-243">Questi messaggi indicano un errore nell'operazione o nell'attività in corso (ad esempio la richiesta HTTP corrente), non un errore a livello di app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-243">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="5aae7-244">Messaggio di registrazione di esempio:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="5aae7-244">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="5aae7-245">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="5aae7-245">Critical = 5</span></span>

  <span data-ttu-id="5aae7-246">Per gli errori che richiedono attenzione immediata.</span><span class="sxs-lookup"><span data-stu-id="5aae7-246">For failures that require immediate attention.</span></span> <span data-ttu-id="5aae7-247">Esempi: scenari di perdita di dati, spazio su disco insufficiente.</span><span class="sxs-lookup"><span data-stu-id="5aae7-247">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="5aae7-248">Usare il livello di registrazione per controllare la quantità di output di log scritto in un supporto di archiviazione specifico o in una finestra.</span><span class="sxs-lookup"><span data-stu-id="5aae7-248">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="5aae7-249">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5aae7-249">For example:</span></span>

* <span data-ttu-id="5aae7-250">In produzione:</span><span class="sxs-lookup"><span data-stu-id="5aae7-250">In production:</span></span>
  * <span data-ttu-id="5aae7-251">La registrazione `Trace` `Information` ai livelli through produce un volume elevato di messaggi di log dettagliati.</span><span class="sxs-lookup"><span data-stu-id="5aae7-251">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="5aae7-252">Per controllare i costi e non `Trace` `Information` superare i limiti di archiviazione dei dati, eseguire il log-through dei messaggi di livello in un archivio dati con volumi elevati e a basso costo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-252">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="5aae7-253">La `Warning` registrazione `Critical` a livelli tramite livelli produce in genere un numero inferiore di messaggi di log più piccoli.</span><span class="sxs-lookup"><span data-stu-id="5aae7-253">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="5aae7-254">Pertanto, i costi e i limiti di archiviazione in genere non sono un problema, il che si traduce in una maggiore flessibilità di scelta dell'archivio dati.</span><span class="sxs-lookup"><span data-stu-id="5aae7-254">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="5aae7-255">Durante lo sviluppo:</span><span class="sxs-lookup"><span data-stu-id="5aae7-255">During development:</span></span>
  * <span data-ttu-id="5aae7-256">`Warning` Registrare `Critical` i messaggi nella console.</span><span class="sxs-lookup"><span data-stu-id="5aae7-256">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="5aae7-257">Aggiungere `Trace` `Information` i messaggi durante la risoluzione dei problemi.</span><span class="sxs-lookup"><span data-stu-id="5aae7-257">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="5aae7-258">La sezione [Filtro dei log](#log-filtering) più avanti in questo articolo descrive come controllare quali livelli di registrazione gestisce un provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-258">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="5aae7-259">ASP.NET Core scrive log per gli eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="5aae7-259">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="5aae7-260">Gli esempi di log illustrati in precedenza in questo articolo escludono i log al di sotto del livello `Information`, quindi non vengono creati log di livello `Debug` o `Trace`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-260">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="5aae7-261">Ecco un esempio di log della console prodotti eseguendo l'app di esempio configurata in modo da mostrare i log di livello `Debug`:</span><span class="sxs-lookup"><span data-stu-id="5aae7-261">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="5aae7-262">ID evento di registrazione</span><span class="sxs-lookup"><span data-stu-id="5aae7-262">Log event ID</span></span>

<span data-ttu-id="5aae7-263">Ogni log può specificare un *ID evento*.</span><span class="sxs-lookup"><span data-stu-id="5aae7-263">Each log can specify an *event ID*.</span></span> <span data-ttu-id="5aae7-264">A tale scopo, l'app di esempio usa una classe `LoggingEvents` definita a livello locale:</span><span class="sxs-lookup"><span data-stu-id="5aae7-264">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="5aae7-265">Un ID evento associa un set di eventi.</span><span class="sxs-lookup"><span data-stu-id="5aae7-265">An event ID associates a set of events.</span></span> <span data-ttu-id="5aae7-266">Ad esempio, tutti i log correlati alla visualizzazione di un elenco di elementi in una pagina potrebbero essere indicati con 1001.</span><span class="sxs-lookup"><span data-stu-id="5aae7-266">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="5aae7-267">Il provider di log può archiviare l'ID evento in un campo ID o nel messaggio di registrazione oppure non archiviarlo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-267">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="5aae7-268">Il provider Debug non visualizza gli ID evento.</span><span class="sxs-lookup"><span data-stu-id="5aae7-268">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="5aae7-269">Il provider Console visualizza gli ID evento tra parentesi quadre dopo la categoria:</span><span class="sxs-lookup"><span data-stu-id="5aae7-269">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="5aae7-270">Modello di messaggio di registrazione</span><span class="sxs-lookup"><span data-stu-id="5aae7-270">Log message template</span></span>

<span data-ttu-id="5aae7-271">Ogni log specifica un modello di messaggio.</span><span class="sxs-lookup"><span data-stu-id="5aae7-271">Each log specifies a message template.</span></span> <span data-ttu-id="5aae7-272">Il modello di messaggio può contenere segnaposto per i quali vengono forniti argomenti.</span><span class="sxs-lookup"><span data-stu-id="5aae7-272">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="5aae7-273">Usare nomi per i segnaposto, non numeri.</span><span class="sxs-lookup"><span data-stu-id="5aae7-273">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="5aae7-274">L'ordine dei segnaposto, non il loro nome, determina i parametri da usare per fornire i valori corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="5aae7-274">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="5aae7-275">Nel codice seguente, si noti che i nomi dei parametri non sono in sequenza nel modello di messaggio:</span><span class="sxs-lookup"><span data-stu-id="5aae7-275">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="5aae7-276">Questo codice crea un messaggio di log con i valori dei parametri in sequenza:</span><span class="sxs-lookup"><span data-stu-id="5aae7-276">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="5aae7-277">Il framework di registrazione funziona in questo modo per consentire ai provider di registrazione di implementare la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="5aae7-277">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="5aae7-278">Al sistema di registrazione vengono passati gli argomenti e non solo il modello di messaggio formattato.</span><span class="sxs-lookup"><span data-stu-id="5aae7-278">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="5aae7-279">Queste informazioni consentono ai provider di registrazione di archiviare i valori dei parametri come campi.</span><span class="sxs-lookup"><span data-stu-id="5aae7-279">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="5aae7-280">Si supponga, ad esempio, che il metodo logger esegua chiamate simili alla seguente:</span><span class="sxs-lookup"><span data-stu-id="5aae7-280">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="5aae7-281">Se si inviano i log all'archiviazione tabelle di Azure, ogni entità tabella di Azure può avere le proprietà `ID` e `RequestTime`, il che semplifica le query sui dati dei log.</span><span class="sxs-lookup"><span data-stu-id="5aae7-281">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="5aae7-282">Una query può trovare tutti i log entro un determinato intervallo `RequestTime` senza che sia necessario analizzare il messaggio di testo per determinare l'intervallo di tempo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-282">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="5aae7-283">Registrazione delle eccezioni</span><span class="sxs-lookup"><span data-stu-id="5aae7-283">Logging exceptions</span></span>

<span data-ttu-id="5aae7-284">I metodi logger dispongono di overload che consentono di passare un'eccezione, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="5aae7-284">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="5aae7-285">Provider diversi gestiscono le informazioni sulle eccezioni in modi diversi.</span><span class="sxs-lookup"><span data-stu-id="5aae7-285">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="5aae7-286">Ecco un esempio di output del provider Debug dal codice sopra riportato.</span><span class="sxs-lookup"><span data-stu-id="5aae7-286">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="5aae7-287">Filtro del log</span><span class="sxs-lookup"><span data-stu-id="5aae7-287">Log filtering</span></span>

<span data-ttu-id="5aae7-288">È possibile specificare un livello di registrazione minimo per un provider e una categoria specifici oppure per tutti i provider o tutte le categorie.</span><span class="sxs-lookup"><span data-stu-id="5aae7-288">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="5aae7-289">Tutti i log sotto il livello minimo non sono passati al provider, quindi non vengono visualizzati o archiviati.</span><span class="sxs-lookup"><span data-stu-id="5aae7-289">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="5aae7-290">Per eliminare tutti i log, specificare `LogLevel.None` come livello di registrazione minimo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-290">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="5aae7-291">Il valore intero di `LogLevel.None` è 6, maggiore di `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="5aae7-291">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="5aae7-292">Creare regole di filtro nella configurazione</span><span class="sxs-lookup"><span data-stu-id="5aae7-292">Create filter rules in configuration</span></span>

<span data-ttu-id="5aae7-293">Il codice del `CreateDefaultBuilder` modello di progetto chiama per impostare la registrazione per i provider Console, Debug e EventSource (ASP.NET Core 2.2 o versioni successive).</span><span class="sxs-lookup"><span data-stu-id="5aae7-293">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="5aae7-294">Il metodo `CreateDefaultBuilder` imposta inoltre la registrazione per la ricerca della configurazione in una sezione `Logging`, come illustrato [in precedenza in questo articolo](#configuration).</span><span class="sxs-lookup"><span data-stu-id="5aae7-294">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="5aae7-295">I dati di configurazione specificano i livelli di registrazione minimi in base al provider e alla categoria, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="5aae7-295">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="5aae7-296">Questo codice JSON crea sei regole di filtro, una per il provider Debug, quattro per il provider Console e una per tutti i provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-296">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="5aae7-297">Quando viene creato un oggetto `ILogger`, viene scelta una singola regola per ogni provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-297">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="5aae7-298">Regole di filtro nel codice</span><span class="sxs-lookup"><span data-stu-id="5aae7-298">Filter rules in code</span></span>

<span data-ttu-id="5aae7-299">L'esempio seguente illustra come registrare le regole di filtro nel codice:</span><span class="sxs-lookup"><span data-stu-id="5aae7-299">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="5aae7-300">Il secondo `AddFilter` specifica il provider Debug tramite il nome del tipo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-300">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="5aae7-301">Il primo `AddFilter` si applica a tutti i provider in quanto non consente di specificare un tipo di provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-301">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="5aae7-302">Applicazione delle regole di filtro</span><span class="sxs-lookup"><span data-stu-id="5aae7-302">How filtering rules are applied</span></span>

<span data-ttu-id="5aae7-303">I dati di configurazione e il codice `AddFilter` illustrato negli esempi precedenti creano le regole indicate nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="5aae7-303">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="5aae7-304">I primi sei provengono dall'esempio di configurazione e gli ultimi due dall'esempio di codice.</span><span class="sxs-lookup"><span data-stu-id="5aae7-304">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="5aae7-305">Number</span><span class="sxs-lookup"><span data-stu-id="5aae7-305">Number</span></span> | <span data-ttu-id="5aae7-306">Provider</span><span class="sxs-lookup"><span data-stu-id="5aae7-306">Provider</span></span>      | <span data-ttu-id="5aae7-307">Categorie che iniziano con...</span><span class="sxs-lookup"><span data-stu-id="5aae7-307">Categories that begin with ...</span></span>          | <span data-ttu-id="5aae7-308">Livello di registrazione minimo</span><span class="sxs-lookup"><span data-stu-id="5aae7-308">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="5aae7-309">1</span><span class="sxs-lookup"><span data-stu-id="5aae7-309">1</span></span>      | <span data-ttu-id="5aae7-310">Debug</span><span class="sxs-lookup"><span data-stu-id="5aae7-310">Debug</span></span>         | <span data-ttu-id="5aae7-311">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="5aae7-311">All categories</span></span>                          | <span data-ttu-id="5aae7-312">Informazioni</span><span class="sxs-lookup"><span data-stu-id="5aae7-312">Information</span></span>       |
| <span data-ttu-id="5aae7-313">2</span><span class="sxs-lookup"><span data-stu-id="5aae7-313">2</span></span>      | <span data-ttu-id="5aae7-314">Console</span><span class="sxs-lookup"><span data-stu-id="5aae7-314">Console</span></span>       | <span data-ttu-id="5aae7-315">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="5aae7-315">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="5aae7-316">Avviso</span><span class="sxs-lookup"><span data-stu-id="5aae7-316">Warning</span></span>           |
| <span data-ttu-id="5aae7-317">3</span><span class="sxs-lookup"><span data-stu-id="5aae7-317">3</span></span>      | <span data-ttu-id="5aae7-318">Console</span><span class="sxs-lookup"><span data-stu-id="5aae7-318">Console</span></span>       | <span data-ttu-id="5aae7-319">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="5aae7-319">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="5aae7-320">Debug</span><span class="sxs-lookup"><span data-stu-id="5aae7-320">Debug</span></span>             |
| <span data-ttu-id="5aae7-321">4</span><span class="sxs-lookup"><span data-stu-id="5aae7-321">4</span></span>      | <span data-ttu-id="5aae7-322">Console</span><span class="sxs-lookup"><span data-stu-id="5aae7-322">Console</span></span>       | <span data-ttu-id="5aae7-323">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="5aae7-323">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="5aae7-324">Errore</span><span class="sxs-lookup"><span data-stu-id="5aae7-324">Error</span></span>             |
| <span data-ttu-id="5aae7-325">5</span><span class="sxs-lookup"><span data-stu-id="5aae7-325">5</span></span>      | <span data-ttu-id="5aae7-326">Console</span><span class="sxs-lookup"><span data-stu-id="5aae7-326">Console</span></span>       | <span data-ttu-id="5aae7-327">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="5aae7-327">All categories</span></span>                          | <span data-ttu-id="5aae7-328">Informazioni</span><span class="sxs-lookup"><span data-stu-id="5aae7-328">Information</span></span>       |
| <span data-ttu-id="5aae7-329">6</span><span class="sxs-lookup"><span data-stu-id="5aae7-329">6</span></span>      | <span data-ttu-id="5aae7-330">Tutti i provider</span><span class="sxs-lookup"><span data-stu-id="5aae7-330">All providers</span></span> | <span data-ttu-id="5aae7-331">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="5aae7-331">All categories</span></span>                          | <span data-ttu-id="5aae7-332">Debug</span><span class="sxs-lookup"><span data-stu-id="5aae7-332">Debug</span></span>             |
| <span data-ttu-id="5aae7-333">7</span><span class="sxs-lookup"><span data-stu-id="5aae7-333">7</span></span>      | <span data-ttu-id="5aae7-334">Tutti i provider</span><span class="sxs-lookup"><span data-stu-id="5aae7-334">All providers</span></span> | <span data-ttu-id="5aae7-335">Sistema</span><span class="sxs-lookup"><span data-stu-id="5aae7-335">System</span></span>                                  | <span data-ttu-id="5aae7-336">Debug</span><span class="sxs-lookup"><span data-stu-id="5aae7-336">Debug</span></span>             |
| <span data-ttu-id="5aae7-337">8</span><span class="sxs-lookup"><span data-stu-id="5aae7-337">8</span></span>      | <span data-ttu-id="5aae7-338">Debug</span><span class="sxs-lookup"><span data-stu-id="5aae7-338">Debug</span></span>         | <span data-ttu-id="5aae7-339">Microsoft</span><span class="sxs-lookup"><span data-stu-id="5aae7-339">Microsoft</span></span>                               | <span data-ttu-id="5aae7-340">Trace</span><span class="sxs-lookup"><span data-stu-id="5aae7-340">Trace</span></span>             |

<span data-ttu-id="5aae7-341">Quando viene creato un oggetto `ILogger`, l'oggetto `ILoggerFactory` seleziona una singola regola per ogni provider da applicare al logger.</span><span class="sxs-lookup"><span data-stu-id="5aae7-341">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="5aae7-342">Tutti i messaggi scritti da un'istanza di `ILogger` vengono filtrati in base alle regole selezionate.</span><span class="sxs-lookup"><span data-stu-id="5aae7-342">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="5aae7-343">Tra le regole disponibili viene selezionata la regola più specifica possibile per ogni coppia di categoria e provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-343">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="5aae7-344">L'algoritmo seguente viene usato per ogni provider quando viene creato un `ILogger` per una determinata categoria:</span><span class="sxs-lookup"><span data-stu-id="5aae7-344">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="5aae7-345">Selezionare tutte le regole corrispondenti al provider o al relativo alias.</span><span class="sxs-lookup"><span data-stu-id="5aae7-345">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="5aae7-346">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole con un provider vuoto.</span><span class="sxs-lookup"><span data-stu-id="5aae7-346">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="5aae7-347">Dal risultato del passaggio precedente, selezionare le regole con il prefisso di categoria corrispondente più lungo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-347">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="5aae7-348">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole che non specificano una categoria.</span><span class="sxs-lookup"><span data-stu-id="5aae7-348">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="5aae7-349">Se sono selezionate più regole, scegliere l'**ultima**.</span><span class="sxs-lookup"><span data-stu-id="5aae7-349">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="5aae7-350">Se non è selezionata alcuna regola, usare `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-350">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="5aae7-351">Con l'elenco di regole precedente, si supponga di creare un oggetto `ILogger` per la categoria "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="5aae7-351">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="5aae7-352">Per il provider Debug si applicano le regole 1, 6 e 8.</span><span class="sxs-lookup"><span data-stu-id="5aae7-352">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="5aae7-353">La regola 8 è più specifica, così sarà quella selezionata.</span><span class="sxs-lookup"><span data-stu-id="5aae7-353">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="5aae7-354">Per il provider Console si applicano le regole 3, 4, 5 e 6.</span><span class="sxs-lookup"><span data-stu-id="5aae7-354">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="5aae7-355">La regola 3 è la più specifica.</span><span class="sxs-lookup"><span data-stu-id="5aae7-355">Rule 3 is most specific.</span></span>

<span data-ttu-id="5aae7-356">L'istanza di `ILogger` risultante invia i log di livello `Trace` o superiore al provider Debug.</span><span class="sxs-lookup"><span data-stu-id="5aae7-356">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="5aae7-357">I log di livello `Debug` o superiore vengono inviati al provider Console.</span><span class="sxs-lookup"><span data-stu-id="5aae7-357">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="5aae7-358">Alias dei provider</span><span class="sxs-lookup"><span data-stu-id="5aae7-358">Provider aliases</span></span>

<span data-ttu-id="5aae7-359">Ogni provider definisce un *alias* che può essere utilizzato nella configurazione al posto del nome completo di tipo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-359">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="5aae7-360">Per i provider predefiniti, usare gli alias seguenti:</span><span class="sxs-lookup"><span data-stu-id="5aae7-360">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="5aae7-361">Console</span><span class="sxs-lookup"><span data-stu-id="5aae7-361">Console</span></span>
* <span data-ttu-id="5aae7-362">Debug</span><span class="sxs-lookup"><span data-stu-id="5aae7-362">Debug</span></span>
* <span data-ttu-id="5aae7-363">EventSource</span><span class="sxs-lookup"><span data-stu-id="5aae7-363">EventSource</span></span>
* <span data-ttu-id="5aae7-364">EventLog</span><span class="sxs-lookup"><span data-stu-id="5aae7-364">EventLog</span></span>
* <span data-ttu-id="5aae7-365">TraceSource</span><span class="sxs-lookup"><span data-stu-id="5aae7-365">TraceSource</span></span>
* <span data-ttu-id="5aae7-366">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="5aae7-366">AzureAppServicesFile</span></span>
* <span data-ttu-id="5aae7-367">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="5aae7-367">AzureAppServicesBlob</span></span>
* <span data-ttu-id="5aae7-368">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="5aae7-368">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="5aae7-369">Livello minimo predefinito</span><span class="sxs-lookup"><span data-stu-id="5aae7-369">Default minimum level</span></span>

<span data-ttu-id="5aae7-370">Esiste un'impostazione del livello minimo che diventa effettiva solo se non si applica alcuna regola di configurazione o codice per un provider e una categoria specifici.</span><span class="sxs-lookup"><span data-stu-id="5aae7-370">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="5aae7-371">L'esempio seguente illustra come impostare il livello minimo:</span><span class="sxs-lookup"><span data-stu-id="5aae7-371">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="5aae7-372">Se non si imposta in modo esplicito il livello minimo, il valore predefinito è `Information`, che indica che i log `Trace` e `Debug` vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="5aae7-372">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="5aae7-373">Funzioni di filtro</span><span class="sxs-lookup"><span data-stu-id="5aae7-373">Filter functions</span></span>

<span data-ttu-id="5aae7-374">Una funzione di filtro viene richiamata per tutti i provider e le categorie a cui non sono assegnate regole tramite la configurazione o il codice.</span><span class="sxs-lookup"><span data-stu-id="5aae7-374">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="5aae7-375">Il codice della funzione ha accesso al tipo di provider, alla categoria e al livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-375">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="5aae7-376">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5aae7-376">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="5aae7-377">Livelli e categorie di sistema</span><span class="sxs-lookup"><span data-stu-id="5aae7-377">System categories and levels</span></span>

<span data-ttu-id="5aae7-378">Di seguito sono elencate alcune categorie usate da ASP.NET Core ed Entity Framework Core, con note sui log previsti per ognuna:</span><span class="sxs-lookup"><span data-stu-id="5aae7-378">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="5aae7-379">Category</span><span class="sxs-lookup"><span data-stu-id="5aae7-379">Category</span></span>                            | <span data-ttu-id="5aae7-380">Note</span><span class="sxs-lookup"><span data-stu-id="5aae7-380">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="5aae7-381">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="5aae7-381">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="5aae7-382">Diagnostica generale di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5aae7-382">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="5aae7-383">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="5aae7-383">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="5aae7-384">Chiavi considerate, trovate e usate.</span><span class="sxs-lookup"><span data-stu-id="5aae7-384">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="5aae7-385">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="5aae7-385">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="5aae7-386">Host consentiti.</span><span class="sxs-lookup"><span data-stu-id="5aae7-386">Hosts allowed.</span></span> |
| <span data-ttu-id="5aae7-387">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="5aae7-387">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="5aae7-388">Tempo impiegato per il completamento delle richieste HTTP e ora di inizio delle richieste.</span><span class="sxs-lookup"><span data-stu-id="5aae7-388">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="5aae7-389">Assembly di avvio dell'hosting caricati.</span><span class="sxs-lookup"><span data-stu-id="5aae7-389">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="5aae7-390">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="5aae7-390">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="5aae7-391">Diagnostica di MVC e Razor.</span><span class="sxs-lookup"><span data-stu-id="5aae7-391">MVC and Razor diagnostics.</span></span> <span data-ttu-id="5aae7-392">Associazione di modelli, esecuzione di filtri, compilazione di viste, selezione di azioni.</span><span class="sxs-lookup"><span data-stu-id="5aae7-392">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="5aae7-393">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="5aae7-393">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="5aae7-394">Informazioni sulla corrispondenza di route.</span><span class="sxs-lookup"><span data-stu-id="5aae7-394">Route matching information.</span></span> |
| <span data-ttu-id="5aae7-395">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="5aae7-395">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="5aae7-396">Risposte di avvio, arresto e keep-alive per la connessione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-396">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="5aae7-397">Informazioni sui certificati HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5aae7-397">HTTPS certificate information.</span></span> |
| <span data-ttu-id="5aae7-398">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="5aae7-398">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="5aae7-399">File forniti.</span><span class="sxs-lookup"><span data-stu-id="5aae7-399">Files served.</span></span> |
| <span data-ttu-id="5aae7-400">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="5aae7-400">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="5aae7-401">Diagnostica generale di Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="5aae7-401">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="5aae7-402">Attività e configurazione del database, rilevamento delle modifiche, migrazioni.</span><span class="sxs-lookup"><span data-stu-id="5aae7-402">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="5aae7-403">Ambiti dei log</span><span class="sxs-lookup"><span data-stu-id="5aae7-403">Log scopes</span></span>

 <span data-ttu-id="5aae7-404">Un *ambito* può raggruppare un set di operazioni logiche.</span><span class="sxs-lookup"><span data-stu-id="5aae7-404">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="5aae7-405">Questo raggruppamento può essere usato per collegare gli stessi dati a ogni log creato come parte di un set.</span><span class="sxs-lookup"><span data-stu-id="5aae7-405">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="5aae7-406">Ad esempio, ogni log creato come parte dell'elaborazione di una transazione può includere l'ID transazione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-406">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="5aae7-407">Un ambito è un tipo `IDisposable` restituito dal metodo <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> e viene mantenuto fino all'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-407">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="5aae7-408">Un ambito si usa mediante il wrapping delle chiamate del logger in un blocco `using`:</span><span class="sxs-lookup"><span data-stu-id="5aae7-408">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="5aae7-409">Il codice seguente abilita gli ambiti per il provider Console:</span><span class="sxs-lookup"><span data-stu-id="5aae7-409">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="5aae7-410">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5aae7-410">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="5aae7-411">La configurazione dell'opzione del logger della console `IncludeScopes` è necessaria per abilitare la registrazione basata sull'ambito.</span><span class="sxs-lookup"><span data-stu-id="5aae7-411">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="5aae7-412">Per informazioni sulla configurazione, vedere la sezione [Configurazione](#configuration).</span><span class="sxs-lookup"><span data-stu-id="5aae7-412">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="5aae7-413">Ogni messaggio di registrazione include le informazioni sull'ambito:</span><span class="sxs-lookup"><span data-stu-id="5aae7-413">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="5aae7-414">Provider di registrazione predefiniti</span><span class="sxs-lookup"><span data-stu-id="5aae7-414">Built-in logging providers</span></span>

<span data-ttu-id="5aae7-415">ASP.NET Core include i provider seguenti:</span><span class="sxs-lookup"><span data-stu-id="5aae7-415">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="5aae7-416">Console</span><span class="sxs-lookup"><span data-stu-id="5aae7-416">Console</span></span>](#console-provider)
* [<span data-ttu-id="5aae7-417">Debug</span><span class="sxs-lookup"><span data-stu-id="5aae7-417">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="5aae7-418">Eventsource</span><span class="sxs-lookup"><span data-stu-id="5aae7-418">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="5aae7-419">EventLog</span><span class="sxs-lookup"><span data-stu-id="5aae7-419">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="5aae7-420">TraceSource</span><span class="sxs-lookup"><span data-stu-id="5aae7-420">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="5aae7-421">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="5aae7-421">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="5aae7-422">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="5aae7-422">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="5aae7-423">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="5aae7-423">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="5aae7-424">Per informazioni su StdOut e la registrazione debug con il modulo ASP.NET Core, vedere <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="5aae7-424">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="5aae7-425">Provider Console</span><span class="sxs-lookup"><span data-stu-id="5aae7-425">Console provider</span></span>

<span data-ttu-id="5aae7-426">Il pacchetto del provider [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) invia l'output della registrazione alla console.</span><span class="sxs-lookup"><span data-stu-id="5aae7-426">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="5aae7-427">Per visualizzare l'output di registrazione del provider Console, aprire un prompt dei comandi nella cartella del progetto ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="5aae7-427">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="5aae7-428">Provider Debug</span><span class="sxs-lookup"><span data-stu-id="5aae7-428">Debug provider</span></span>

<span data-ttu-id="5aae7-429">Il pacchetto del provider [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) scrive l'output della registrazione usando la classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (chiamate del metodo `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="5aae7-429">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="5aae7-430">In Linux, questo provider scrive i log in */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="5aae7-430">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="5aae7-431">Provider dell'origine eventi</span><span class="sxs-lookup"><span data-stu-id="5aae7-431">Event Source provider</span></span>

<span data-ttu-id="5aae7-432">Il pacchetto del provider [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) viene scritto in `Microsoft-Extensions-Logging`una piattaforma multipiattaforma Origine evento con il nome .</span><span class="sxs-lookup"><span data-stu-id="5aae7-432">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="5aae7-433">In Windows, il provider utilizza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="5aae7-433">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="5aae7-434">Il provider di origine `CreateDefaultBuilder` evento viene aggiunto automaticamente quando viene chiamato per compilare l'host.</span><span class="sxs-lookup"><span data-stu-id="5aae7-434">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="5aae7-435">strumenti di traccia dotnet</span><span class="sxs-lookup"><span data-stu-id="5aae7-435">dotnet trace tooling</span></span>

<span data-ttu-id="5aae7-436">Lo strumento [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) è uno strumento globale CLI multipiattaforma che consente la raccolta di tracce .NET Core di un processo in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-436">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="5aae7-437">Lo strumento <xref:Microsoft.Extensions.Logging.EventSource> raccoglie i <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>dati del provider utilizzando un file .</span><span class="sxs-lookup"><span data-stu-id="5aae7-437">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="5aae7-438">Installare gli strumenti di traccia dotnet con il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="5aae7-438">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="5aae7-439">Usare gli strumenti di traccia dotnet per raccogliere una traccia da un'app:Use the dotnet trace tooling to collect a trace from an app:</span><span class="sxs-lookup"><span data-stu-id="5aae7-439">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="5aae7-440">Se l'app non compila `CreateDefaultBuilder`l'host con , aggiungi il [provider dell'origine eventi](#event-source-provider) alla configurazione di registrazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-440">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="5aae7-441">Eseguire l'app `dotnet run` con il comando.</span><span class="sxs-lookup"><span data-stu-id="5aae7-441">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="5aae7-442">Determinare l'identificatore di processo (PID) dell'app .NET Core:</span><span class="sxs-lookup"><span data-stu-id="5aae7-442">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="5aae7-443">In Windows, utilizzare uno dei seguenti approcci:</span><span class="sxs-lookup"><span data-stu-id="5aae7-443">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="5aae7-444">Gestione attività (CTRL-ALT-CANC)</span><span class="sxs-lookup"><span data-stu-id="5aae7-444">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="5aae7-445">comando tasklist</span><span class="sxs-lookup"><span data-stu-id="5aae7-445">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="5aae7-446">Comando di PowerShell Get-Process</span><span class="sxs-lookup"><span data-stu-id="5aae7-446">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="5aae7-447">Su Linux, utilizzare il [comando pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="5aae7-447">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="5aae7-448">Trova il PID per il processo che ha lo stesso nome dell'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-448">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="5aae7-449">Eseguire `dotnet trace` il comando.</span><span class="sxs-lookup"><span data-stu-id="5aae7-449">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="5aae7-450">Sintassi generale dei comandi:</span><span class="sxs-lookup"><span data-stu-id="5aae7-450">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="5aae7-451">Quando si usa una shell `--providers` dei comandi di`'`PowerShell, racchiudere il valore tra virgolette singole ( ):</span><span class="sxs-lookup"><span data-stu-id="5aae7-451">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="5aae7-452">Su piattaforme non Windows, aggiungere l'opzione `-f speedscope` per modificare `speedscope`il formato del file di traccia di output in .</span><span class="sxs-lookup"><span data-stu-id="5aae7-452">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="5aae7-453">Parola chiave</span><span class="sxs-lookup"><span data-stu-id="5aae7-453">Keyword</span></span> | <span data-ttu-id="5aae7-454">Descrizione</span><span class="sxs-lookup"><span data-stu-id="5aae7-454">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="5aae7-455">1</span><span class="sxs-lookup"><span data-stu-id="5aae7-455">1</span></span>       | <span data-ttu-id="5aae7-456">Registrare meta `LoggingEventSource`eventi relativi al file .</span><span class="sxs-lookup"><span data-stu-id="5aae7-456">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="5aae7-457">Non registra gli `ILogger`eventi da ).</span><span class="sxs-lookup"><span data-stu-id="5aae7-457">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="5aae7-458">2</span><span class="sxs-lookup"><span data-stu-id="5aae7-458">2</span></span>       | <span data-ttu-id="5aae7-459">Attiva `Message` l'evento `ILogger.Log()` quando viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="5aae7-459">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="5aae7-460">Fornisce informazioni in modo programmatico (non formattato).</span><span class="sxs-lookup"><span data-stu-id="5aae7-460">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="5aae7-461">4</span><span class="sxs-lookup"><span data-stu-id="5aae7-461">4</span></span>       | <span data-ttu-id="5aae7-462">Attiva `FormatMessage` l'evento `ILogger.Log()` quando viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="5aae7-462">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="5aae7-463">Fornisce la versione in formato stringa delle informazioni.</span><span class="sxs-lookup"><span data-stu-id="5aae7-463">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="5aae7-464">8</span><span class="sxs-lookup"><span data-stu-id="5aae7-464">8</span></span>       | <span data-ttu-id="5aae7-465">Attiva `MessageJson` l'evento `ILogger.Log()` quando viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="5aae7-465">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="5aae7-466">Fornisce una rappresentazione JSON degli argomenti.</span><span class="sxs-lookup"><span data-stu-id="5aae7-466">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="5aae7-467">Livello dell'evento</span><span class="sxs-lookup"><span data-stu-id="5aae7-467">Event Level</span></span> | <span data-ttu-id="5aae7-468">Descrizione</span><span class="sxs-lookup"><span data-stu-id="5aae7-468">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="5aae7-469">0</span><span class="sxs-lookup"><span data-stu-id="5aae7-469">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="5aae7-470">1</span><span class="sxs-lookup"><span data-stu-id="5aae7-470">1</span></span>           | `Critical`      |
   | <span data-ttu-id="5aae7-471">2</span><span class="sxs-lookup"><span data-stu-id="5aae7-471">2</span></span>           | `Error`         |
   | <span data-ttu-id="5aae7-472">3</span><span class="sxs-lookup"><span data-stu-id="5aae7-472">3</span></span>           | `Warning`       |
   | <span data-ttu-id="5aae7-473">4</span><span class="sxs-lookup"><span data-stu-id="5aae7-473">4</span></span>           | `Informational` |
   | <span data-ttu-id="5aae7-474">5</span><span class="sxs-lookup"><span data-stu-id="5aae7-474">5</span></span>           | `Verbose`       |

   <span data-ttu-id="5aae7-475">`FilterSpecs`voci per `{Logger Category}` `{Event Level}` e rappresentano condizioni di filtro del registro aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="5aae7-475">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="5aae7-476">Separare `FilterSpecs` le voci con`;`un punto e virgola ( ).</span><span class="sxs-lookup"><span data-stu-id="5aae7-476">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="5aae7-477">Esempio di utilizzo di una shell dei `--providers` comandi di Windows **(nessuna** virgoletta singola intorno al valore):</span><span class="sxs-lookup"><span data-stu-id="5aae7-477">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="5aae7-478">Il comando precedente attiva:</span><span class="sxs-lookup"><span data-stu-id="5aae7-478">The preceding command activates:</span></span>

   * <span data-ttu-id="5aae7-479">Logger origine evento per produrre`4`stringhe formattate`2`( ) per gli errori ( ).</span><span class="sxs-lookup"><span data-stu-id="5aae7-479">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="5aae7-480">`Microsoft.AspNetCore.Hosting`registrazione a `Informational` livello`4`di registrazione ( ).</span><span class="sxs-lookup"><span data-stu-id="5aae7-480">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="5aae7-481">Arrestare gli strumenti di traccia dotnet premendo il tasto Invio o CTRL .</span><span class="sxs-lookup"><span data-stu-id="5aae7-481">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="5aae7-482">La traccia viene salvata con il nome *trace.nettrace* nella cartella in cui viene eseguito il `dotnet trace` comando.</span><span class="sxs-lookup"><span data-stu-id="5aae7-482">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="5aae7-483">Aprire la traccia con [Perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="5aae7-483">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="5aae7-484">Aprire il file *trace.nettrace* ed esplorare gli eventi di traccia.</span><span class="sxs-lookup"><span data-stu-id="5aae7-484">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="5aae7-485">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="5aae7-485">For more information, see:</span></span>

* <span data-ttu-id="5aae7-486">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (documentazione di .NET Core)</span><span class="sxs-lookup"><span data-stu-id="5aae7-486">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="5aae7-487">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (documentazione del repository GitHub di dotnet/diagnostica)Trace for performance analysis utility (dotnet-trace) (dotnet/diagnostics GitHub repository documentation)</span><span class="sxs-lookup"><span data-stu-id="5aae7-487">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="5aae7-488">[Classe LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (browser API.NET)</span><span class="sxs-lookup"><span data-stu-id="5aae7-488">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="5aae7-489">Origine di [riferimento LoggingEventSource (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Per ottenere l'origine `release/{Version}`del `{Version}` riferimento per una versione diversa, modificare il ramo in , dove è la versione di ASP.NET Core desiderato.</span><span class="sxs-lookup"><span data-stu-id="5aae7-489">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="5aae7-490">[Perfview](#perfview) &ndash; Utile per la visualizzazione delle tracce dell'origine evento.</span><span class="sxs-lookup"><span data-stu-id="5aae7-490">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="5aae7-491">Perfview</span><span class="sxs-lookup"><span data-stu-id="5aae7-491">Perfview</span></span>

<span data-ttu-id="5aae7-492">Utilizzare [l'utilità PerfView](https://github.com/Microsoft/perfview) per raccogliere e visualizzare i log.</span><span class="sxs-lookup"><span data-stu-id="5aae7-492">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="5aae7-493">Sono disponibili altri strumenti per la visualizzazione dei log ETW, ma PerfView fornisce un'esperienza ottimale per l'uso con gli eventi ETW generati da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5aae7-493">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="5aae7-494">Per configurare PerfView per la raccolta degli eventi registrati da questo provider, aggiungere la stringa `*Microsoft-Extensions-Logging` nell'elenco **Provider aggiuntivi**</span><span class="sxs-lookup"><span data-stu-id="5aae7-494">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="5aae7-495">(non dimenticare l'asterisco all'inizio della stringa).</span><span class="sxs-lookup"><span data-stu-id="5aae7-495">(Don't miss the asterisk at the start of the string.)</span></span>

![Provider aggiuntivi di PerfView](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="5aae7-497">Provider EventLog di Windows</span><span class="sxs-lookup"><span data-stu-id="5aae7-497">Windows EventLog provider</span></span>

<span data-ttu-id="5aae7-498">Il pacchetto di provider [Microsoft.Extensions.Logging.AventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) invia l'output del log al registro eventi di Windows.</span><span class="sxs-lookup"><span data-stu-id="5aae7-498">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="5aae7-499">Gli [overload di AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) consentono di passare <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="5aae7-499">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="5aae7-500">Se `null` specificato o non specificato, vengono utilizzate le seguenti impostazioni predefinite:</span><span class="sxs-lookup"><span data-stu-id="5aae7-500">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="5aae7-501">`LogName`&ndash; "Applicazione"</span><span class="sxs-lookup"><span data-stu-id="5aae7-501">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="5aae7-502">`SourceName`&ndash; "Runtime .NET"</span><span class="sxs-lookup"><span data-stu-id="5aae7-502">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="5aae7-503">`MachineName` &ndash; computer locale</span><span class="sxs-lookup"><span data-stu-id="5aae7-503">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="5aae7-504">Gli eventi vengono registrati per [il livello di avviso e superiore](#log-level).</span><span class="sxs-lookup"><span data-stu-id="5aae7-504">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="5aae7-505">Per registrare `Warning`eventi inferiori a , impostare in modo esplicito il livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-505">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="5aae7-506">Ad esempio, aggiungere quanto segue al file *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="5aae7-506">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="5aae7-507">Provider TraceSource</span><span class="sxs-lookup"><span data-stu-id="5aae7-507">TraceSource provider</span></span>

<span data-ttu-id="5aae7-508">Il pacchetto di provider [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa le librerie e i provider <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="5aae7-508">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="5aae7-509">Gli [overload di AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) consentono di passare un cambio di origine e un listener di traccia.</span><span class="sxs-lookup"><span data-stu-id="5aae7-509">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="5aae7-510">Per usare questo provider, un'app deve essere in esecuzione in .NET Framework (invece che in .NET Core).</span><span class="sxs-lookup"><span data-stu-id="5aae7-510">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="5aae7-511">Il provider può instradare i messaggi a diversi [listener](/dotnet/framework/debug-trace-profile/trace-listeners), come <xref:System.Diagnostics.TextWriterTraceListener>, usato nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="5aae7-511">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="5aae7-512">Provider del Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="5aae7-512">Azure App Service provider</span></span>

<span data-ttu-id="5aae7-513">Il pacchetto di provider [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) scrive i log in file di testo nel file system di un'app del Servizio app di Azure e nell'[archivio di BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in un account di archiviazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="5aae7-513">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="5aae7-514">Il pacchetto del provider non è incluso nel framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="5aae7-514">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="5aae7-515">Per usare il provider, aggiungere il relativo pacchetto al progetto.</span><span class="sxs-lookup"><span data-stu-id="5aae7-515">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="5aae7-516">Per configurare le impostazioni del provider, usare <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> e <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="5aae7-516">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="5aae7-517">Quando si distribuisce un'app del servizio app, l'applicazione applica le impostazioni della sezione [Log del servizio app](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) della pagina **Servizio app** del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="5aae7-517">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="5aae7-518">Quando le impostazioni seguenti vengono aggiornate, le modifiche hanno effetto immediatamente senza richiedere un riavvio o la ridistribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-518">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="5aae7-519">**Registrazione applicazioni (file system)**</span><span class="sxs-lookup"><span data-stu-id="5aae7-519">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="5aae7-520">**Registrazione applicazione (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="5aae7-520">**Application Logging (Blob)**</span></span>

<span data-ttu-id="5aae7-521">Il percorso predefinito per i file di log è nella cartella *D:\\home\\LogFiles\\Application* e il nome di file predefinito è *diagnostics-aaaammgg.txt*.</span><span class="sxs-lookup"><span data-stu-id="5aae7-521">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="5aae7-522">Il limite predefinito per le dimensioni del file è 10 MB e il numero massimo predefinito di file conservati è 2.</span><span class="sxs-lookup"><span data-stu-id="5aae7-522">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="5aae7-523">Il nome BLOB predefinito è *{nome-app}{timestamp}/aaaa/mm/gg/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="5aae7-523">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="5aae7-524">Il provider funziona solo quando il progetto viene eseguito nell'ambiente di Azure.</span><span class="sxs-lookup"><span data-stu-id="5aae7-524">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="5aae7-525">Non ha alcun effetto quando il progetto viene eseguito localmente, in quanto non scrive nulla in file locali o in archivi di sviluppo locali per i BLOB.</span><span class="sxs-lookup"><span data-stu-id="5aae7-525">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="5aae7-526">Flusso di registrazione di Azure</span><span class="sxs-lookup"><span data-stu-id="5aae7-526">Azure log streaming</span></span>

<span data-ttu-id="5aae7-527">Il flusso di registrazione di Azure consente di visualizzare l'attività di registrazione in tempo reale da:</span><span class="sxs-lookup"><span data-stu-id="5aae7-527">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="5aae7-528">Server applicazioni</span><span class="sxs-lookup"><span data-stu-id="5aae7-528">The app server</span></span>
* <span data-ttu-id="5aae7-529">Server Web</span><span class="sxs-lookup"><span data-stu-id="5aae7-529">The web server</span></span>
* <span data-ttu-id="5aae7-530">Traccia delle richieste non riuscite</span><span class="sxs-lookup"><span data-stu-id="5aae7-530">Failed request tracing</span></span>

<span data-ttu-id="5aae7-531">Per configurare il flusso di registrazione di Azure:</span><span class="sxs-lookup"><span data-stu-id="5aae7-531">To configure Azure log streaming:</span></span>

* <span data-ttu-id="5aae7-532">Passare alla pagina **Log del servizio app** dalla pagina del portale dell'app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-532">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="5aae7-533">Impostare **Registrazione applicazioni (file system)** su **Attiva**.</span><span class="sxs-lookup"><span data-stu-id="5aae7-533">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="5aae7-534">Scegliere il livello di registrazione in **Livello**.</span><span class="sxs-lookup"><span data-stu-id="5aae7-534">Choose the log **Level**.</span></span> <span data-ttu-id="5aae7-535">Questa impostazione si applica solo allo streaming dei log di Azure, non agli altri provider di registrazione nell'app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-535">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="5aae7-536">Passare alla pagina **Flusso di registrazione** per visualizzare i messaggi dell'app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-536">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="5aae7-537">I messaggi vengono registrati dall'app tramite l'interfaccia `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-537">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="5aae7-538">Registrazione di traccia di Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="5aae7-538">Azure Application Insights trace logging</span></span>

<span data-ttu-id="5aae7-539">Il pacchetto di provider [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) scrive log in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="5aae7-539">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="5aae7-540">Application Insights è un servizio che monitora un'app Web e fornisce gli strumenti per l'esecuzione di query sui dati di telemetria e la loro analisi.</span><span class="sxs-lookup"><span data-stu-id="5aae7-540">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="5aae7-541">Se si usa questo provider, è possibile eseguire query sui log e analizzarli usando gli strumenti di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="5aae7-541">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="5aae7-542">Il provider di registrazione è incluso come dipendenza di [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), ovvero il pacchetto che fornisce tutti i dati di telemetria disponibili per ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5aae7-542">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="5aae7-543">Se si usa questo pacchetto, non è necessario installare il pacchetto di provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-543">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="5aae7-544">Non usare il pacchetto [](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)Microsoft.ApplicationInsights.Web&mdash;, che è per ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="5aae7-544">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="5aae7-545">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="5aae7-545">For more information, see the following resources:</span></span>

* [<span data-ttu-id="5aae7-546">Panoramica di Application Insights</span><span class="sxs-lookup"><span data-stu-id="5aae7-546">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="5aae7-547">[Application Insights per applicazioni ASP.NET Core](/azure/azure-monitor/app/asp-net-core): iniziare da qui se si vuole implementare l'intervallo completo dei dati di telemetria di Application Insights insieme alla registrazione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-547">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="5aae7-548">[ApplicationInsightsLoggerProvider per log ILogger .NET Core](/azure/azure-monitor/app/ilogger): iniziare da qui se si vuole implementare il provider di registrazione senza gli altri dati di telemetria di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="5aae7-548">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="5aae7-549">[Adattatori di registrazione di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="5aae7-549">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="5aae7-550">[Installare, configurare e inizializzare Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): esercitazione interattiva nel sito Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="5aae7-550">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="5aae7-551">Provider di registrazione di terze parti</span><span class="sxs-lookup"><span data-stu-id="5aae7-551">Third-party logging providers</span></span>

<span data-ttu-id="5aae7-552">Framework di registrazione di terze parti che usano ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5aae7-552">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="5aae7-553">[elmah.io](https://elmah.io/) ([repository GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="5aae7-553">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="5aae7-554">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repository GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="5aae7-554">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="5aae7-555">[JSNLog](https://jsnlog.com/) ([repository GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="5aae7-555">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="5aae7-556">[KissLog.net](https://kisslog.net/) ([repository GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="5aae7-556">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="5aae7-557">[Log4Net](https://logging.apache.org/log4net/) ([repository GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="5aae7-557">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="5aae7-558">[Loggr](https://loggr.net/) ([repository GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="5aae7-558">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="5aae7-559">[NLog](https://nlog-project.org/) ([repository GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="5aae7-559">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="5aae7-560">[Sentry](https://sentry.io/welcome/) ([repository GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="5aae7-560">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="5aae7-561">[Serilog](https://serilog.net/) ([repository GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="5aae7-561">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="5aae7-562">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repository Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="5aae7-562">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="5aae7-563">Alcuni framework di terze parti possono eseguire la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="5aae7-563">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="5aae7-564">L'uso di un framework di terze parti è simile a quello di uno dei provider predefiniti:</span><span class="sxs-lookup"><span data-stu-id="5aae7-564">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="5aae7-565">Aggiungere un pacchetto NuGet al progetto.</span><span class="sxs-lookup"><span data-stu-id="5aae7-565">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="5aae7-566">Chiamare `ILoggerFactory` un metodo di estensione fornito dal framework di registrazione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-566">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="5aae7-567">Per altre informazioni, vedere la documentazione di ogni provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-567">For more information, see each provider's documentation.</span></span> <span data-ttu-id="5aae7-568">I provider di registrazione di terze parti non sono supportati da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="5aae7-568">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5aae7-569">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="5aae7-569">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5aae7-570">Di [Tom Dykstra](https://github.com/tdykstra) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5aae7-570">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5aae7-571">.NET Core supporta un'API di registrazione che funziona con un'ampia gamma di provider di registrazione predefiniti e di terze parti.</span><span class="sxs-lookup"><span data-stu-id="5aae7-571">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="5aae7-572">Questo articolo illustra come usare l'API di registrazione con i provider predefiniti.</span><span class="sxs-lookup"><span data-stu-id="5aae7-572">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="5aae7-573">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5aae7-573">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="5aae7-574">Aggiungere provider</span><span class="sxs-lookup"><span data-stu-id="5aae7-574">Add providers</span></span>

<span data-ttu-id="5aae7-575">Un provider di registrazione visualizza o archivia i log.</span><span class="sxs-lookup"><span data-stu-id="5aae7-575">A logging provider displays or stores logs.</span></span> <span data-ttu-id="5aae7-576">Il provider Console, ad esempio, visualizza i log nella console, mentre il provider Azure Application Insights li archivia in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="5aae7-576">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="5aae7-577">I log possono essere inviati a più destinazioni aggiungendo più provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-577">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="5aae7-578">Per aggiungere un provider, chiamare il metodo di estensione `Add{provider name}` del provider in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5aae7-578">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="5aae7-579">Il codice precedente richiede riferimenti a `Microsoft.Extensions.Logging` e `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-579">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="5aae7-580">Il modello di progetto predefinito chiama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, che aggiunge i provider di registrazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="5aae7-580">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="5aae7-581">Console</span><span class="sxs-lookup"><span data-stu-id="5aae7-581">Console</span></span>
* <span data-ttu-id="5aae7-582">Debug</span><span class="sxs-lookup"><span data-stu-id="5aae7-582">Debug</span></span>
* <span data-ttu-id="5aae7-583">EventSource (a partire da ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="5aae7-583">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="5aae7-584">Se si usa `CreateDefaultBuilder`, è possibile sostituire i provider predefiniti con quelli di propria scelta.</span><span class="sxs-lookup"><span data-stu-id="5aae7-584">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="5aae7-585">Chiamare <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e aggiungere i provider desiderati.</span><span class="sxs-lookup"><span data-stu-id="5aae7-585">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="5aae7-586">Altre informazioni sui [provider di registrazione predefiniti](#built-in-logging-providers) e sui [provider di registrazione di terze parti](#third-party-logging-providers) vengono fornite più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-586">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="5aae7-587">Creare log</span><span class="sxs-lookup"><span data-stu-id="5aae7-587">Create logs</span></span>

<span data-ttu-id="5aae7-588">Per creare i log, usare un oggetto <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="5aae7-588">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="5aae7-589">In un'app Web o in un servizio ospitato, ottenere un oggetto `ILogger` da un inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="5aae7-589">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="5aae7-590">Nelle app console non host, usare `LoggerFactory` per creare un oggetto `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-590">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="5aae7-591">L'esempio seguente di ASP.NET Core crea un logger con `TodoApiSample.Pages.AboutModel` come categoria.</span><span class="sxs-lookup"><span data-stu-id="5aae7-591">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="5aae7-592">La *categoria* del log è una stringa associata a ogni log.</span><span class="sxs-lookup"><span data-stu-id="5aae7-592">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="5aae7-593">L'istanza di `ILogger<T>` fornita dall'inserimento delle dipendenze crea log con nome completo di tipo `T` come categoria.</span><span class="sxs-lookup"><span data-stu-id="5aae7-593">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="5aae7-594">Negli esempi seguenti di ASP.NET Core e app console, il logger viene usato per creare log con `Information` come livello.</span><span class="sxs-lookup"><span data-stu-id="5aae7-594">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="5aae7-595">Il *livello* del log indica la gravità dell'evento registrato.</span><span class="sxs-lookup"><span data-stu-id="5aae7-595">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="5aae7-596">[Livelli](#log-level) e [categorie](#log-category) vengono illustrati in modo dettagliato più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-596">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="5aae7-597">Creare log nella classe Startup</span><span class="sxs-lookup"><span data-stu-id="5aae7-597">Create logs in Startup</span></span>

<span data-ttu-id="5aae7-598">Per scrivere log nella classe `Startup`, includere un parametro `ILogger` nella firma del costruttore:</span><span class="sxs-lookup"><span data-stu-id="5aae7-598">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="5aae7-599">Creare log nella classe Program</span><span class="sxs-lookup"><span data-stu-id="5aae7-599">Create logs in the Program class</span></span>

<span data-ttu-id="5aae7-600">Per scrivere log nella classe `Program`, ottenere un'istanza di `ILogger` dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="5aae7-600">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="5aae7-601">La registrazione durante la costruzione dell'host non è supportata direttamente.</span><span class="sxs-lookup"><span data-stu-id="5aae7-601">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="5aae7-602">Tuttavia, è possibile utilizzare un logger separato.</span><span class="sxs-lookup"><span data-stu-id="5aae7-602">However, a separate logger can be used.</span></span> <span data-ttu-id="5aae7-603">Nell'esempio seguente viene utilizzato un logger [Serilog](https://serilog.net/) per l'accesso `CreateWebHostBuilder`a .</span><span class="sxs-lookup"><span data-stu-id="5aae7-603">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="5aae7-604">`AddSerilog`utilizza la configurazione `Log.Logger`statica specificata in :</span><span class="sxs-lookup"><span data-stu-id="5aae7-604">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="5aae7-605">Evitare l'uso di metodi logger asincroni</span><span class="sxs-lookup"><span data-stu-id="5aae7-605">No asynchronous logger methods</span></span>

<span data-ttu-id="5aae7-606">La registrazione deve essere così rapida da non giustificare l'impatto sulle prestazioni del codice asincrono.</span><span class="sxs-lookup"><span data-stu-id="5aae7-606">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="5aae7-607">Se l'archivio dati di registrazione è lento, non scrivere direttamente al suo interno.</span><span class="sxs-lookup"><span data-stu-id="5aae7-607">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="5aae7-608">Scrivere invece i messaggi di log prima in un archivio veloce e quindi spostarli nell'archivio lento in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="5aae7-608">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="5aae7-609">Ad esempio, se la registrazione viene eseguita in SQL Server, è preferibile non farlo direttamente in un metodo `Log`, poiché i metodi `Log` sono sincroni.</span><span class="sxs-lookup"><span data-stu-id="5aae7-609">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="5aae7-610">Al contrario, aggiungere i messaggi di log in modo sincrono a una coda in memoria e usare un ruolo di lavoro in background per eseguire il pull dei messaggi dalla coda per eseguire le operazioni asincrone di push dei dati in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="5aae7-610">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="5aae7-611">Per altre informazioni, vedere questo problema di GitHub.For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span><span class="sxs-lookup"><span data-stu-id="5aae7-611">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="5aae7-612">Configurazione</span><span class="sxs-lookup"><span data-stu-id="5aae7-612">Configuration</span></span>

<span data-ttu-id="5aae7-613">La configurazione dei provider di registrazione viene fornita da uno o più provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="5aae7-613">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="5aae7-614">Formati di file (INI, JSON e XML).</span><span class="sxs-lookup"><span data-stu-id="5aae7-614">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="5aae7-615">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="5aae7-615">Command-line arguments.</span></span>
* <span data-ttu-id="5aae7-616">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="5aae7-616">Environment variables.</span></span>
* <span data-ttu-id="5aae7-617">Oggetti .NET in memoria.</span><span class="sxs-lookup"><span data-stu-id="5aae7-617">In-memory .NET objects.</span></span>
* <span data-ttu-id="5aae7-618">Archiviazione con [Secret Manager](xref:security/app-secrets) senza crittografia.</span><span class="sxs-lookup"><span data-stu-id="5aae7-618">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="5aae7-619">Un archivio utente non crittografato, come [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="5aae7-619">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="5aae7-620">Provider personalizzati (installati o creati).</span><span class="sxs-lookup"><span data-stu-id="5aae7-620">Custom providers (installed or created).</span></span>

<span data-ttu-id="5aae7-621">Ad esempio, la configurazione di registrazione viene comunemente fornita dalla sezione `Logging` del file di impostazioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-621">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="5aae7-622">L'esempio seguente mostra il contenuto di un file *appsettings.Development.json* tipico:</span><span class="sxs-lookup"><span data-stu-id="5aae7-622">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="5aae7-623">La proprietà `Logging` può avere le proprietà `LogLevel` e quella del provider di log (nell'esempio, Console).</span><span class="sxs-lookup"><span data-stu-id="5aae7-623">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="5aae7-624">La proprietà `LogLevel` in `Logging` specifica il [livello](#log-level) minimo per la registrazione per determinate categorie.</span><span class="sxs-lookup"><span data-stu-id="5aae7-624">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="5aae7-625">Nell'esempio, le categorie `System` e `Microsoft` eseguono la registrazione al livello `Information`, mentre tutte le altre la eseguono al livello `Debug`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-625">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="5aae7-626">Altre proprietà in `Logging` specificano i provider di registrazione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-626">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="5aae7-627">L'esempio si riferisce al provider Console.</span><span class="sxs-lookup"><span data-stu-id="5aae7-627">The example is for the Console provider.</span></span> <span data-ttu-id="5aae7-628">Se un provider supporta gli `IncludeScopes` ambiti di [log](#log-scopes), indica se sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="5aae7-628">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="5aae7-629">Una proprietà del provider (ad esempio `Console` nell'esempio) può specificare anche una proprietà `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-629">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="5aae7-630">`LogLevel` in un provider specifica i livelli di registrazione per tale provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-630">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="5aae7-631">Se i livelli sono specificati in `Logging.{providername}.LogLevel`, eseguono l'override di eventuali valori impostati in `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-631">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="5aae7-632">L'API di registrazione non include uno scenario per modificare i livelli di log mentre un'app è in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-632">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="5aae7-633">Tuttavia, alcuni provider di configurazione sono in grado di ricaricare la configurazione, che ha effetto immediato sulla configurazione della registrazione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-633">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="5aae7-634">Ad esempio, il provider di configurazione `CreateDefaultBuilder` [file](xref:fundamentals/configuration/index#file-configuration-provider), che viene aggiunto da per leggere i file di impostazioni, ricarica la configurazione di registrazione per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="5aae7-634">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="5aae7-635">Se la configurazione viene modificata nel codice mentre un'app è in esecuzione, l'app può chiamare [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) per aggiornare la configurazione di registrazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-635">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="5aae7-636">Per informazioni sull'implementazione dei provider di configurazione, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="5aae7-636">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="5aae7-637">Esempio di output di registrazione</span><span class="sxs-lookup"><span data-stu-id="5aae7-637">Sample logging output</span></span>

<span data-ttu-id="5aae7-638">Con il codice di esempio illustrato nella sezione precedente i log vengono visualizzati nella console quando l'app viene eseguita dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="5aae7-638">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="5aae7-639">Ecco un esempio di output della console:</span><span class="sxs-lookup"><span data-stu-id="5aae7-639">Here's an example of console output:</span></span>

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

<span data-ttu-id="5aae7-640">I log precedenti sono stati generati inviando una richiesta HTTP Get all'app di esempio all'indirizzo `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-640">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="5aae7-641">Ecco un esempio degli stessi log visualizzati nella finestra Debug quando si esegue l'app di esempio in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="5aae7-641">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="5aae7-642">I log creati dalle chiamate a `ILogger` illustrate nella sezione precedente iniziano con "TodoApi".</span><span class="sxs-lookup"><span data-stu-id="5aae7-642">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="5aae7-643">I log che iniziano con le categorie "Microsoft" provengono dal codice del framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5aae7-643">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="5aae7-644">ASP.NET Core e il codice dell'applicazione usano la stessa API di registrazione e gli stessi provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-644">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="5aae7-645">Il resto di questo articolo spiega alcuni dettagli e le opzioni per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-645">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="5aae7-646">Pacchetti NuGet</span><span class="sxs-lookup"><span data-stu-id="5aae7-646">NuGet packages</span></span>

<span data-ttu-id="5aae7-647">Le interfacce `ILogger` e `ILoggerFactory` si trovano in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) e le loro implementazioni predefinite si trovano in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="5aae7-647">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="5aae7-648">Categoria di log</span><span class="sxs-lookup"><span data-stu-id="5aae7-648">Log category</span></span>

<span data-ttu-id="5aae7-649">Quando viene creato un oggetto `ILogger`, viene specificata la relativa *categoria*.</span><span class="sxs-lookup"><span data-stu-id="5aae7-649">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="5aae7-650">La categoria è inclusa in ogni messaggio di log creato da tale istanza di `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-650">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="5aae7-651">La categoria può essere qualsiasi stringa, ma per convenzione si usa il nome della classe, ad esempio "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="5aae7-651">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="5aae7-652">Usare `ILogger<T>` per ottenere un'istanza di `ILogger` che usa il nome completo di tipo `T` come categoria:</span><span class="sxs-lookup"><span data-stu-id="5aae7-652">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="5aae7-653">Per specificare in modo esplicito la categoria, chiamare `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="5aae7-653">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="5aae7-654">L'uso di `ILogger<T>` equivale a chiamare `CreateLogger` con il nome completo di tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-654">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="5aae7-655">Livello di registrazione</span><span class="sxs-lookup"><span data-stu-id="5aae7-655">Log level</span></span>

<span data-ttu-id="5aae7-656">Ogni log specifica un valore <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="5aae7-656">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="5aae7-657">Il livello di registrazione indica la gravità o l'importanza.</span><span class="sxs-lookup"><span data-stu-id="5aae7-657">The log level indicates the severity or importance.</span></span> <span data-ttu-id="5aae7-658">È ad esempio possibile scrivere un log `Information` quando un metodo termina normalmente e un log `Warning` quando un metodo restituisce un codice di stato *404 Non trovato*.</span><span class="sxs-lookup"><span data-stu-id="5aae7-658">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="5aae7-659">Il codice seguente crea i log `Information` e `Warning`:</span><span class="sxs-lookup"><span data-stu-id="5aae7-659">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="5aae7-660">Nel codice precedente il primo parametro è l'[ID dell'evento di log](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="5aae7-660">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="5aae7-661">Il secondo parametro è un modello di messaggio con segnaposto per i valori degli argomenti forniti dai parametri dei metodi rimanenti.</span><span class="sxs-lookup"><span data-stu-id="5aae7-661">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="5aae7-662">I parametri dei metodi sono descritti nella [sezione relativa al modello di messaggio](#log-message-template) più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-662">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="5aae7-663">I metodi di registrazione che includono il livello nel nome del metodo (ad esempio `LogInformation` e `LogWarning`) sono [metodi di estensione per ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="5aae7-663">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="5aae7-664">Questi metodi chiamano un metodo `Log` che accetta un parametro `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-664">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="5aae7-665">È possibile chiamare il metodo `Log` direttamente anziché chiamare uno di questi metodi di estensione, ma la sintassi è relativamente complessa.</span><span class="sxs-lookup"><span data-stu-id="5aae7-665">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="5aae7-666">Per altre informazioni, vedere <xref:Microsoft.Extensions.Logging.ILogger> e il [codice sorgente delle estensioni del logger](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="5aae7-666">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="5aae7-667">ASP.NET Core definisce i livelli di registrazione seguenti, ordinati dal meno grave al più grave.</span><span class="sxs-lookup"><span data-stu-id="5aae7-667">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="5aae7-668">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="5aae7-668">Trace = 0</span></span>

  <span data-ttu-id="5aae7-669">Per informazioni in genere utili solo per il debug.</span><span class="sxs-lookup"><span data-stu-id="5aae7-669">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="5aae7-670">Questi messaggi possono contenere dati sensibili dell'applicazione e pertanto non devono essere abilitati in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-670">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="5aae7-671">*Disattivato per impostazione predefinita.*</span><span class="sxs-lookup"><span data-stu-id="5aae7-671">*Disabled by default.*</span></span>

* <span data-ttu-id="5aae7-672">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="5aae7-672">Debug = 1</span></span>

  <span data-ttu-id="5aae7-673">Per informazioni che possono essere utili per lo sviluppo e il debug.</span><span class="sxs-lookup"><span data-stu-id="5aae7-673">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="5aae7-674">Esempio: `Entering method Configure with flag set to true.` Abilitare i livelli di registrazione `Debug` nell'ambiente di produzione solo in fase di risoluzione dei problemi, per non fare aumentare eccessivamente il volume dei log.</span><span class="sxs-lookup"><span data-stu-id="5aae7-674">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="5aae7-675">Information = 2</span><span class="sxs-lookup"><span data-stu-id="5aae7-675">Information = 2</span></span>

  <span data-ttu-id="5aae7-676">Per tenere traccia del flusso generale dell'app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-676">For tracking the general flow of the app.</span></span> <span data-ttu-id="5aae7-677">Queste registrazioni hanno in genere un valore a lungo termine.</span><span class="sxs-lookup"><span data-stu-id="5aae7-677">These logs typically have some long-term value.</span></span> <span data-ttu-id="5aae7-678">Esempio: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="5aae7-678">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="5aae7-679">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="5aae7-679">Warning = 3</span></span>

  <span data-ttu-id="5aae7-680">Per gli eventi imprevisti o anomali nel flusso dell'app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-680">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="5aae7-681">Tali eventi potrebbero includere errori o altre condizioni che non provocano l'arresto dell'app, ma che potrebbe essere necessario analizzare.</span><span class="sxs-lookup"><span data-stu-id="5aae7-681">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="5aae7-682">Le eccezioni gestite sono una situazione comune in cui usare il livello di registrazione `Warning`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-682">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="5aae7-683">Esempio: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="5aae7-683">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="5aae7-684">Error = 4</span><span class="sxs-lookup"><span data-stu-id="5aae7-684">Error = 4</span></span>

  <span data-ttu-id="5aae7-685">Per errori ed eccezioni che non possono essere gestiti.</span><span class="sxs-lookup"><span data-stu-id="5aae7-685">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="5aae7-686">Questi messaggi indicano un errore nell'operazione o nell'attività in corso (ad esempio la richiesta HTTP corrente), non un errore a livello di app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-686">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="5aae7-687">Messaggio di registrazione di esempio:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="5aae7-687">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="5aae7-688">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="5aae7-688">Critical = 5</span></span>

  <span data-ttu-id="5aae7-689">Per gli errori che richiedono attenzione immediata.</span><span class="sxs-lookup"><span data-stu-id="5aae7-689">For failures that require immediate attention.</span></span> <span data-ttu-id="5aae7-690">Esempi: scenari di perdita di dati, spazio su disco insufficiente.</span><span class="sxs-lookup"><span data-stu-id="5aae7-690">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="5aae7-691">Usare il livello di registrazione per controllare la quantità di output di log scritto in un supporto di archiviazione specifico o in una finestra.</span><span class="sxs-lookup"><span data-stu-id="5aae7-691">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="5aae7-692">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5aae7-692">For example:</span></span>

* <span data-ttu-id="5aae7-693">In produzione:</span><span class="sxs-lookup"><span data-stu-id="5aae7-693">In production:</span></span>
  * <span data-ttu-id="5aae7-694">La registrazione `Trace` `Information` ai livelli through produce un volume elevato di messaggi di log dettagliati.</span><span class="sxs-lookup"><span data-stu-id="5aae7-694">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="5aae7-695">Per controllare i costi e non `Trace` `Information` superare i limiti di archiviazione dei dati, eseguire il log-through dei messaggi di livello in un archivio dati con volumi elevati e a basso costo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-695">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="5aae7-696">La `Warning` registrazione `Critical` a livelli tramite livelli produce in genere un numero inferiore di messaggi di log più piccoli.</span><span class="sxs-lookup"><span data-stu-id="5aae7-696">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="5aae7-697">Pertanto, i costi e i limiti di archiviazione in genere non sono un problema, il che si traduce in una maggiore flessibilità di scelta dell'archivio dati.</span><span class="sxs-lookup"><span data-stu-id="5aae7-697">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="5aae7-698">Durante lo sviluppo:</span><span class="sxs-lookup"><span data-stu-id="5aae7-698">During development:</span></span>
  * <span data-ttu-id="5aae7-699">`Warning` Registrare `Critical` i messaggi nella console.</span><span class="sxs-lookup"><span data-stu-id="5aae7-699">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="5aae7-700">Aggiungere `Trace` `Information` i messaggi durante la risoluzione dei problemi.</span><span class="sxs-lookup"><span data-stu-id="5aae7-700">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="5aae7-701">La sezione [Filtro dei log](#log-filtering) più avanti in questo articolo descrive come controllare quali livelli di registrazione gestisce un provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-701">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="5aae7-702">ASP.NET Core scrive log per gli eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="5aae7-702">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="5aae7-703">Gli esempi di log illustrati in precedenza in questo articolo escludono i log al di sotto del livello `Information`, quindi non vengono creati log di livello `Debug` o `Trace`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-703">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="5aae7-704">Ecco un esempio di log della console prodotti eseguendo l'app di esempio configurata in modo da mostrare i log di livello `Debug`:</span><span class="sxs-lookup"><span data-stu-id="5aae7-704">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="5aae7-705">ID evento di registrazione</span><span class="sxs-lookup"><span data-stu-id="5aae7-705">Log event ID</span></span>

<span data-ttu-id="5aae7-706">Ogni log può specificare un *ID evento*.</span><span class="sxs-lookup"><span data-stu-id="5aae7-706">Each log can specify an *event ID*.</span></span> <span data-ttu-id="5aae7-707">A tale scopo, l'app di esempio usa una classe `LoggingEvents` definita a livello locale:</span><span class="sxs-lookup"><span data-stu-id="5aae7-707">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="5aae7-708">Un ID evento associa un set di eventi.</span><span class="sxs-lookup"><span data-stu-id="5aae7-708">An event ID associates a set of events.</span></span> <span data-ttu-id="5aae7-709">Ad esempio, tutti i log correlati alla visualizzazione di un elenco di elementi in una pagina potrebbero essere indicati con 1001.</span><span class="sxs-lookup"><span data-stu-id="5aae7-709">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="5aae7-710">Il provider di log può archiviare l'ID evento in un campo ID o nel messaggio di registrazione oppure non archiviarlo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-710">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="5aae7-711">Il provider Debug non visualizza gli ID evento.</span><span class="sxs-lookup"><span data-stu-id="5aae7-711">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="5aae7-712">Il provider Console visualizza gli ID evento tra parentesi quadre dopo la categoria:</span><span class="sxs-lookup"><span data-stu-id="5aae7-712">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="5aae7-713">Modello di messaggio di registrazione</span><span class="sxs-lookup"><span data-stu-id="5aae7-713">Log message template</span></span>

<span data-ttu-id="5aae7-714">Ogni log specifica un modello di messaggio.</span><span class="sxs-lookup"><span data-stu-id="5aae7-714">Each log specifies a message template.</span></span> <span data-ttu-id="5aae7-715">Il modello di messaggio può contenere segnaposto per i quali vengono forniti argomenti.</span><span class="sxs-lookup"><span data-stu-id="5aae7-715">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="5aae7-716">Usare nomi per i segnaposto, non numeri.</span><span class="sxs-lookup"><span data-stu-id="5aae7-716">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="5aae7-717">L'ordine dei segnaposto, non il loro nome, determina i parametri da usare per fornire i valori corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="5aae7-717">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="5aae7-718">Nel codice seguente, si noti che i nomi dei parametri non sono in sequenza nel modello di messaggio:</span><span class="sxs-lookup"><span data-stu-id="5aae7-718">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="5aae7-719">Questo codice crea un messaggio di log con i valori dei parametri in sequenza:</span><span class="sxs-lookup"><span data-stu-id="5aae7-719">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="5aae7-720">Il framework di registrazione funziona in questo modo per consentire ai provider di registrazione di implementare la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="5aae7-720">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="5aae7-721">Al sistema di registrazione vengono passati gli argomenti e non solo il modello di messaggio formattato.</span><span class="sxs-lookup"><span data-stu-id="5aae7-721">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="5aae7-722">Queste informazioni consentono ai provider di registrazione di archiviare i valori dei parametri come campi.</span><span class="sxs-lookup"><span data-stu-id="5aae7-722">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="5aae7-723">Si supponga, ad esempio, che il metodo logger esegua chiamate simili alla seguente:</span><span class="sxs-lookup"><span data-stu-id="5aae7-723">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="5aae7-724">Se si inviano i log all'archiviazione tabelle di Azure, ogni entità tabella di Azure può avere le proprietà `ID` e `RequestTime`, il che semplifica le query sui dati dei log.</span><span class="sxs-lookup"><span data-stu-id="5aae7-724">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="5aae7-725">Una query può trovare tutti i log entro un determinato intervallo `RequestTime` senza che sia necessario analizzare il messaggio di testo per determinare l'intervallo di tempo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-725">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="5aae7-726">Registrazione delle eccezioni</span><span class="sxs-lookup"><span data-stu-id="5aae7-726">Logging exceptions</span></span>

<span data-ttu-id="5aae7-727">I metodi logger dispongono di overload che consentono di passare un'eccezione, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="5aae7-727">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="5aae7-728">Provider diversi gestiscono le informazioni sulle eccezioni in modi diversi.</span><span class="sxs-lookup"><span data-stu-id="5aae7-728">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="5aae7-729">Ecco un esempio di output del provider Debug dal codice sopra riportato.</span><span class="sxs-lookup"><span data-stu-id="5aae7-729">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="5aae7-730">Filtro del log</span><span class="sxs-lookup"><span data-stu-id="5aae7-730">Log filtering</span></span>

<span data-ttu-id="5aae7-731">È possibile specificare un livello di registrazione minimo per un provider e una categoria specifici oppure per tutti i provider o tutte le categorie.</span><span class="sxs-lookup"><span data-stu-id="5aae7-731">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="5aae7-732">Tutti i log sotto il livello minimo non sono passati al provider, quindi non vengono visualizzati o archiviati.</span><span class="sxs-lookup"><span data-stu-id="5aae7-732">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="5aae7-733">Per eliminare tutti i log, specificare `LogLevel.None` come livello di registrazione minimo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-733">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="5aae7-734">Il valore intero di `LogLevel.None` è 6, maggiore di `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="5aae7-734">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="5aae7-735">Creare regole di filtro nella configurazione</span><span class="sxs-lookup"><span data-stu-id="5aae7-735">Create filter rules in configuration</span></span>

<span data-ttu-id="5aae7-736">Il codice del `CreateDefaultBuilder` modello di progetto chiama per impostare la registrazione per i provider Console, Debug e EventSource (ASP.NET Core 2.2 o versioni successive).</span><span class="sxs-lookup"><span data-stu-id="5aae7-736">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="5aae7-737">Il metodo `CreateDefaultBuilder` imposta inoltre la registrazione per la ricerca della configurazione in una sezione `Logging`, come illustrato [in precedenza in questo articolo](#configuration).</span><span class="sxs-lookup"><span data-stu-id="5aae7-737">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="5aae7-738">I dati di configurazione specificano i livelli di registrazione minimi in base al provider e alla categoria, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="5aae7-738">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="5aae7-739">Questo codice JSON crea sei regole di filtro, una per il provider Debug, quattro per il provider Console e una per tutti i provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-739">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="5aae7-740">Quando viene creato un oggetto `ILogger`, viene scelta una singola regola per ogni provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-740">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="5aae7-741">Regole di filtro nel codice</span><span class="sxs-lookup"><span data-stu-id="5aae7-741">Filter rules in code</span></span>

<span data-ttu-id="5aae7-742">L'esempio seguente illustra come registrare le regole di filtro nel codice:</span><span class="sxs-lookup"><span data-stu-id="5aae7-742">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="5aae7-743">Il secondo `AddFilter` specifica il provider Debug tramite il nome del tipo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-743">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="5aae7-744">Il primo `AddFilter` si applica a tutti i provider in quanto non consente di specificare un tipo di provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-744">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="5aae7-745">Applicazione delle regole di filtro</span><span class="sxs-lookup"><span data-stu-id="5aae7-745">How filtering rules are applied</span></span>

<span data-ttu-id="5aae7-746">I dati di configurazione e il codice `AddFilter` illustrato negli esempi precedenti creano le regole indicate nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="5aae7-746">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="5aae7-747">I primi sei provengono dall'esempio di configurazione e gli ultimi due dall'esempio di codice.</span><span class="sxs-lookup"><span data-stu-id="5aae7-747">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="5aae7-748">Number</span><span class="sxs-lookup"><span data-stu-id="5aae7-748">Number</span></span> | <span data-ttu-id="5aae7-749">Provider</span><span class="sxs-lookup"><span data-stu-id="5aae7-749">Provider</span></span>      | <span data-ttu-id="5aae7-750">Categorie che iniziano con...</span><span class="sxs-lookup"><span data-stu-id="5aae7-750">Categories that begin with ...</span></span>          | <span data-ttu-id="5aae7-751">Livello di registrazione minimo</span><span class="sxs-lookup"><span data-stu-id="5aae7-751">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="5aae7-752">1</span><span class="sxs-lookup"><span data-stu-id="5aae7-752">1</span></span>      | <span data-ttu-id="5aae7-753">Debug</span><span class="sxs-lookup"><span data-stu-id="5aae7-753">Debug</span></span>         | <span data-ttu-id="5aae7-754">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="5aae7-754">All categories</span></span>                          | <span data-ttu-id="5aae7-755">Informazioni</span><span class="sxs-lookup"><span data-stu-id="5aae7-755">Information</span></span>       |
| <span data-ttu-id="5aae7-756">2</span><span class="sxs-lookup"><span data-stu-id="5aae7-756">2</span></span>      | <span data-ttu-id="5aae7-757">Console</span><span class="sxs-lookup"><span data-stu-id="5aae7-757">Console</span></span>       | <span data-ttu-id="5aae7-758">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="5aae7-758">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="5aae7-759">Avviso</span><span class="sxs-lookup"><span data-stu-id="5aae7-759">Warning</span></span>           |
| <span data-ttu-id="5aae7-760">3</span><span class="sxs-lookup"><span data-stu-id="5aae7-760">3</span></span>      | <span data-ttu-id="5aae7-761">Console</span><span class="sxs-lookup"><span data-stu-id="5aae7-761">Console</span></span>       | <span data-ttu-id="5aae7-762">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="5aae7-762">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="5aae7-763">Debug</span><span class="sxs-lookup"><span data-stu-id="5aae7-763">Debug</span></span>             |
| <span data-ttu-id="5aae7-764">4</span><span class="sxs-lookup"><span data-stu-id="5aae7-764">4</span></span>      | <span data-ttu-id="5aae7-765">Console</span><span class="sxs-lookup"><span data-stu-id="5aae7-765">Console</span></span>       | <span data-ttu-id="5aae7-766">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="5aae7-766">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="5aae7-767">Errore</span><span class="sxs-lookup"><span data-stu-id="5aae7-767">Error</span></span>             |
| <span data-ttu-id="5aae7-768">5</span><span class="sxs-lookup"><span data-stu-id="5aae7-768">5</span></span>      | <span data-ttu-id="5aae7-769">Console</span><span class="sxs-lookup"><span data-stu-id="5aae7-769">Console</span></span>       | <span data-ttu-id="5aae7-770">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="5aae7-770">All categories</span></span>                          | <span data-ttu-id="5aae7-771">Informazioni</span><span class="sxs-lookup"><span data-stu-id="5aae7-771">Information</span></span>       |
| <span data-ttu-id="5aae7-772">6</span><span class="sxs-lookup"><span data-stu-id="5aae7-772">6</span></span>      | <span data-ttu-id="5aae7-773">Tutti i provider</span><span class="sxs-lookup"><span data-stu-id="5aae7-773">All providers</span></span> | <span data-ttu-id="5aae7-774">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="5aae7-774">All categories</span></span>                          | <span data-ttu-id="5aae7-775">Debug</span><span class="sxs-lookup"><span data-stu-id="5aae7-775">Debug</span></span>             |
| <span data-ttu-id="5aae7-776">7</span><span class="sxs-lookup"><span data-stu-id="5aae7-776">7</span></span>      | <span data-ttu-id="5aae7-777">Tutti i provider</span><span class="sxs-lookup"><span data-stu-id="5aae7-777">All providers</span></span> | <span data-ttu-id="5aae7-778">Sistema</span><span class="sxs-lookup"><span data-stu-id="5aae7-778">System</span></span>                                  | <span data-ttu-id="5aae7-779">Debug</span><span class="sxs-lookup"><span data-stu-id="5aae7-779">Debug</span></span>             |
| <span data-ttu-id="5aae7-780">8</span><span class="sxs-lookup"><span data-stu-id="5aae7-780">8</span></span>      | <span data-ttu-id="5aae7-781">Debug</span><span class="sxs-lookup"><span data-stu-id="5aae7-781">Debug</span></span>         | <span data-ttu-id="5aae7-782">Microsoft</span><span class="sxs-lookup"><span data-stu-id="5aae7-782">Microsoft</span></span>                               | <span data-ttu-id="5aae7-783">Trace</span><span class="sxs-lookup"><span data-stu-id="5aae7-783">Trace</span></span>             |

<span data-ttu-id="5aae7-784">Quando viene creato un oggetto `ILogger`, l'oggetto `ILoggerFactory` seleziona una singola regola per ogni provider da applicare al logger.</span><span class="sxs-lookup"><span data-stu-id="5aae7-784">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="5aae7-785">Tutti i messaggi scritti da un'istanza di `ILogger` vengono filtrati in base alle regole selezionate.</span><span class="sxs-lookup"><span data-stu-id="5aae7-785">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="5aae7-786">Tra le regole disponibili viene selezionata la regola più specifica possibile per ogni coppia di categoria e provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-786">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="5aae7-787">L'algoritmo seguente viene usato per ogni provider quando viene creato un `ILogger` per una determinata categoria:</span><span class="sxs-lookup"><span data-stu-id="5aae7-787">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="5aae7-788">Selezionare tutte le regole corrispondenti al provider o al relativo alias.</span><span class="sxs-lookup"><span data-stu-id="5aae7-788">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="5aae7-789">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole con un provider vuoto.</span><span class="sxs-lookup"><span data-stu-id="5aae7-789">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="5aae7-790">Dal risultato del passaggio precedente, selezionare le regole con il prefisso di categoria corrispondente più lungo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-790">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="5aae7-791">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole che non specificano una categoria.</span><span class="sxs-lookup"><span data-stu-id="5aae7-791">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="5aae7-792">Se sono selezionate più regole, scegliere l'**ultima**.</span><span class="sxs-lookup"><span data-stu-id="5aae7-792">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="5aae7-793">Se non è selezionata alcuna regola, usare `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-793">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="5aae7-794">Con l'elenco di regole precedente, si supponga di creare un oggetto `ILogger` per la categoria "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="5aae7-794">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="5aae7-795">Per il provider Debug si applicano le regole 1, 6 e 8.</span><span class="sxs-lookup"><span data-stu-id="5aae7-795">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="5aae7-796">La regola 8 è più specifica, così sarà quella selezionata.</span><span class="sxs-lookup"><span data-stu-id="5aae7-796">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="5aae7-797">Per il provider Console si applicano le regole 3, 4, 5 e 6.</span><span class="sxs-lookup"><span data-stu-id="5aae7-797">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="5aae7-798">La regola 3 è la più specifica.</span><span class="sxs-lookup"><span data-stu-id="5aae7-798">Rule 3 is most specific.</span></span>

<span data-ttu-id="5aae7-799">L'istanza di `ILogger` risultante invia i log di livello `Trace` o superiore al provider Debug.</span><span class="sxs-lookup"><span data-stu-id="5aae7-799">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="5aae7-800">I log di livello `Debug` o superiore vengono inviati al provider Console.</span><span class="sxs-lookup"><span data-stu-id="5aae7-800">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="5aae7-801">Alias dei provider</span><span class="sxs-lookup"><span data-stu-id="5aae7-801">Provider aliases</span></span>

<span data-ttu-id="5aae7-802">Ogni provider definisce un *alias* che può essere utilizzato nella configurazione al posto del nome completo di tipo.</span><span class="sxs-lookup"><span data-stu-id="5aae7-802">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="5aae7-803">Per i provider predefiniti, usare gli alias seguenti:</span><span class="sxs-lookup"><span data-stu-id="5aae7-803">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="5aae7-804">Console</span><span class="sxs-lookup"><span data-stu-id="5aae7-804">Console</span></span>
* <span data-ttu-id="5aae7-805">Debug</span><span class="sxs-lookup"><span data-stu-id="5aae7-805">Debug</span></span>
* <span data-ttu-id="5aae7-806">EventSource</span><span class="sxs-lookup"><span data-stu-id="5aae7-806">EventSource</span></span>
* <span data-ttu-id="5aae7-807">EventLog</span><span class="sxs-lookup"><span data-stu-id="5aae7-807">EventLog</span></span>
* <span data-ttu-id="5aae7-808">TraceSource</span><span class="sxs-lookup"><span data-stu-id="5aae7-808">TraceSource</span></span>
* <span data-ttu-id="5aae7-809">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="5aae7-809">AzureAppServicesFile</span></span>
* <span data-ttu-id="5aae7-810">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="5aae7-810">AzureAppServicesBlob</span></span>
* <span data-ttu-id="5aae7-811">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="5aae7-811">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="5aae7-812">Livello minimo predefinito</span><span class="sxs-lookup"><span data-stu-id="5aae7-812">Default minimum level</span></span>

<span data-ttu-id="5aae7-813">Esiste un'impostazione del livello minimo che diventa effettiva solo se non si applica alcuna regola di configurazione o codice per un provider e una categoria specifici.</span><span class="sxs-lookup"><span data-stu-id="5aae7-813">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="5aae7-814">L'esempio seguente illustra come impostare il livello minimo:</span><span class="sxs-lookup"><span data-stu-id="5aae7-814">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="5aae7-815">Se non si imposta in modo esplicito il livello minimo, il valore predefinito è `Information`, che indica che i log `Trace` e `Debug` vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="5aae7-815">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="5aae7-816">Funzioni di filtro</span><span class="sxs-lookup"><span data-stu-id="5aae7-816">Filter functions</span></span>

<span data-ttu-id="5aae7-817">Una funzione di filtro viene richiamata per tutti i provider e le categorie a cui non sono assegnate regole tramite la configurazione o il codice.</span><span class="sxs-lookup"><span data-stu-id="5aae7-817">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="5aae7-818">Il codice della funzione ha accesso al tipo di provider, alla categoria e al livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-818">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="5aae7-819">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5aae7-819">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="5aae7-820">Livelli e categorie di sistema</span><span class="sxs-lookup"><span data-stu-id="5aae7-820">System categories and levels</span></span>

<span data-ttu-id="5aae7-821">Di seguito sono elencate alcune categorie usate da ASP.NET Core ed Entity Framework Core, con note sui log previsti per ognuna:</span><span class="sxs-lookup"><span data-stu-id="5aae7-821">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="5aae7-822">Category</span><span class="sxs-lookup"><span data-stu-id="5aae7-822">Category</span></span>                            | <span data-ttu-id="5aae7-823">Note</span><span class="sxs-lookup"><span data-stu-id="5aae7-823">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="5aae7-824">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="5aae7-824">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="5aae7-825">Diagnostica generale di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5aae7-825">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="5aae7-826">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="5aae7-826">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="5aae7-827">Chiavi considerate, trovate e usate.</span><span class="sxs-lookup"><span data-stu-id="5aae7-827">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="5aae7-828">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="5aae7-828">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="5aae7-829">Host consentiti.</span><span class="sxs-lookup"><span data-stu-id="5aae7-829">Hosts allowed.</span></span> |
| <span data-ttu-id="5aae7-830">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="5aae7-830">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="5aae7-831">Tempo impiegato per il completamento delle richieste HTTP e ora di inizio delle richieste.</span><span class="sxs-lookup"><span data-stu-id="5aae7-831">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="5aae7-832">Assembly di avvio dell'hosting caricati.</span><span class="sxs-lookup"><span data-stu-id="5aae7-832">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="5aae7-833">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="5aae7-833">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="5aae7-834">Diagnostica di MVC e Razor.</span><span class="sxs-lookup"><span data-stu-id="5aae7-834">MVC and Razor diagnostics.</span></span> <span data-ttu-id="5aae7-835">Associazione di modelli, esecuzione di filtri, compilazione di viste, selezione di azioni.</span><span class="sxs-lookup"><span data-stu-id="5aae7-835">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="5aae7-836">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="5aae7-836">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="5aae7-837">Informazioni sulla corrispondenza di route.</span><span class="sxs-lookup"><span data-stu-id="5aae7-837">Route matching information.</span></span> |
| <span data-ttu-id="5aae7-838">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="5aae7-838">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="5aae7-839">Risposte di avvio, arresto e keep-alive per la connessione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-839">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="5aae7-840">Informazioni sui certificati HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5aae7-840">HTTPS certificate information.</span></span> |
| <span data-ttu-id="5aae7-841">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="5aae7-841">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="5aae7-842">File forniti.</span><span class="sxs-lookup"><span data-stu-id="5aae7-842">Files served.</span></span> |
| <span data-ttu-id="5aae7-843">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="5aae7-843">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="5aae7-844">Diagnostica generale di Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="5aae7-844">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="5aae7-845">Attività e configurazione del database, rilevamento delle modifiche, migrazioni.</span><span class="sxs-lookup"><span data-stu-id="5aae7-845">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="5aae7-846">Ambiti dei log</span><span class="sxs-lookup"><span data-stu-id="5aae7-846">Log scopes</span></span>

 <span data-ttu-id="5aae7-847">Un *ambito* può raggruppare un set di operazioni logiche.</span><span class="sxs-lookup"><span data-stu-id="5aae7-847">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="5aae7-848">Questo raggruppamento può essere usato per collegare gli stessi dati a ogni log creato come parte di un set.</span><span class="sxs-lookup"><span data-stu-id="5aae7-848">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="5aae7-849">Ad esempio, ogni log creato come parte dell'elaborazione di una transazione può includere l'ID transazione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-849">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="5aae7-850">Un ambito è un tipo `IDisposable` restituito dal metodo <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> e viene mantenuto fino all'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-850">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="5aae7-851">Un ambito si usa mediante il wrapping delle chiamate del logger in un blocco `using`:</span><span class="sxs-lookup"><span data-stu-id="5aae7-851">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="5aae7-852">Il codice seguente abilita gli ambiti per il provider Console:</span><span class="sxs-lookup"><span data-stu-id="5aae7-852">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="5aae7-853">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5aae7-853">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="5aae7-854">La configurazione dell'opzione del logger della console `IncludeScopes` è necessaria per abilitare la registrazione basata sull'ambito.</span><span class="sxs-lookup"><span data-stu-id="5aae7-854">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="5aae7-855">Per informazioni sulla configurazione, vedere la sezione [Configurazione](#configuration).</span><span class="sxs-lookup"><span data-stu-id="5aae7-855">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="5aae7-856">Ogni messaggio di registrazione include le informazioni sull'ambito:</span><span class="sxs-lookup"><span data-stu-id="5aae7-856">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="5aae7-857">Provider di registrazione predefiniti</span><span class="sxs-lookup"><span data-stu-id="5aae7-857">Built-in logging providers</span></span>

<span data-ttu-id="5aae7-858">ASP.NET Core include i provider seguenti:</span><span class="sxs-lookup"><span data-stu-id="5aae7-858">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="5aae7-859">Console</span><span class="sxs-lookup"><span data-stu-id="5aae7-859">Console</span></span>](#console-provider)
* [<span data-ttu-id="5aae7-860">Debug</span><span class="sxs-lookup"><span data-stu-id="5aae7-860">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="5aae7-861">Eventsource</span><span class="sxs-lookup"><span data-stu-id="5aae7-861">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="5aae7-862">EventLog</span><span class="sxs-lookup"><span data-stu-id="5aae7-862">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="5aae7-863">TraceSource</span><span class="sxs-lookup"><span data-stu-id="5aae7-863">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="5aae7-864">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="5aae7-864">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="5aae7-865">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="5aae7-865">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="5aae7-866">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="5aae7-866">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="5aae7-867">Per informazioni su StdOut e la registrazione debug con il modulo ASP.NET Core, vedere <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="5aae7-867">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="5aae7-868">Provider Console</span><span class="sxs-lookup"><span data-stu-id="5aae7-868">Console provider</span></span>

<span data-ttu-id="5aae7-869">Il pacchetto del provider [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) invia l'output della registrazione alla console.</span><span class="sxs-lookup"><span data-stu-id="5aae7-869">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="5aae7-870">Per visualizzare l'output di registrazione del provider Console, aprire un prompt dei comandi nella cartella del progetto ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="5aae7-870">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="5aae7-871">Provider Debug</span><span class="sxs-lookup"><span data-stu-id="5aae7-871">Debug provider</span></span>

<span data-ttu-id="5aae7-872">Il pacchetto del provider [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) scrive l'output della registrazione usando la classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (chiamate del metodo `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="5aae7-872">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="5aae7-873">In Linux, questo provider scrive i log in */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="5aae7-873">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="5aae7-874">Provider dell'origine eventi</span><span class="sxs-lookup"><span data-stu-id="5aae7-874">Event Source provider</span></span>

<span data-ttu-id="5aae7-875">Il pacchetto del provider [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) viene scritto in `Microsoft-Extensions-Logging`una piattaforma multipiattaforma Origine evento con il nome .</span><span class="sxs-lookup"><span data-stu-id="5aae7-875">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="5aae7-876">In Windows, il provider utilizza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="5aae7-876">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="5aae7-877">Il provider di origine `CreateDefaultBuilder` evento viene aggiunto automaticamente quando viene chiamato per compilare l'host.</span><span class="sxs-lookup"><span data-stu-id="5aae7-877">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="5aae7-878">Utilizzare [l'utilità PerfView](https://github.com/Microsoft/perfview) per raccogliere e visualizzare i log.</span><span class="sxs-lookup"><span data-stu-id="5aae7-878">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="5aae7-879">Sono disponibili altri strumenti per la visualizzazione dei log ETW, ma PerfView fornisce un'esperienza ottimale per l'uso con gli eventi ETW generati da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5aae7-879">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="5aae7-880">Per configurare PerfView per la raccolta degli eventi registrati da questo provider, aggiungere la stringa `*Microsoft-Extensions-Logging` nell'elenco **Provider aggiuntivi**</span><span class="sxs-lookup"><span data-stu-id="5aae7-880">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="5aae7-881">(non dimenticare l'asterisco all'inizio della stringa).</span><span class="sxs-lookup"><span data-stu-id="5aae7-881">(Don't miss the asterisk at the start of the string.)</span></span>

![Provider aggiuntivi di PerfView](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="5aae7-883">Provider EventLog di Windows</span><span class="sxs-lookup"><span data-stu-id="5aae7-883">Windows EventLog provider</span></span>

<span data-ttu-id="5aae7-884">Il pacchetto di provider [Microsoft.Extensions.Logging.AventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) invia l'output del log al registro eventi di Windows.</span><span class="sxs-lookup"><span data-stu-id="5aae7-884">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="5aae7-885">Gli [overload di AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) consentono di passare <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="5aae7-885">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="5aae7-886">Se `null` specificato o non specificato, vengono utilizzate le seguenti impostazioni predefinite:</span><span class="sxs-lookup"><span data-stu-id="5aae7-886">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="5aae7-887">`LogName`&ndash; "Applicazione"</span><span class="sxs-lookup"><span data-stu-id="5aae7-887">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="5aae7-888">`SourceName`&ndash; "Runtime .NET"</span><span class="sxs-lookup"><span data-stu-id="5aae7-888">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="5aae7-889">`MachineName` &ndash; computer locale</span><span class="sxs-lookup"><span data-stu-id="5aae7-889">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="5aae7-890">Gli eventi vengono registrati per [il livello di avviso e superiore](#log-level).</span><span class="sxs-lookup"><span data-stu-id="5aae7-890">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="5aae7-891">Per registrare `Warning`eventi inferiori a , impostare in modo esplicito il livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-891">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="5aae7-892">Ad esempio, aggiungere quanto segue al file *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="5aae7-892">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="5aae7-893">Provider TraceSource</span><span class="sxs-lookup"><span data-stu-id="5aae7-893">TraceSource provider</span></span>

<span data-ttu-id="5aae7-894">Il pacchetto di provider [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa le librerie e i provider <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="5aae7-894">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="5aae7-895">Gli [overload di AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) consentono di passare un cambio di origine e un listener di traccia.</span><span class="sxs-lookup"><span data-stu-id="5aae7-895">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="5aae7-896">Per usare questo provider, un'app deve essere in esecuzione in .NET Framework (invece che in .NET Core).</span><span class="sxs-lookup"><span data-stu-id="5aae7-896">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="5aae7-897">Il provider può instradare i messaggi a diversi [listener](/dotnet/framework/debug-trace-profile/trace-listeners), come <xref:System.Diagnostics.TextWriterTraceListener>, usato nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="5aae7-897">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="5aae7-898">Provider del Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="5aae7-898">Azure App Service provider</span></span>

<span data-ttu-id="5aae7-899">Il pacchetto di provider [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) scrive i log in file di testo nel file system di un'app del Servizio app di Azure e nell'[archivio di BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in un account di archiviazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="5aae7-899">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="5aae7-900">Il pacchetto del provider non è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="5aae7-900">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="5aae7-901">Quando la destinazione è .NET Framework o il riferimento è il metapacchetto `Microsoft.AspNetCore.App`, aggiungere il pacchetto del provider al progetto.</span><span class="sxs-lookup"><span data-stu-id="5aae7-901">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="5aae7-902">Un overload di <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> consente di passare <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="5aae7-902">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="5aae7-903">L'oggetto impostazioni può eseguire l'override delle impostazioni predefinite, ad esempio il modello di output di registrazione, il nome di BLOB e il limite di dimensioni di file.</span><span class="sxs-lookup"><span data-stu-id="5aae7-903">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="5aae7-904">Il *modello di output* è un modello di messaggio che viene applicato a tutti i log in aggiunta a quanto specificato quando si chiama un metodo `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-904">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="5aae7-905">Quando si distribuisce un'app del servizio app, l'applicazione applica le impostazioni della sezione [Log del servizio app](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) della pagina **Servizio app** del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="5aae7-905">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="5aae7-906">Quando le impostazioni seguenti vengono aggiornate, le modifiche hanno effetto immediatamente senza richiedere un riavvio o la ridistribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-906">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="5aae7-907">**Registrazione applicazioni (file system)**</span><span class="sxs-lookup"><span data-stu-id="5aae7-907">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="5aae7-908">**Registrazione applicazione (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="5aae7-908">**Application Logging (Blob)**</span></span>

<span data-ttu-id="5aae7-909">Il percorso predefinito per i file di log è nella cartella *D:\\home\\LogFiles\\Application* e il nome di file predefinito è *diagnostics-aaaammgg.txt*.</span><span class="sxs-lookup"><span data-stu-id="5aae7-909">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="5aae7-910">Il limite predefinito per le dimensioni del file è 10 MB e il numero massimo predefinito di file conservati è 2.</span><span class="sxs-lookup"><span data-stu-id="5aae7-910">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="5aae7-911">Il nome BLOB predefinito è *{nome-app}{timestamp}/aaaa/mm/gg/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="5aae7-911">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="5aae7-912">Il provider funziona solo quando il progetto viene eseguito nell'ambiente di Azure.</span><span class="sxs-lookup"><span data-stu-id="5aae7-912">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="5aae7-913">Non ha alcun effetto quando il progetto viene eseguito localmente, in quanto non scrive nulla in file locali o in archivi di sviluppo locali per i BLOB.</span><span class="sxs-lookup"><span data-stu-id="5aae7-913">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="5aae7-914">Flusso di registrazione di Azure</span><span class="sxs-lookup"><span data-stu-id="5aae7-914">Azure log streaming</span></span>

<span data-ttu-id="5aae7-915">Il flusso di registrazione di Azure consente di visualizzare l'attività di registrazione in tempo reale da:</span><span class="sxs-lookup"><span data-stu-id="5aae7-915">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="5aae7-916">Server applicazioni</span><span class="sxs-lookup"><span data-stu-id="5aae7-916">The app server</span></span>
* <span data-ttu-id="5aae7-917">Server Web</span><span class="sxs-lookup"><span data-stu-id="5aae7-917">The web server</span></span>
* <span data-ttu-id="5aae7-918">Traccia delle richieste non riuscite</span><span class="sxs-lookup"><span data-stu-id="5aae7-918">Failed request tracing</span></span>

<span data-ttu-id="5aae7-919">Per configurare il flusso di registrazione di Azure:</span><span class="sxs-lookup"><span data-stu-id="5aae7-919">To configure Azure log streaming:</span></span>

* <span data-ttu-id="5aae7-920">Passare alla pagina **Log del servizio app** dalla pagina del portale dell'app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-920">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="5aae7-921">Impostare **Registrazione applicazioni (file system)** su **Attiva**.</span><span class="sxs-lookup"><span data-stu-id="5aae7-921">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="5aae7-922">Scegliere il livello di registrazione in **Livello**.</span><span class="sxs-lookup"><span data-stu-id="5aae7-922">Choose the log **Level**.</span></span> <span data-ttu-id="5aae7-923">Questa impostazione si applica solo allo streaming dei log di Azure, non agli altri provider di registrazione nell'app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-923">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="5aae7-924">Passare alla pagina **Flusso di registrazione** per visualizzare i messaggi dell'app.</span><span class="sxs-lookup"><span data-stu-id="5aae7-924">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="5aae7-925">I messaggi vengono registrati dall'app tramite l'interfaccia `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="5aae7-925">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="5aae7-926">Registrazione di traccia di Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="5aae7-926">Azure Application Insights trace logging</span></span>

<span data-ttu-id="5aae7-927">Il pacchetto di provider [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) scrive log in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="5aae7-927">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="5aae7-928">Application Insights è un servizio che monitora un'app Web e fornisce gli strumenti per l'esecuzione di query sui dati di telemetria e la loro analisi.</span><span class="sxs-lookup"><span data-stu-id="5aae7-928">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="5aae7-929">Se si usa questo provider, è possibile eseguire query sui log e analizzarli usando gli strumenti di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="5aae7-929">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="5aae7-930">Il provider di registrazione è incluso come dipendenza di [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), ovvero il pacchetto che fornisce tutti i dati di telemetria disponibili per ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5aae7-930">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="5aae7-931">Se si usa questo pacchetto, non è necessario installare il pacchetto di provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-931">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="5aae7-932">Non usare il pacchetto [](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)Microsoft.ApplicationInsights.Web&mdash;, che è per ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="5aae7-932">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="5aae7-933">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="5aae7-933">For more information, see the following resources:</span></span>

* [<span data-ttu-id="5aae7-934">Panoramica di Application Insights</span><span class="sxs-lookup"><span data-stu-id="5aae7-934">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="5aae7-935">[Application Insights per applicazioni ASP.NET Core](/azure/azure-monitor/app/asp-net-core): iniziare da qui se si vuole implementare l'intervallo completo dei dati di telemetria di Application Insights insieme alla registrazione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-935">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="5aae7-936">[ApplicationInsightsLoggerProvider per log ILogger .NET Core](/azure/azure-monitor/app/ilogger): iniziare da qui se si vuole implementare il provider di registrazione senza gli altri dati di telemetria di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="5aae7-936">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="5aae7-937">[Adattatori di registrazione di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="5aae7-937">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="5aae7-938">[Installare, configurare e inizializzare Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): esercitazione interattiva nel sito Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="5aae7-938">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="5aae7-939">Provider di registrazione di terze parti</span><span class="sxs-lookup"><span data-stu-id="5aae7-939">Third-party logging providers</span></span>

<span data-ttu-id="5aae7-940">Framework di registrazione di terze parti che usano ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5aae7-940">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="5aae7-941">[elmah.io](https://elmah.io/) ([repository GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="5aae7-941">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="5aae7-942">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repository GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="5aae7-942">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="5aae7-943">[JSNLog](https://jsnlog.com/) ([repository GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="5aae7-943">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="5aae7-944">[KissLog.net](https://kisslog.net/) ([repository GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="5aae7-944">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="5aae7-945">[Log4Net](https://logging.apache.org/log4net/) ([repository GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="5aae7-945">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="5aae7-946">[Loggr](https://loggr.net/) ([repository GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="5aae7-946">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="5aae7-947">[NLog](https://nlog-project.org/) ([repository GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="5aae7-947">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="5aae7-948">[Sentry](https://sentry.io/welcome/) ([repository GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="5aae7-948">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="5aae7-949">[Serilog](https://serilog.net/) ([repository GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="5aae7-949">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="5aae7-950">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repository Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="5aae7-950">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="5aae7-951">Alcuni framework di terze parti possono eseguire la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="5aae7-951">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="5aae7-952">L'uso di un framework di terze parti è simile a quello di uno dei provider predefiniti:</span><span class="sxs-lookup"><span data-stu-id="5aae7-952">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="5aae7-953">Aggiungere un pacchetto NuGet al progetto.</span><span class="sxs-lookup"><span data-stu-id="5aae7-953">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="5aae7-954">Chiamare `ILoggerFactory` un metodo di estensione fornito dal framework di registrazione.</span><span class="sxs-lookup"><span data-stu-id="5aae7-954">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="5aae7-955">Per altre informazioni, vedere la documentazione di ogni provider.</span><span class="sxs-lookup"><span data-stu-id="5aae7-955">For more information, see each provider's documentation.</span></span> <span data-ttu-id="5aae7-956">I provider di registrazione di terze parti non sono supportati da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="5aae7-956">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5aae7-957">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="5aae7-957">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
