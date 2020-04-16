---
title: Registrazione in .NET Core e ASP.NET Core
author: rick-anderson
description: Informazioni su come usare il framework di registrazione fornito dal pacchetto NuGet Microsoft.Extensions.Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/logging/index
ms.openlocfilehash: a3c63b738d3eaa51249475b88d78572038348a7a
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440740"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="dbce8-103">Registrazione in .NET Core e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dbce8-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dbce8-104">Di [Tom Dykstra](https://github.com/tdykstra) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="dbce8-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="dbce8-105">.NET Core supporta un'API di registrazione che funziona con un'ampia gamma di provider di registrazione predefiniti e di terze parti.</span><span class="sxs-lookup"><span data-stu-id="dbce8-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="dbce8-106">Questo articolo illustra come usare l'API di registrazione con i provider predefiniti.</span><span class="sxs-lookup"><span data-stu-id="dbce8-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="dbce8-107">La maggior parte degli esempi di codice illustrati in questo articolo proviene da app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbce8-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="dbce8-108">Le parti specifiche della registrazione di questi frammenti di codice si applicano a qualsiasi app .NET Core che usa [l'host generico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="dbce8-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="dbce8-109">Per un esempio di come usare l'host generico in un'app non console<xref:fundamentals/host/hosted-services>Web, vedere il file *Program.cs* dell'app di esempio Attività in [background](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) ( ).</span><span class="sxs-lookup"><span data-stu-id="dbce8-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="dbce8-110">Il codice di registrazione per le app senza host generico differisce per il modo in cui vengono [aggiunti i provider](#add-providers) e [creati i logger](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="dbce8-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="dbce8-111">Gli esempi di codice non host sono illustrati nelle sezioni dell'articolo in cui sono riportate queste procedure.</span><span class="sxs-lookup"><span data-stu-id="dbce8-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="dbce8-112">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dbce8-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="dbce8-113">Aggiungere provider</span><span class="sxs-lookup"><span data-stu-id="dbce8-113">Add providers</span></span>

<span data-ttu-id="dbce8-114">Un provider di registrazione visualizza o archivia i log.</span><span class="sxs-lookup"><span data-stu-id="dbce8-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="dbce8-115">Il provider Console, ad esempio, visualizza i log nella console, mentre il provider Azure Application Insights li archivia in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="dbce8-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="dbce8-116">I log possono essere inviati a più destinazioni aggiungendo più provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="dbce8-117">Per aggiungere un provider in un'app che usa l'host generico, chiamare il metodo di estensione `Add{provider name}` del provider in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="dbce8-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="dbce8-118">In un'app console non host chiamare il metodo di estensione `Add{provider name}` del provider durante la creazione di un oggetto `LoggerFactory`:</span><span class="sxs-lookup"><span data-stu-id="dbce8-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="dbce8-119">`LoggerFactory` e `AddConsole` richiedono un'istruzione `using` per `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="dbce8-120">I modelli di progetto ASP.NET Core predefiniti chiamano <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, che aggiunge i provider di registrazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="dbce8-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="dbce8-121">Console</span><span class="sxs-lookup"><span data-stu-id="dbce8-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="dbce8-122">Debug</span><span class="sxs-lookup"><span data-stu-id="dbce8-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="dbce8-123">Eventsource</span><span class="sxs-lookup"><span data-stu-id="dbce8-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="dbce8-124">[EventLog](#windows-eventlog-provider) (solo quando è in esecuzione in Windows)</span><span class="sxs-lookup"><span data-stu-id="dbce8-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="dbce8-125">È possibile sostituire i provider predefiniti con quelli di propria scelta.</span><span class="sxs-lookup"><span data-stu-id="dbce8-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="dbce8-126">Chiamare <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e aggiungere i provider desiderati.</span><span class="sxs-lookup"><span data-stu-id="dbce8-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="dbce8-127">Altre informazioni sui [provider di registrazione predefiniti](#built-in-logging-providers) e sui [provider di registrazione di terze parti](#third-party-logging-providers) vengono fornite più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="dbce8-128">Creare log</span><span class="sxs-lookup"><span data-stu-id="dbce8-128">Create logs</span></span>

<span data-ttu-id="dbce8-129">Per creare i log, usare un oggetto <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="dbce8-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="dbce8-130">In un'app Web o in un servizio ospitato, ottenere un oggetto `ILogger` da un inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dbce8-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="dbce8-131">Nelle app console non host, usare `LoggerFactory` per creare un oggetto `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="dbce8-132">L'esempio seguente di ASP.NET Core crea un logger con `TodoApiSample.Pages.AboutModel` come categoria.</span><span class="sxs-lookup"><span data-stu-id="dbce8-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="dbce8-133">La *categoria* del log è una stringa associata a ogni log.</span><span class="sxs-lookup"><span data-stu-id="dbce8-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="dbce8-134">L'istanza di `ILogger<T>` fornita dall'inserimento delle dipendenze crea log con nome completo di tipo `T` come categoria.</span><span class="sxs-lookup"><span data-stu-id="dbce8-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="dbce8-135">L'esempio seguente di app console non host crea un logger con `LoggingConsoleApp.Program` come categoria.</span><span class="sxs-lookup"><span data-stu-id="dbce8-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="dbce8-136">Negli esempi seguenti di ASP.NET Core e app console, il logger viene usato per creare log con `Information` come livello.</span><span class="sxs-lookup"><span data-stu-id="dbce8-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="dbce8-137">Il *livello* del log indica la gravità dell'evento registrato.</span><span class="sxs-lookup"><span data-stu-id="dbce8-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="dbce8-138">[Livelli](#log-level) e [categorie](#log-category) vengono illustrati in modo dettagliato più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="dbce8-139">Creare log nella classe Program</span><span class="sxs-lookup"><span data-stu-id="dbce8-139">Create logs in the Program class</span></span>

<span data-ttu-id="dbce8-140">Per scrivere log nella classe `Program` di un'app ASP.NET Core, ottenere un'istanza di `ILogger` dall'inserimento delle dipendenze dopo aver creato l'host:</span><span class="sxs-lookup"><span data-stu-id="dbce8-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="dbce8-141">La registrazione durante la costruzione dell'host non è supportata direttamente.</span><span class="sxs-lookup"><span data-stu-id="dbce8-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="dbce8-142">Tuttavia, è possibile utilizzare un logger separato.</span><span class="sxs-lookup"><span data-stu-id="dbce8-142">However, a separate logger can be used.</span></span> <span data-ttu-id="dbce8-143">Nell'esempio seguente viene utilizzato un logger [Serilog](https://serilog.net/) per l'accesso `CreateHostBuilder`a .</span><span class="sxs-lookup"><span data-stu-id="dbce8-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="dbce8-144">`AddSerilog`utilizza la configurazione `Log.Logger`statica specificata in :</span><span class="sxs-lookup"><span data-stu-id="dbce8-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="dbce8-145">Creare log nella classe Startup</span><span class="sxs-lookup"><span data-stu-id="dbce8-145">Create logs in the Startup class</span></span>

<span data-ttu-id="dbce8-146">Per scrivere log nel metodo `Startup.Configure` di un'app ASP.NET Core, includere un parametro `ILogger` nella firma del metodo:</span><span class="sxs-lookup"><span data-stu-id="dbce8-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="dbce8-147">La scrittura di log prima del completamento della configurazione del contenitore di inserimento delle dipendenze nel metodo `Startup.ConfigureServices` non è supportata:</span><span class="sxs-lookup"><span data-stu-id="dbce8-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="dbce8-148">L'inserimento del logger nel costruttore `Startup` non è supportato.</span><span class="sxs-lookup"><span data-stu-id="dbce8-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="dbce8-149">L'inserimento del logger nella firma del metodo `Startup.ConfigureServices` non è supportato</span><span class="sxs-lookup"><span data-stu-id="dbce8-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="dbce8-150">Questa restrizione è dovuta al fatto che la registrazione dipende dall'inserimento delle dipendenze e dalla configurazione, che a sua volta dipende dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dbce8-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="dbce8-151">Il contenitore di inserimento delle dipendenze non viene configurato finché il metodo `ConfigureServices` non è completato.</span><span class="sxs-lookup"><span data-stu-id="dbce8-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="dbce8-152">L'inserimento di un logger nel costruttore `Startup` è possibile nelle versioni precedenti di ASP.NET Core perché viene creato un contenitore di inserimento delle dipendenze separato per l'host Web.</span><span class="sxs-lookup"><span data-stu-id="dbce8-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="dbce8-153">Per informazioni sul motivo per cui viene creato un solo contenitore per l'host generico, vedere l'[annuncio relativo alle modifiche di rilievo](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="dbce8-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="dbce8-154">Se è necessario configurare un servizio che dipende da `ILogger<T>`, è comunque possibile eseguire questa operazione usando l'inserimento nel costruttore o specificando un metodo factory.</span><span class="sxs-lookup"><span data-stu-id="dbce8-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="dbce8-155">L'approccio con il metodo factory è consigliato solo se non sono disponibili altre opzioni.</span><span class="sxs-lookup"><span data-stu-id="dbce8-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="dbce8-156">Si supponga, ad esempio, di dover specificare una proprietà con un servizio ottenuto dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="dbce8-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="dbce8-157">Il precedente codice evidenziato è un oggetto `Func` che viene eseguito la prima volta che il contenitore di inserimento delle dipendenze deve creare un'istanza di `MyService`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="dbce8-158">È possibile accedere a uno dei servizi registrati in questo modo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-158">You can access any of the registered services in this way.</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="dbce8-159">Evitare l'uso di metodi logger asincroni</span><span class="sxs-lookup"><span data-stu-id="dbce8-159">No asynchronous logger methods</span></span>

<span data-ttu-id="dbce8-160">La registrazione deve essere così rapida da non giustificare l'impatto sulle prestazioni del codice asincrono.</span><span class="sxs-lookup"><span data-stu-id="dbce8-160">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="dbce8-161">Se l'archivio dati di registrazione è lento, non scrivere direttamente al suo interno.</span><span class="sxs-lookup"><span data-stu-id="dbce8-161">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="dbce8-162">Scrivere invece i messaggi di log prima in un archivio veloce e quindi spostarli nell'archivio lento in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="dbce8-162">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="dbce8-163">Ad esempio, se la registrazione viene eseguita in SQL Server, è preferibile non farlo direttamente in un metodo `Log`, poiché i metodi `Log` sono sincroni.</span><span class="sxs-lookup"><span data-stu-id="dbce8-163">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="dbce8-164">Al contrario, aggiungere i messaggi di log in modo sincrono a una coda in memoria e usare un ruolo di lavoro in background per eseguire il pull dei messaggi dalla coda per eseguire le operazioni asincrone di push dei dati in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="dbce8-164">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="dbce8-165">Per altre informazioni, vedere questo problema di GitHub.For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span><span class="sxs-lookup"><span data-stu-id="dbce8-165">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="dbce8-166">Configurazione</span><span class="sxs-lookup"><span data-stu-id="dbce8-166">Configuration</span></span>

<span data-ttu-id="dbce8-167">La configurazione dei provider di registrazione viene fornita da uno o più provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="dbce8-167">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="dbce8-168">Formati di file (INI, JSON e XML).</span><span class="sxs-lookup"><span data-stu-id="dbce8-168">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="dbce8-169">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="dbce8-169">Command-line arguments.</span></span>
* <span data-ttu-id="dbce8-170">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="dbce8-170">Environment variables.</span></span>
* <span data-ttu-id="dbce8-171">Oggetti .NET in memoria.</span><span class="sxs-lookup"><span data-stu-id="dbce8-171">In-memory .NET objects.</span></span>
* <span data-ttu-id="dbce8-172">Archiviazione con [Secret Manager](xref:security/app-secrets) senza crittografia.</span><span class="sxs-lookup"><span data-stu-id="dbce8-172">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="dbce8-173">Un archivio utente non crittografato, come [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="dbce8-173">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="dbce8-174">Provider personalizzati (installati o creati).</span><span class="sxs-lookup"><span data-stu-id="dbce8-174">Custom providers (installed or created).</span></span>

<span data-ttu-id="dbce8-175">Ad esempio, la configurazione di registrazione viene comunemente fornita dalla sezione `Logging` del file di impostazioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-175">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="dbce8-176">L'esempio seguente mostra il contenuto di un file *appsettings.Development.json* tipico:</span><span class="sxs-lookup"><span data-stu-id="dbce8-176">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="dbce8-177">La proprietà `Logging` può avere le proprietà `LogLevel` e quella del provider di log (nell'esempio, Console).</span><span class="sxs-lookup"><span data-stu-id="dbce8-177">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="dbce8-178">La proprietà `LogLevel` in `Logging` specifica il [livello](#log-level) minimo per la registrazione per determinate categorie.</span><span class="sxs-lookup"><span data-stu-id="dbce8-178">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="dbce8-179">Nell'esempio, le categorie `System` e `Microsoft` eseguono la registrazione al livello `Information`, mentre tutte le altre la eseguono al livello `Debug`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-179">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="dbce8-180">Altre proprietà in `Logging` specificano i provider di registrazione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-180">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="dbce8-181">L'esempio si riferisce al provider Console.</span><span class="sxs-lookup"><span data-stu-id="dbce8-181">The example is for the Console provider.</span></span> <span data-ttu-id="dbce8-182">Se un provider supporta gli `IncludeScopes` ambiti di [log](#log-scopes), indica se sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="dbce8-182">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="dbce8-183">Una proprietà del provider (ad esempio `Console` nell'esempio) può specificare anche una proprietà `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-183">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="dbce8-184">`LogLevel` in un provider specifica i livelli di registrazione per tale provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-184">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="dbce8-185">Se i livelli sono specificati in `Logging.{providername}.LogLevel`, eseguono l'override di eventuali valori impostati in `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-185">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="dbce8-186">L'API di registrazione non include uno scenario per modificare i livelli di log mentre un'app è in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-186">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="dbce8-187">Tuttavia, alcuni provider di configurazione sono in grado di ricaricare la configurazione, che ha effetto immediato sulla configurazione della registrazione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-187">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="dbce8-188">Ad esempio, il provider di configurazione `CreateDefaultBuilder` [file](xref:fundamentals/configuration/index#file-configuration-provider), che viene aggiunto da per leggere i file di impostazioni, ricarica la configurazione di registrazione per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="dbce8-188">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="dbce8-189">Se la configurazione viene modificata nel codice mentre un'app è in esecuzione, l'app può chiamare [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) per aggiornare la configurazione di registrazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-189">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="dbce8-190">Per informazioni sull'implementazione dei provider di configurazione, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="dbce8-190">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="dbce8-191">Esempio di output di registrazione</span><span class="sxs-lookup"><span data-stu-id="dbce8-191">Sample logging output</span></span>

<span data-ttu-id="dbce8-192">Con il codice di esempio illustrato nella sezione precedente i log vengono visualizzati nella console quando l'app viene eseguita dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="dbce8-192">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="dbce8-193">Ecco un esempio di output della console:</span><span class="sxs-lookup"><span data-stu-id="dbce8-193">Here's an example of console output:</span></span>

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

<span data-ttu-id="dbce8-194">I log precedenti sono stati generati inviando una richiesta HTTP Get all'app di esempio all'indirizzo `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-194">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="dbce8-195">Ecco un esempio degli stessi log visualizzati nella finestra Debug quando si esegue l'app di esempio in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="dbce8-195">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

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

<span data-ttu-id="dbce8-196">I log creati dalle chiamate a `ILogger` illustrate nella sezione precedente iniziano con "TodoApiSample".</span><span class="sxs-lookup"><span data-stu-id="dbce8-196">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="dbce8-197">I log che iniziano con le categorie "Microsoft" provengono dal codice del framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbce8-197">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="dbce8-198">ASP.NET Core e il codice dell'applicazione usano la stessa API di registrazione e gli stessi provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-198">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="dbce8-199">Il resto di questo articolo spiega alcuni dettagli e le opzioni per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-199">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="dbce8-200">Pacchetti NuGet</span><span class="sxs-lookup"><span data-stu-id="dbce8-200">NuGet packages</span></span>

<span data-ttu-id="dbce8-201">Le interfacce `ILogger` e `ILoggerFactory` si trovano in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) e le loro implementazioni predefinite si trovano in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="dbce8-201">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="dbce8-202">Categoria di log</span><span class="sxs-lookup"><span data-stu-id="dbce8-202">Log category</span></span>

<span data-ttu-id="dbce8-203">Quando viene creato un oggetto `ILogger`, viene specificata la relativa *categoria*.</span><span class="sxs-lookup"><span data-stu-id="dbce8-203">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="dbce8-204">La categoria è inclusa in ogni messaggio di log creato da tale istanza di `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-204">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="dbce8-205">La categoria può essere qualsiasi stringa, ma per convenzione si usa il nome della classe, ad esempio "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="dbce8-205">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="dbce8-206">Usare `ILogger<T>` per ottenere un'istanza di `ILogger` che usa il nome completo di tipo `T` come categoria:</span><span class="sxs-lookup"><span data-stu-id="dbce8-206">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="dbce8-207">Per specificare in modo esplicito la categoria, chiamare `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="dbce8-207">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="dbce8-208">L'uso di `ILogger<T>` equivale a chiamare `CreateLogger` con il nome completo di tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-208">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="dbce8-209">Livello di registrazione</span><span class="sxs-lookup"><span data-stu-id="dbce8-209">Log level</span></span>

<span data-ttu-id="dbce8-210">Ogni log specifica un valore <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="dbce8-210">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="dbce8-211">Il livello di registrazione indica la gravità o l'importanza.</span><span class="sxs-lookup"><span data-stu-id="dbce8-211">The log level indicates the severity or importance.</span></span> <span data-ttu-id="dbce8-212">È ad esempio possibile scrivere un log `Information` quando un metodo termina normalmente e un log `Warning` quando un metodo restituisce un codice di stato *404 Non trovato*.</span><span class="sxs-lookup"><span data-stu-id="dbce8-212">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="dbce8-213">Il codice seguente crea i log `Information` e `Warning`:</span><span class="sxs-lookup"><span data-stu-id="dbce8-213">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="dbce8-214">Nel codice precedente il primo parametro è l'[ID dell'evento di log](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="dbce8-214">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="dbce8-215">Il secondo parametro è un modello di messaggio con segnaposto per i valori degli argomenti forniti dai parametri dei metodi rimanenti.</span><span class="sxs-lookup"><span data-stu-id="dbce8-215">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="dbce8-216">I parametri dei metodi sono descritti nella [sezione relativa al modello di messaggio](#log-message-template) più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-216">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="dbce8-217">I metodi di registrazione che includono il livello nel nome del metodo (ad esempio `LogInformation` e `LogWarning`) sono [metodi di estensione per ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="dbce8-217">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="dbce8-218">Questi metodi chiamano un metodo `Log` che accetta un parametro `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-218">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="dbce8-219">È possibile chiamare il metodo `Log` direttamente anziché chiamare uno di questi metodi di estensione, ma la sintassi è relativamente complessa.</span><span class="sxs-lookup"><span data-stu-id="dbce8-219">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="dbce8-220">Per altre informazioni, vedere <xref:Microsoft.Extensions.Logging.ILogger> e il [codice sorgente delle estensioni del logger](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="dbce8-220">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="dbce8-221">ASP.NET Core definisce i livelli di registrazione seguenti, ordinati dal meno grave al più grave.</span><span class="sxs-lookup"><span data-stu-id="dbce8-221">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="dbce8-222">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="dbce8-222">Trace = 0</span></span>

  <span data-ttu-id="dbce8-223">Per informazioni in genere utili solo per il debug.</span><span class="sxs-lookup"><span data-stu-id="dbce8-223">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="dbce8-224">Questi messaggi possono contenere dati sensibili dell'applicazione e pertanto non devono essere abilitati in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-224">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="dbce8-225">*Disattivato per impostazione predefinita.*</span><span class="sxs-lookup"><span data-stu-id="dbce8-225">*Disabled by default.*</span></span>

* <span data-ttu-id="dbce8-226">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="dbce8-226">Debug = 1</span></span>

  <span data-ttu-id="dbce8-227">Per informazioni che possono essere utili per lo sviluppo e il debug.</span><span class="sxs-lookup"><span data-stu-id="dbce8-227">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="dbce8-228">Esempio: `Entering method Configure with flag set to true.` Abilitare i livelli di registrazione `Debug` nell'ambiente di produzione solo in fase di risoluzione dei problemi, per non fare aumentare eccessivamente il volume dei log.</span><span class="sxs-lookup"><span data-stu-id="dbce8-228">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="dbce8-229">Information = 2</span><span class="sxs-lookup"><span data-stu-id="dbce8-229">Information = 2</span></span>

  <span data-ttu-id="dbce8-230">Per tenere traccia del flusso generale dell'app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-230">For tracking the general flow of the app.</span></span> <span data-ttu-id="dbce8-231">Queste registrazioni hanno in genere un valore a lungo termine.</span><span class="sxs-lookup"><span data-stu-id="dbce8-231">These logs typically have some long-term value.</span></span> <span data-ttu-id="dbce8-232">Esempio: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="dbce8-232">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="dbce8-233">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="dbce8-233">Warning = 3</span></span>

  <span data-ttu-id="dbce8-234">Per gli eventi imprevisti o anomali nel flusso dell'app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-234">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="dbce8-235">Tali eventi potrebbero includere errori o altre condizioni che non provocano l'arresto dell'app, ma che potrebbe essere necessario analizzare.</span><span class="sxs-lookup"><span data-stu-id="dbce8-235">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="dbce8-236">Le eccezioni gestite sono una situazione comune in cui usare il livello di registrazione `Warning`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-236">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="dbce8-237">Esempio: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="dbce8-237">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="dbce8-238">Error = 4</span><span class="sxs-lookup"><span data-stu-id="dbce8-238">Error = 4</span></span>

  <span data-ttu-id="dbce8-239">Per errori ed eccezioni che non possono essere gestiti.</span><span class="sxs-lookup"><span data-stu-id="dbce8-239">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="dbce8-240">Questi messaggi indicano un errore nell'operazione o nell'attività in corso (ad esempio la richiesta HTTP corrente), non un errore a livello di app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-240">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="dbce8-241">Messaggio di registrazione di esempio:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="dbce8-241">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="dbce8-242">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="dbce8-242">Critical = 5</span></span>

  <span data-ttu-id="dbce8-243">Per gli errori che richiedono attenzione immediata.</span><span class="sxs-lookup"><span data-stu-id="dbce8-243">For failures that require immediate attention.</span></span> <span data-ttu-id="dbce8-244">Esempi: scenari di perdita di dati, spazio su disco insufficiente.</span><span class="sxs-lookup"><span data-stu-id="dbce8-244">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="dbce8-245">Usare il livello di registrazione per controllare la quantità di output di log scritto in un supporto di archiviazione specifico o in una finestra.</span><span class="sxs-lookup"><span data-stu-id="dbce8-245">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="dbce8-246">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="dbce8-246">For example:</span></span>

* <span data-ttu-id="dbce8-247">In produzione:</span><span class="sxs-lookup"><span data-stu-id="dbce8-247">In production:</span></span>
  * <span data-ttu-id="dbce8-248">La registrazione `Trace` `Information` ai livelli through produce un volume elevato di messaggi di log dettagliati.</span><span class="sxs-lookup"><span data-stu-id="dbce8-248">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="dbce8-249">Per controllare i costi e non `Trace` `Information` superare i limiti di archiviazione dei dati, eseguire il log-through dei messaggi di livello in un archivio dati con volumi elevati e a basso costo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-249">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="dbce8-250">La `Warning` registrazione `Critical` a livelli tramite livelli produce in genere un numero inferiore di messaggi di log più piccoli.</span><span class="sxs-lookup"><span data-stu-id="dbce8-250">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="dbce8-251">Pertanto, i costi e i limiti di archiviazione in genere non sono un problema, il che si traduce in una maggiore flessibilità di scelta dell'archivio dati.</span><span class="sxs-lookup"><span data-stu-id="dbce8-251">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="dbce8-252">Durante lo sviluppo:</span><span class="sxs-lookup"><span data-stu-id="dbce8-252">During development:</span></span>
  * <span data-ttu-id="dbce8-253">`Warning` Registrare `Critical` i messaggi nella console.</span><span class="sxs-lookup"><span data-stu-id="dbce8-253">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="dbce8-254">Aggiungere `Trace` `Information` i messaggi durante la risoluzione dei problemi.</span><span class="sxs-lookup"><span data-stu-id="dbce8-254">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="dbce8-255">La sezione [Filtro dei log](#log-filtering) più avanti in questo articolo descrive come controllare quali livelli di registrazione gestisce un provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-255">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="dbce8-256">ASP.NET Core scrive log per gli eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="dbce8-256">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="dbce8-257">Gli esempi di log illustrati in precedenza in questo articolo escludono i log al di sotto del livello `Information`, quindi non vengono creati log di livello `Debug` o `Trace`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-257">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="dbce8-258">Ecco un esempio di log della console prodotti eseguendo l'app di esempio configurata in modo da mostrare i log di livello `Debug`:</span><span class="sxs-lookup"><span data-stu-id="dbce8-258">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="dbce8-259">ID evento di registrazione</span><span class="sxs-lookup"><span data-stu-id="dbce8-259">Log event ID</span></span>

<span data-ttu-id="dbce8-260">Ogni log può specificare un *ID evento*.</span><span class="sxs-lookup"><span data-stu-id="dbce8-260">Each log can specify an *event ID*.</span></span> <span data-ttu-id="dbce8-261">A tale scopo, l'app di esempio usa una classe `LoggingEvents` definita a livello locale:</span><span class="sxs-lookup"><span data-stu-id="dbce8-261">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="dbce8-262">Un ID evento associa un set di eventi.</span><span class="sxs-lookup"><span data-stu-id="dbce8-262">An event ID associates a set of events.</span></span> <span data-ttu-id="dbce8-263">Ad esempio, tutti i log correlati alla visualizzazione di un elenco di elementi in una pagina potrebbero essere indicati con 1001.</span><span class="sxs-lookup"><span data-stu-id="dbce8-263">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="dbce8-264">Il provider di log può archiviare l'ID evento in un campo ID o nel messaggio di registrazione oppure non archiviarlo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-264">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="dbce8-265">Il provider Debug non visualizza gli ID evento.</span><span class="sxs-lookup"><span data-stu-id="dbce8-265">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="dbce8-266">Il provider Console visualizza gli ID evento tra parentesi quadre dopo la categoria:</span><span class="sxs-lookup"><span data-stu-id="dbce8-266">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="dbce8-267">Modello di messaggio di registrazione</span><span class="sxs-lookup"><span data-stu-id="dbce8-267">Log message template</span></span>

<span data-ttu-id="dbce8-268">Ogni log specifica un modello di messaggio.</span><span class="sxs-lookup"><span data-stu-id="dbce8-268">Each log specifies a message template.</span></span> <span data-ttu-id="dbce8-269">Il modello di messaggio può contenere segnaposto per i quali vengono forniti argomenti.</span><span class="sxs-lookup"><span data-stu-id="dbce8-269">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="dbce8-270">Usare nomi per i segnaposto, non numeri.</span><span class="sxs-lookup"><span data-stu-id="dbce8-270">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="dbce8-271">L'ordine dei segnaposto, non il loro nome, determina i parametri da usare per fornire i valori corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="dbce8-271">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="dbce8-272">Nel codice seguente, si noti che i nomi dei parametri non sono in sequenza nel modello di messaggio:</span><span class="sxs-lookup"><span data-stu-id="dbce8-272">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="dbce8-273">Questo codice crea un messaggio di log con i valori dei parametri in sequenza:</span><span class="sxs-lookup"><span data-stu-id="dbce8-273">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="dbce8-274">Il framework di registrazione funziona in questo modo per consentire ai provider di registrazione di implementare la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="dbce8-274">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="dbce8-275">Al sistema di registrazione vengono passati gli argomenti e non solo il modello di messaggio formattato.</span><span class="sxs-lookup"><span data-stu-id="dbce8-275">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="dbce8-276">Queste informazioni consentono ai provider di registrazione di archiviare i valori dei parametri come campi.</span><span class="sxs-lookup"><span data-stu-id="dbce8-276">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="dbce8-277">Si supponga, ad esempio, che il metodo logger esegua chiamate simili alla seguente:</span><span class="sxs-lookup"><span data-stu-id="dbce8-277">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="dbce8-278">Se si inviano i log all'archiviazione tabelle di Azure, ogni entità tabella di Azure può avere le proprietà `ID` e `RequestTime`, il che semplifica le query sui dati dei log.</span><span class="sxs-lookup"><span data-stu-id="dbce8-278">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="dbce8-279">Una query può trovare tutti i log entro un determinato intervallo `RequestTime` senza che sia necessario analizzare il messaggio di testo per determinare l'intervallo di tempo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-279">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="dbce8-280">Registrazione delle eccezioni</span><span class="sxs-lookup"><span data-stu-id="dbce8-280">Logging exceptions</span></span>

<span data-ttu-id="dbce8-281">I metodi logger dispongono di overload che consentono di passare un'eccezione, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="dbce8-281">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="dbce8-282">Provider diversi gestiscono le informazioni sulle eccezioni in modi diversi.</span><span class="sxs-lookup"><span data-stu-id="dbce8-282">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="dbce8-283">Ecco un esempio di output del provider Debug dal codice sopra riportato.</span><span class="sxs-lookup"><span data-stu-id="dbce8-283">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="dbce8-284">Filtro del log</span><span class="sxs-lookup"><span data-stu-id="dbce8-284">Log filtering</span></span>

<span data-ttu-id="dbce8-285">È possibile specificare un livello di registrazione minimo per un provider e una categoria specifici oppure per tutti i provider o tutte le categorie.</span><span class="sxs-lookup"><span data-stu-id="dbce8-285">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="dbce8-286">Tutti i log sotto il livello minimo non sono passati al provider, quindi non vengono visualizzati o archiviati.</span><span class="sxs-lookup"><span data-stu-id="dbce8-286">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="dbce8-287">Per eliminare tutti i log, specificare `LogLevel.None` come livello di registrazione minimo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-287">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="dbce8-288">Il valore intero di `LogLevel.None` è 6, maggiore di `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="dbce8-288">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="dbce8-289">Creare regole di filtro nella configurazione</span><span class="sxs-lookup"><span data-stu-id="dbce8-289">Create filter rules in configuration</span></span>

<span data-ttu-id="dbce8-290">Il codice del `CreateDefaultBuilder` modello di progetto chiama per impostare la registrazione per i provider Console, Debug e EventSource (ASP.NET Core 2.2 o versioni successive).</span><span class="sxs-lookup"><span data-stu-id="dbce8-290">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="dbce8-291">Il metodo `CreateDefaultBuilder` imposta inoltre la registrazione per la ricerca della configurazione in una sezione `Logging`, come illustrato [in precedenza in questo articolo](#configuration).</span><span class="sxs-lookup"><span data-stu-id="dbce8-291">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="dbce8-292">I dati di configurazione specificano i livelli di registrazione minimi in base al provider e alla categoria, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="dbce8-292">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="dbce8-293">Questo codice JSON crea sei regole di filtro, una per il provider Debug, quattro per il provider Console e una per tutti i provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-293">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="dbce8-294">Quando viene creato un oggetto `ILogger`, viene scelta una singola regola per ogni provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-294">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="dbce8-295">Regole di filtro nel codice</span><span class="sxs-lookup"><span data-stu-id="dbce8-295">Filter rules in code</span></span>

<span data-ttu-id="dbce8-296">L'esempio seguente illustra come registrare le regole di filtro nel codice:</span><span class="sxs-lookup"><span data-stu-id="dbce8-296">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="dbce8-297">Il secondo `AddFilter` specifica il provider Debug tramite il nome del tipo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-297">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="dbce8-298">Il primo `AddFilter` si applica a tutti i provider in quanto non consente di specificare un tipo di provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-298">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="dbce8-299">Applicazione delle regole di filtro</span><span class="sxs-lookup"><span data-stu-id="dbce8-299">How filtering rules are applied</span></span>

<span data-ttu-id="dbce8-300">I dati di configurazione e il codice `AddFilter` illustrato negli esempi precedenti creano le regole indicate nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="dbce8-300">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="dbce8-301">I primi sei provengono dall'esempio di configurazione e gli ultimi due dall'esempio di codice.</span><span class="sxs-lookup"><span data-stu-id="dbce8-301">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="dbce8-302">Number</span><span class="sxs-lookup"><span data-stu-id="dbce8-302">Number</span></span> | <span data-ttu-id="dbce8-303">Provider</span><span class="sxs-lookup"><span data-stu-id="dbce8-303">Provider</span></span>      | <span data-ttu-id="dbce8-304">Categorie che iniziano con...</span><span class="sxs-lookup"><span data-stu-id="dbce8-304">Categories that begin with ...</span></span>          | <span data-ttu-id="dbce8-305">Livello di registrazione minimo</span><span class="sxs-lookup"><span data-stu-id="dbce8-305">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="dbce8-306">1</span><span class="sxs-lookup"><span data-stu-id="dbce8-306">1</span></span>      | <span data-ttu-id="dbce8-307">Debug</span><span class="sxs-lookup"><span data-stu-id="dbce8-307">Debug</span></span>         | <span data-ttu-id="dbce8-308">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="dbce8-308">All categories</span></span>                          | <span data-ttu-id="dbce8-309">Informazioni</span><span class="sxs-lookup"><span data-stu-id="dbce8-309">Information</span></span>       |
| <span data-ttu-id="dbce8-310">2</span><span class="sxs-lookup"><span data-stu-id="dbce8-310">2</span></span>      | <span data-ttu-id="dbce8-311">Console</span><span class="sxs-lookup"><span data-stu-id="dbce8-311">Console</span></span>       | <span data-ttu-id="dbce8-312">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="dbce8-312">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="dbce8-313">Avviso</span><span class="sxs-lookup"><span data-stu-id="dbce8-313">Warning</span></span>           |
| <span data-ttu-id="dbce8-314">3</span><span class="sxs-lookup"><span data-stu-id="dbce8-314">3</span></span>      | <span data-ttu-id="dbce8-315">Console</span><span class="sxs-lookup"><span data-stu-id="dbce8-315">Console</span></span>       | <span data-ttu-id="dbce8-316">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="dbce8-316">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="dbce8-317">Debug</span><span class="sxs-lookup"><span data-stu-id="dbce8-317">Debug</span></span>             |
| <span data-ttu-id="dbce8-318">4</span><span class="sxs-lookup"><span data-stu-id="dbce8-318">4</span></span>      | <span data-ttu-id="dbce8-319">Console</span><span class="sxs-lookup"><span data-stu-id="dbce8-319">Console</span></span>       | <span data-ttu-id="dbce8-320">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="dbce8-320">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="dbce8-321">Errore</span><span class="sxs-lookup"><span data-stu-id="dbce8-321">Error</span></span>             |
| <span data-ttu-id="dbce8-322">5</span><span class="sxs-lookup"><span data-stu-id="dbce8-322">5</span></span>      | <span data-ttu-id="dbce8-323">Console</span><span class="sxs-lookup"><span data-stu-id="dbce8-323">Console</span></span>       | <span data-ttu-id="dbce8-324">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="dbce8-324">All categories</span></span>                          | <span data-ttu-id="dbce8-325">Informazioni</span><span class="sxs-lookup"><span data-stu-id="dbce8-325">Information</span></span>       |
| <span data-ttu-id="dbce8-326">6</span><span class="sxs-lookup"><span data-stu-id="dbce8-326">6</span></span>      | <span data-ttu-id="dbce8-327">Tutti i provider</span><span class="sxs-lookup"><span data-stu-id="dbce8-327">All providers</span></span> | <span data-ttu-id="dbce8-328">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="dbce8-328">All categories</span></span>                          | <span data-ttu-id="dbce8-329">Debug</span><span class="sxs-lookup"><span data-stu-id="dbce8-329">Debug</span></span>             |
| <span data-ttu-id="dbce8-330">7</span><span class="sxs-lookup"><span data-stu-id="dbce8-330">7</span></span>      | <span data-ttu-id="dbce8-331">Tutti i provider</span><span class="sxs-lookup"><span data-stu-id="dbce8-331">All providers</span></span> | <span data-ttu-id="dbce8-332">Sistema</span><span class="sxs-lookup"><span data-stu-id="dbce8-332">System</span></span>                                  | <span data-ttu-id="dbce8-333">Debug</span><span class="sxs-lookup"><span data-stu-id="dbce8-333">Debug</span></span>             |
| <span data-ttu-id="dbce8-334">8</span><span class="sxs-lookup"><span data-stu-id="dbce8-334">8</span></span>      | <span data-ttu-id="dbce8-335">Debug</span><span class="sxs-lookup"><span data-stu-id="dbce8-335">Debug</span></span>         | <span data-ttu-id="dbce8-336">Microsoft</span><span class="sxs-lookup"><span data-stu-id="dbce8-336">Microsoft</span></span>                               | <span data-ttu-id="dbce8-337">Trace</span><span class="sxs-lookup"><span data-stu-id="dbce8-337">Trace</span></span>             |

<span data-ttu-id="dbce8-338">Quando viene creato un oggetto `ILogger`, l'oggetto `ILoggerFactory` seleziona una singola regola per ogni provider da applicare al logger.</span><span class="sxs-lookup"><span data-stu-id="dbce8-338">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="dbce8-339">Tutti i messaggi scritti da un'istanza di `ILogger` vengono filtrati in base alle regole selezionate.</span><span class="sxs-lookup"><span data-stu-id="dbce8-339">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="dbce8-340">Tra le regole disponibili viene selezionata la regola più specifica possibile per ogni coppia di categoria e provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-340">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="dbce8-341">L'algoritmo seguente viene usato per ogni provider quando viene creato un `ILogger` per una determinata categoria:</span><span class="sxs-lookup"><span data-stu-id="dbce8-341">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="dbce8-342">Selezionare tutte le regole corrispondenti al provider o al relativo alias.</span><span class="sxs-lookup"><span data-stu-id="dbce8-342">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="dbce8-343">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole con un provider vuoto.</span><span class="sxs-lookup"><span data-stu-id="dbce8-343">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="dbce8-344">Dal risultato del passaggio precedente, selezionare le regole con il prefisso di categoria corrispondente più lungo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-344">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="dbce8-345">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole che non specificano una categoria.</span><span class="sxs-lookup"><span data-stu-id="dbce8-345">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="dbce8-346">Se sono selezionate più regole, scegliere l'**ultima**.</span><span class="sxs-lookup"><span data-stu-id="dbce8-346">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="dbce8-347">Se non è selezionata alcuna regola, usare `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-347">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="dbce8-348">Con l'elenco di regole precedente, si supponga di creare un oggetto `ILogger` per la categoria "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="dbce8-348">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="dbce8-349">Per il provider Debug si applicano le regole 1, 6 e 8.</span><span class="sxs-lookup"><span data-stu-id="dbce8-349">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="dbce8-350">La regola 8 è più specifica, così sarà quella selezionata.</span><span class="sxs-lookup"><span data-stu-id="dbce8-350">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="dbce8-351">Per il provider Console si applicano le regole 3, 4, 5 e 6.</span><span class="sxs-lookup"><span data-stu-id="dbce8-351">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="dbce8-352">La regola 3 è la più specifica.</span><span class="sxs-lookup"><span data-stu-id="dbce8-352">Rule 3 is most specific.</span></span>

<span data-ttu-id="dbce8-353">L'istanza di `ILogger` risultante invia i log di livello `Trace` o superiore al provider Debug.</span><span class="sxs-lookup"><span data-stu-id="dbce8-353">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="dbce8-354">I log di livello `Debug` o superiore vengono inviati al provider Console.</span><span class="sxs-lookup"><span data-stu-id="dbce8-354">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="dbce8-355">Alias dei provider</span><span class="sxs-lookup"><span data-stu-id="dbce8-355">Provider aliases</span></span>

<span data-ttu-id="dbce8-356">Ogni provider definisce un *alias* che può essere utilizzato nella configurazione al posto del nome completo di tipo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-356">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="dbce8-357">Per i provider predefiniti, usare gli alias seguenti:</span><span class="sxs-lookup"><span data-stu-id="dbce8-357">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="dbce8-358">Console</span><span class="sxs-lookup"><span data-stu-id="dbce8-358">Console</span></span>
* <span data-ttu-id="dbce8-359">Debug</span><span class="sxs-lookup"><span data-stu-id="dbce8-359">Debug</span></span>
* <span data-ttu-id="dbce8-360">EventSource</span><span class="sxs-lookup"><span data-stu-id="dbce8-360">EventSource</span></span>
* <span data-ttu-id="dbce8-361">EventLog</span><span class="sxs-lookup"><span data-stu-id="dbce8-361">EventLog</span></span>
* <span data-ttu-id="dbce8-362">TraceSource</span><span class="sxs-lookup"><span data-stu-id="dbce8-362">TraceSource</span></span>
* <span data-ttu-id="dbce8-363">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="dbce8-363">AzureAppServicesFile</span></span>
* <span data-ttu-id="dbce8-364">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="dbce8-364">AzureAppServicesBlob</span></span>
* <span data-ttu-id="dbce8-365">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="dbce8-365">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="dbce8-366">Livello minimo predefinito</span><span class="sxs-lookup"><span data-stu-id="dbce8-366">Default minimum level</span></span>

<span data-ttu-id="dbce8-367">Esiste un'impostazione del livello minimo che diventa effettiva solo se non si applica alcuna regola di configurazione o codice per un provider e una categoria specifici.</span><span class="sxs-lookup"><span data-stu-id="dbce8-367">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="dbce8-368">L'esempio seguente illustra come impostare il livello minimo:</span><span class="sxs-lookup"><span data-stu-id="dbce8-368">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="dbce8-369">Se non si imposta in modo esplicito il livello minimo, il valore predefinito è `Information`, che indica che i log `Trace` e `Debug` vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="dbce8-369">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="dbce8-370">Funzioni di filtro</span><span class="sxs-lookup"><span data-stu-id="dbce8-370">Filter functions</span></span>

<span data-ttu-id="dbce8-371">Una funzione di filtro viene richiamata per tutti i provider e le categorie a cui non sono assegnate regole tramite la configurazione o il codice.</span><span class="sxs-lookup"><span data-stu-id="dbce8-371">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="dbce8-372">Il codice della funzione ha accesso al tipo di provider, alla categoria e al livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-372">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="dbce8-373">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="dbce8-373">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="dbce8-374">Livelli e categorie di sistema</span><span class="sxs-lookup"><span data-stu-id="dbce8-374">System categories and levels</span></span>

<span data-ttu-id="dbce8-375">Di seguito sono elencate alcune categorie usate da ASP.NET Core ed Entity Framework Core, con note sui log previsti per ognuna:</span><span class="sxs-lookup"><span data-stu-id="dbce8-375">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="dbce8-376">Category</span><span class="sxs-lookup"><span data-stu-id="dbce8-376">Category</span></span>                            | <span data-ttu-id="dbce8-377">Note</span><span class="sxs-lookup"><span data-stu-id="dbce8-377">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="dbce8-378">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="dbce8-378">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="dbce8-379">Diagnostica generale di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbce8-379">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="dbce8-380">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="dbce8-380">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="dbce8-381">Chiavi considerate, trovate e usate.</span><span class="sxs-lookup"><span data-stu-id="dbce8-381">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="dbce8-382">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="dbce8-382">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="dbce8-383">Host consentiti.</span><span class="sxs-lookup"><span data-stu-id="dbce8-383">Hosts allowed.</span></span> |
| <span data-ttu-id="dbce8-384">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="dbce8-384">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="dbce8-385">Tempo impiegato per il completamento delle richieste HTTP e ora di inizio delle richieste.</span><span class="sxs-lookup"><span data-stu-id="dbce8-385">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="dbce8-386">Assembly di avvio dell'hosting caricati.</span><span class="sxs-lookup"><span data-stu-id="dbce8-386">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="dbce8-387">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="dbce8-387">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="dbce8-388">Diagnostica di MVC e Razor.</span><span class="sxs-lookup"><span data-stu-id="dbce8-388">MVC and Razor diagnostics.</span></span> <span data-ttu-id="dbce8-389">Associazione di modelli, esecuzione di filtri, compilazione di viste, selezione di azioni.</span><span class="sxs-lookup"><span data-stu-id="dbce8-389">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="dbce8-390">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="dbce8-390">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="dbce8-391">Informazioni sulla corrispondenza di route.</span><span class="sxs-lookup"><span data-stu-id="dbce8-391">Route matching information.</span></span> |
| <span data-ttu-id="dbce8-392">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="dbce8-392">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="dbce8-393">Risposte di avvio, arresto e keep-alive per la connessione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-393">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="dbce8-394">Informazioni sui certificati HTTPS.</span><span class="sxs-lookup"><span data-stu-id="dbce8-394">HTTPS certificate information.</span></span> |
| <span data-ttu-id="dbce8-395">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="dbce8-395">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="dbce8-396">File forniti.</span><span class="sxs-lookup"><span data-stu-id="dbce8-396">Files served.</span></span> |
| <span data-ttu-id="dbce8-397">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="dbce8-397">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="dbce8-398">Diagnostica generale di Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="dbce8-398">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="dbce8-399">Attività e configurazione del database, rilevamento delle modifiche, migrazioni.</span><span class="sxs-lookup"><span data-stu-id="dbce8-399">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="dbce8-400">Ambiti dei log</span><span class="sxs-lookup"><span data-stu-id="dbce8-400">Log scopes</span></span>

 <span data-ttu-id="dbce8-401">Un *ambito* può raggruppare un set di operazioni logiche.</span><span class="sxs-lookup"><span data-stu-id="dbce8-401">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="dbce8-402">Questo raggruppamento può essere usato per collegare gli stessi dati a ogni log creato come parte di un set.</span><span class="sxs-lookup"><span data-stu-id="dbce8-402">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="dbce8-403">Ad esempio, ogni log creato come parte dell'elaborazione di una transazione può includere l'ID transazione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-403">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="dbce8-404">Un ambito è un tipo `IDisposable` restituito dal metodo <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> e viene mantenuto fino all'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-404">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="dbce8-405">Un ambito si usa mediante il wrapping delle chiamate del logger in un blocco `using`:</span><span class="sxs-lookup"><span data-stu-id="dbce8-405">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="dbce8-406">Il codice seguente abilita gli ambiti per il provider Console:</span><span class="sxs-lookup"><span data-stu-id="dbce8-406">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="dbce8-407">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="dbce8-407">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="dbce8-408">La configurazione dell'opzione del logger della console `IncludeScopes` è necessaria per abilitare la registrazione basata sull'ambito.</span><span class="sxs-lookup"><span data-stu-id="dbce8-408">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="dbce8-409">Per informazioni sulla configurazione, vedere la sezione [Configurazione](#configuration).</span><span class="sxs-lookup"><span data-stu-id="dbce8-409">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="dbce8-410">Ogni messaggio di registrazione include le informazioni sull'ambito:</span><span class="sxs-lookup"><span data-stu-id="dbce8-410">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="dbce8-411">Provider di registrazione predefiniti</span><span class="sxs-lookup"><span data-stu-id="dbce8-411">Built-in logging providers</span></span>

<span data-ttu-id="dbce8-412">ASP.NET Core include i provider seguenti:</span><span class="sxs-lookup"><span data-stu-id="dbce8-412">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="dbce8-413">Console</span><span class="sxs-lookup"><span data-stu-id="dbce8-413">Console</span></span>](#console-provider)
* [<span data-ttu-id="dbce8-414">Debug</span><span class="sxs-lookup"><span data-stu-id="dbce8-414">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="dbce8-415">Eventsource</span><span class="sxs-lookup"><span data-stu-id="dbce8-415">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="dbce8-416">EventLog</span><span class="sxs-lookup"><span data-stu-id="dbce8-416">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="dbce8-417">TraceSource</span><span class="sxs-lookup"><span data-stu-id="dbce8-417">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="dbce8-418">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="dbce8-418">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="dbce8-419">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="dbce8-419">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="dbce8-420">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="dbce8-420">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="dbce8-421">Per informazioni su StdOut e la registrazione debug con il modulo ASP.NET Core, vedere <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="dbce8-421">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="dbce8-422">Provider Console</span><span class="sxs-lookup"><span data-stu-id="dbce8-422">Console provider</span></span>

<span data-ttu-id="dbce8-423">Il pacchetto del provider [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) invia l'output della registrazione alla console.</span><span class="sxs-lookup"><span data-stu-id="dbce8-423">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="dbce8-424">Per visualizzare l'output di registrazione del provider Console, aprire un prompt dei comandi nella cartella del progetto ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="dbce8-424">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="dbce8-425">Provider Debug</span><span class="sxs-lookup"><span data-stu-id="dbce8-425">Debug provider</span></span>

<span data-ttu-id="dbce8-426">Il pacchetto del provider [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) scrive l'output della registrazione usando la classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (chiamate del metodo `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="dbce8-426">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="dbce8-427">In Linux, questo provider scrive i log in */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="dbce8-427">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="dbce8-428">Provider dell'origine eventi</span><span class="sxs-lookup"><span data-stu-id="dbce8-428">Event Source provider</span></span>

<span data-ttu-id="dbce8-429">Il pacchetto del provider [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) viene scritto in `Microsoft-Extensions-Logging`una piattaforma multipiattaforma Origine evento con il nome .</span><span class="sxs-lookup"><span data-stu-id="dbce8-429">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="dbce8-430">In Windows, il provider utilizza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="dbce8-430">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="dbce8-431">Il provider di origine `CreateDefaultBuilder` evento viene aggiunto automaticamente quando viene chiamato per compilare l'host.</span><span class="sxs-lookup"><span data-stu-id="dbce8-431">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="dbce8-432">strumenti di traccia dotnet</span><span class="sxs-lookup"><span data-stu-id="dbce8-432">dotnet trace tooling</span></span>

<span data-ttu-id="dbce8-433">Lo strumento [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) è uno strumento globale CLI multipiattaforma che consente la raccolta di tracce .NET Core di un processo in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-433">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="dbce8-434">Lo strumento <xref:Microsoft.Extensions.Logging.EventSource> raccoglie i <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>dati del provider utilizzando un file .</span><span class="sxs-lookup"><span data-stu-id="dbce8-434">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="dbce8-435">Installare gli strumenti di traccia dotnet con il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="dbce8-435">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="dbce8-436">Usare gli strumenti di traccia dotnet per raccogliere una traccia da un'app:Use the dotnet trace tooling to collect a trace from an app:</span><span class="sxs-lookup"><span data-stu-id="dbce8-436">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="dbce8-437">Se l'app non compila `CreateDefaultBuilder`l'host con , aggiungi il [provider dell'origine eventi](#event-source-provider) alla configurazione di registrazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-437">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="dbce8-438">Eseguire l'app `dotnet run` con il comando.</span><span class="sxs-lookup"><span data-stu-id="dbce8-438">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="dbce8-439">Determinare l'identificatore di processo (PID) dell'app .NET Core:</span><span class="sxs-lookup"><span data-stu-id="dbce8-439">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="dbce8-440">In Windows, utilizzare uno dei seguenti approcci:</span><span class="sxs-lookup"><span data-stu-id="dbce8-440">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="dbce8-441">Gestione attività (CTRL-ALT-CANC)</span><span class="sxs-lookup"><span data-stu-id="dbce8-441">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="dbce8-442">comando tasklist</span><span class="sxs-lookup"><span data-stu-id="dbce8-442">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="dbce8-443">Comando di PowerShell Get-Process</span><span class="sxs-lookup"><span data-stu-id="dbce8-443">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="dbce8-444">Su Linux, utilizzare il [comando pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="dbce8-444">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="dbce8-445">Trova il PID per il processo che ha lo stesso nome dell'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-445">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="dbce8-446">Eseguire `dotnet trace` il comando.</span><span class="sxs-lookup"><span data-stu-id="dbce8-446">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="dbce8-447">Sintassi generale dei comandi:</span><span class="sxs-lookup"><span data-stu-id="dbce8-447">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="dbce8-448">Quando si usa una shell `--providers` dei comandi di`'`PowerShell, racchiudere il valore tra virgolette singole ( ):</span><span class="sxs-lookup"><span data-stu-id="dbce8-448">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="dbce8-449">Su piattaforme non Windows, aggiungere l'opzione `-f speedscope` per modificare `speedscope`il formato del file di traccia di output in .</span><span class="sxs-lookup"><span data-stu-id="dbce8-449">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="dbce8-450">Parola chiave</span><span class="sxs-lookup"><span data-stu-id="dbce8-450">Keyword</span></span> | <span data-ttu-id="dbce8-451">Descrizione</span><span class="sxs-lookup"><span data-stu-id="dbce8-451">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="dbce8-452">1</span><span class="sxs-lookup"><span data-stu-id="dbce8-452">1</span></span>       | <span data-ttu-id="dbce8-453">Registrare meta `LoggingEventSource`eventi relativi al file .</span><span class="sxs-lookup"><span data-stu-id="dbce8-453">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="dbce8-454">Non registra gli `ILogger`eventi da ).</span><span class="sxs-lookup"><span data-stu-id="dbce8-454">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="dbce8-455">2</span><span class="sxs-lookup"><span data-stu-id="dbce8-455">2</span></span>       | <span data-ttu-id="dbce8-456">Attiva `Message` l'evento `ILogger.Log()` quando viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="dbce8-456">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="dbce8-457">Fornisce informazioni in modo programmatico (non formattato).</span><span class="sxs-lookup"><span data-stu-id="dbce8-457">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="dbce8-458">4</span><span class="sxs-lookup"><span data-stu-id="dbce8-458">4</span></span>       | <span data-ttu-id="dbce8-459">Attiva `FormatMessage` l'evento `ILogger.Log()` quando viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="dbce8-459">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="dbce8-460">Fornisce la versione in formato stringa delle informazioni.</span><span class="sxs-lookup"><span data-stu-id="dbce8-460">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="dbce8-461">8</span><span class="sxs-lookup"><span data-stu-id="dbce8-461">8</span></span>       | <span data-ttu-id="dbce8-462">Attiva `MessageJson` l'evento `ILogger.Log()` quando viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="dbce8-462">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="dbce8-463">Fornisce una rappresentazione JSON degli argomenti.</span><span class="sxs-lookup"><span data-stu-id="dbce8-463">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="dbce8-464">Livello dell'evento</span><span class="sxs-lookup"><span data-stu-id="dbce8-464">Event Level</span></span> | <span data-ttu-id="dbce8-465">Descrizione</span><span class="sxs-lookup"><span data-stu-id="dbce8-465">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="dbce8-466">0</span><span class="sxs-lookup"><span data-stu-id="dbce8-466">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="dbce8-467">1</span><span class="sxs-lookup"><span data-stu-id="dbce8-467">1</span></span>           | `Critical`      |
   | <span data-ttu-id="dbce8-468">2</span><span class="sxs-lookup"><span data-stu-id="dbce8-468">2</span></span>           | `Error`         |
   | <span data-ttu-id="dbce8-469">3</span><span class="sxs-lookup"><span data-stu-id="dbce8-469">3</span></span>           | `Warning`       |
   | <span data-ttu-id="dbce8-470">4</span><span class="sxs-lookup"><span data-stu-id="dbce8-470">4</span></span>           | `Informational` |
   | <span data-ttu-id="dbce8-471">5</span><span class="sxs-lookup"><span data-stu-id="dbce8-471">5</span></span>           | `Verbose`       |

   <span data-ttu-id="dbce8-472">`FilterSpecs`voci per `{Logger Category}` `{Event Level}` e rappresentano condizioni di filtro del registro aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="dbce8-472">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="dbce8-473">Separare `FilterSpecs` le voci con`;`un punto e virgola ( ).</span><span class="sxs-lookup"><span data-stu-id="dbce8-473">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="dbce8-474">Esempio di utilizzo di una shell dei `--providers` comandi di Windows **(nessuna** virgoletta singola intorno al valore):</span><span class="sxs-lookup"><span data-stu-id="dbce8-474">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="dbce8-475">Il comando precedente attiva:</span><span class="sxs-lookup"><span data-stu-id="dbce8-475">The preceding command activates:</span></span>

   * <span data-ttu-id="dbce8-476">Logger origine evento per produrre`4`stringhe formattate`2`( ) per gli errori ( ).</span><span class="sxs-lookup"><span data-stu-id="dbce8-476">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="dbce8-477">`Microsoft.AspNetCore.Hosting`registrazione a `Informational` livello`4`di registrazione ( ).</span><span class="sxs-lookup"><span data-stu-id="dbce8-477">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="dbce8-478">Arrestare gli strumenti di traccia dotnet premendo il tasto Invio o CTRL .</span><span class="sxs-lookup"><span data-stu-id="dbce8-478">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="dbce8-479">La traccia viene salvata con il nome *trace.nettrace* nella cartella in cui viene eseguito il `dotnet trace` comando.</span><span class="sxs-lookup"><span data-stu-id="dbce8-479">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="dbce8-480">Aprire la traccia con [Perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="dbce8-480">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="dbce8-481">Aprire il file *trace.nettrace* ed esplorare gli eventi di traccia.</span><span class="sxs-lookup"><span data-stu-id="dbce8-481">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="dbce8-482">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="dbce8-482">For more information, see:</span></span>

* <span data-ttu-id="dbce8-483">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (documentazione di .NET Core)</span><span class="sxs-lookup"><span data-stu-id="dbce8-483">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="dbce8-484">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (documentazione del repository GitHub di dotnet/diagnostica)Trace for performance analysis utility (dotnet-trace) (dotnet/diagnostics GitHub repository documentation)</span><span class="sxs-lookup"><span data-stu-id="dbce8-484">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="dbce8-485">[Classe LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (browser API.NET)</span><span class="sxs-lookup"><span data-stu-id="dbce8-485">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="dbce8-486">Origine di [riferimento LoggingEventSource (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Per ottenere l'origine `release/{Version}`del `{Version}` riferimento per una versione diversa, modificare il ramo in , dove è la versione di ASP.NET Core desiderato.</span><span class="sxs-lookup"><span data-stu-id="dbce8-486">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="dbce8-487">[Perfview](#perfview) &ndash; Utile per la visualizzazione delle tracce dell'origine evento.</span><span class="sxs-lookup"><span data-stu-id="dbce8-487">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="dbce8-488">Perfview</span><span class="sxs-lookup"><span data-stu-id="dbce8-488">Perfview</span></span>

<span data-ttu-id="dbce8-489">Utilizzare [l'utilità PerfView](https://github.com/Microsoft/perfview) per raccogliere e visualizzare i log.</span><span class="sxs-lookup"><span data-stu-id="dbce8-489">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="dbce8-490">Sono disponibili altri strumenti per la visualizzazione dei log ETW, ma PerfView fornisce un'esperienza ottimale per l'uso con gli eventi ETW generati da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbce8-490">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="dbce8-491">Per configurare PerfView per la raccolta degli eventi registrati da questo provider, aggiungere la stringa `*Microsoft-Extensions-Logging` nell'elenco **Provider aggiuntivi**</span><span class="sxs-lookup"><span data-stu-id="dbce8-491">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="dbce8-492">(non dimenticare l'asterisco all'inizio della stringa).</span><span class="sxs-lookup"><span data-stu-id="dbce8-492">(Don't miss the asterisk at the start of the string.)</span></span>

![Provider aggiuntivi di PerfView](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="dbce8-494">Provider EventLog di Windows</span><span class="sxs-lookup"><span data-stu-id="dbce8-494">Windows EventLog provider</span></span>

<span data-ttu-id="dbce8-495">Il pacchetto di provider [Microsoft.Extensions.Logging.AventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) invia l'output del log al registro eventi di Windows.</span><span class="sxs-lookup"><span data-stu-id="dbce8-495">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="dbce8-496">Gli [overload di AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) consentono di passare <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="dbce8-496">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="dbce8-497">Se `null` specificato o non specificato, vengono utilizzate le seguenti impostazioni predefinite:</span><span class="sxs-lookup"><span data-stu-id="dbce8-497">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="dbce8-498">`LogName`&ndash; "Applicazione"</span><span class="sxs-lookup"><span data-stu-id="dbce8-498">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="dbce8-499">`SourceName`&ndash; "Runtime .NET"</span><span class="sxs-lookup"><span data-stu-id="dbce8-499">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="dbce8-500">`MachineName` &ndash; computer locale</span><span class="sxs-lookup"><span data-stu-id="dbce8-500">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="dbce8-501">Gli eventi vengono registrati per [il livello di avviso e superiore](#log-level).</span><span class="sxs-lookup"><span data-stu-id="dbce8-501">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="dbce8-502">Per registrare `Warning`eventi inferiori a , impostare in modo esplicito il livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-502">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="dbce8-503">Ad esempio, aggiungere quanto segue al file *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="dbce8-503">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="dbce8-504">Provider TraceSource</span><span class="sxs-lookup"><span data-stu-id="dbce8-504">TraceSource provider</span></span>

<span data-ttu-id="dbce8-505">Il pacchetto di provider [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa le librerie e i provider <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="dbce8-505">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="dbce8-506">Gli [overload di AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) consentono di passare un cambio di origine e un listener di traccia.</span><span class="sxs-lookup"><span data-stu-id="dbce8-506">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="dbce8-507">Per usare questo provider, un'app deve essere in esecuzione in .NET Framework (invece che in .NET Core).</span><span class="sxs-lookup"><span data-stu-id="dbce8-507">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="dbce8-508">Il provider può instradare i messaggi a diversi [listener](/dotnet/framework/debug-trace-profile/trace-listeners), come <xref:System.Diagnostics.TextWriterTraceListener>, usato nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="dbce8-508">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="dbce8-509">Provider del Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="dbce8-509">Azure App Service provider</span></span>

<span data-ttu-id="dbce8-510">Il pacchetto di provider [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) scrive i log in file di testo nel file system di un'app del Servizio app di Azure e nell'[archivio di BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in un account di archiviazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="dbce8-510">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="dbce8-511">Il pacchetto del provider non è incluso nel framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="dbce8-511">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="dbce8-512">Per usare il provider, aggiungere il relativo pacchetto al progetto.</span><span class="sxs-lookup"><span data-stu-id="dbce8-512">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="dbce8-513">Per configurare le impostazioni del provider, usare <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> e <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="dbce8-513">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="dbce8-514">Quando si distribuisce un'app del servizio app, l'applicazione applica le impostazioni della sezione [Log del servizio app](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) della pagina **Servizio app** del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="dbce8-514">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="dbce8-515">Quando le impostazioni seguenti vengono aggiornate, le modifiche hanno effetto immediatamente senza richiedere un riavvio o la ridistribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-515">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="dbce8-516">**Registrazione applicazioni (file system)**</span><span class="sxs-lookup"><span data-stu-id="dbce8-516">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="dbce8-517">**Registrazione applicazione (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="dbce8-517">**Application Logging (Blob)**</span></span>

<span data-ttu-id="dbce8-518">Il percorso predefinito per i file di log è nella cartella *D:\\home\\LogFiles\\Application* e il nome di file predefinito è *diagnostics-aaaammgg.txt*.</span><span class="sxs-lookup"><span data-stu-id="dbce8-518">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="dbce8-519">Il limite predefinito per le dimensioni del file è 10 MB e il numero massimo predefinito di file conservati è 2.</span><span class="sxs-lookup"><span data-stu-id="dbce8-519">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="dbce8-520">Il nome BLOB predefinito è *{nome-app}{timestamp}/aaaa/mm/gg/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="dbce8-520">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="dbce8-521">Il provider funziona solo quando il progetto viene eseguito nell'ambiente di Azure.</span><span class="sxs-lookup"><span data-stu-id="dbce8-521">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="dbce8-522">Non ha alcun effetto quando il progetto viene eseguito localmente, in quanto non scrive nulla in file locali o in archivi di sviluppo locali per i BLOB.</span><span class="sxs-lookup"><span data-stu-id="dbce8-522">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="dbce8-523">Flusso di registrazione di Azure</span><span class="sxs-lookup"><span data-stu-id="dbce8-523">Azure log streaming</span></span>

<span data-ttu-id="dbce8-524">Il flusso di registrazione di Azure consente di visualizzare l'attività di registrazione in tempo reale da:</span><span class="sxs-lookup"><span data-stu-id="dbce8-524">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="dbce8-525">Server applicazioni</span><span class="sxs-lookup"><span data-stu-id="dbce8-525">The app server</span></span>
* <span data-ttu-id="dbce8-526">Server Web</span><span class="sxs-lookup"><span data-stu-id="dbce8-526">The web server</span></span>
* <span data-ttu-id="dbce8-527">Traccia delle richieste non riuscite</span><span class="sxs-lookup"><span data-stu-id="dbce8-527">Failed request tracing</span></span>

<span data-ttu-id="dbce8-528">Per configurare il flusso di registrazione di Azure:</span><span class="sxs-lookup"><span data-stu-id="dbce8-528">To configure Azure log streaming:</span></span>

* <span data-ttu-id="dbce8-529">Passare alla pagina **Log del servizio app** dalla pagina del portale dell'app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-529">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="dbce8-530">Impostare **Registrazione applicazioni (file system)** su **Attiva**.</span><span class="sxs-lookup"><span data-stu-id="dbce8-530">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="dbce8-531">Scegliere il livello di registrazione in **Livello**.</span><span class="sxs-lookup"><span data-stu-id="dbce8-531">Choose the log **Level**.</span></span> <span data-ttu-id="dbce8-532">Questa impostazione si applica solo allo streaming dei log di Azure, non agli altri provider di registrazione nell'app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-532">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="dbce8-533">Passare alla pagina **Flusso di registrazione** per visualizzare i messaggi dell'app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-533">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="dbce8-534">I messaggi vengono registrati dall'app tramite l'interfaccia `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-534">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="dbce8-535">Registrazione di traccia di Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="dbce8-535">Azure Application Insights trace logging</span></span>

<span data-ttu-id="dbce8-536">Il pacchetto di provider [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) scrive log in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="dbce8-536">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="dbce8-537">Application Insights è un servizio che monitora un'app Web e fornisce gli strumenti per l'esecuzione di query sui dati di telemetria e la loro analisi.</span><span class="sxs-lookup"><span data-stu-id="dbce8-537">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="dbce8-538">Se si usa questo provider, è possibile eseguire query sui log e analizzarli usando gli strumenti di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="dbce8-538">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="dbce8-539">Il provider di registrazione è incluso come dipendenza di [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), ovvero il pacchetto che fornisce tutti i dati di telemetria disponibili per ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbce8-539">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="dbce8-540">Se si usa questo pacchetto, non è necessario installare il pacchetto di provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-540">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="dbce8-541">Non usare il pacchetto [](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)Microsoft.ApplicationInsights.Web&mdash;, che è per ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="dbce8-541">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="dbce8-542">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="dbce8-542">For more information, see the following resources:</span></span>

* [<span data-ttu-id="dbce8-543">Panoramica di Application Insights</span><span class="sxs-lookup"><span data-stu-id="dbce8-543">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="dbce8-544">[Application Insights per applicazioni ASP.NET Core](/azure/azure-monitor/app/asp-net-core): iniziare da qui se si vuole implementare l'intervallo completo dei dati di telemetria di Application Insights insieme alla registrazione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-544">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="dbce8-545">[ApplicationInsightsLoggerProvider per log ILogger .NET Core](/azure/azure-monitor/app/ilogger): iniziare da qui se si vuole implementare il provider di registrazione senza gli altri dati di telemetria di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="dbce8-545">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="dbce8-546">[Adattatori di registrazione di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="dbce8-546">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="dbce8-547">[Installare, configurare e inizializzare Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): esercitazione interattiva nel sito Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="dbce8-547">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="dbce8-548">Provider di registrazione di terze parti</span><span class="sxs-lookup"><span data-stu-id="dbce8-548">Third-party logging providers</span></span>

<span data-ttu-id="dbce8-549">Framework di registrazione di terze parti che usano ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="dbce8-549">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="dbce8-550">[elmah.io](https://elmah.io/) ([repository GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="dbce8-550">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="dbce8-551">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repository GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="dbce8-551">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="dbce8-552">[JSNLog](https://jsnlog.com/) ([repository GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="dbce8-552">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="dbce8-553">[KissLog.net](https://kisslog.net/) ([repository GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="dbce8-553">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="dbce8-554">[Log4Net](https://logging.apache.org/log4net/) ([repository GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="dbce8-554">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="dbce8-555">[Loggr](https://loggr.net/) ([repository GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="dbce8-555">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="dbce8-556">[NLog](https://nlog-project.org/) ([repository GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="dbce8-556">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="dbce8-557">[Sentry](https://sentry.io/welcome/) ([repository GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="dbce8-557">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="dbce8-558">[Serilog](https://serilog.net/) ([repository GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="dbce8-558">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="dbce8-559">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repository Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="dbce8-559">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="dbce8-560">Alcuni framework di terze parti possono eseguire la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="dbce8-560">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="dbce8-561">L'uso di un framework di terze parti è simile a quello di uno dei provider predefiniti:</span><span class="sxs-lookup"><span data-stu-id="dbce8-561">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="dbce8-562">Aggiungere un pacchetto NuGet al progetto.</span><span class="sxs-lookup"><span data-stu-id="dbce8-562">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="dbce8-563">Chiamare `ILoggerFactory` un metodo di estensione fornito dal framework di registrazione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-563">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="dbce8-564">Per altre informazioni, vedere la documentazione di ogni provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-564">For more information, see each provider's documentation.</span></span> <span data-ttu-id="dbce8-565">I provider di registrazione di terze parti non sono supportati da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="dbce8-565">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dbce8-566">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="dbce8-566">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dbce8-567">Di [Tom Dykstra](https://github.com/tdykstra) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="dbce8-567">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="dbce8-568">.NET Core supporta un'API di registrazione che funziona con un'ampia gamma di provider di registrazione predefiniti e di terze parti.</span><span class="sxs-lookup"><span data-stu-id="dbce8-568">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="dbce8-569">Questo articolo illustra come usare l'API di registrazione con i provider predefiniti.</span><span class="sxs-lookup"><span data-stu-id="dbce8-569">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="dbce8-570">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dbce8-570">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="dbce8-571">Aggiungere provider</span><span class="sxs-lookup"><span data-stu-id="dbce8-571">Add providers</span></span>

<span data-ttu-id="dbce8-572">Un provider di registrazione visualizza o archivia i log.</span><span class="sxs-lookup"><span data-stu-id="dbce8-572">A logging provider displays or stores logs.</span></span> <span data-ttu-id="dbce8-573">Il provider Console, ad esempio, visualizza i log nella console, mentre il provider Azure Application Insights li archivia in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="dbce8-573">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="dbce8-574">I log possono essere inviati a più destinazioni aggiungendo più provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-574">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="dbce8-575">Per aggiungere un provider, chiamare il metodo di estensione `Add{provider name}` del provider in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="dbce8-575">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="dbce8-576">Il codice precedente richiede riferimenti a `Microsoft.Extensions.Logging` e `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-576">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="dbce8-577">Il modello di progetto predefinito chiama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, che aggiunge i provider di registrazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="dbce8-577">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="dbce8-578">Console</span><span class="sxs-lookup"><span data-stu-id="dbce8-578">Console</span></span>
* <span data-ttu-id="dbce8-579">Debug</span><span class="sxs-lookup"><span data-stu-id="dbce8-579">Debug</span></span>
* <span data-ttu-id="dbce8-580">EventSource (a partire da ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="dbce8-580">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="dbce8-581">Se si usa `CreateDefaultBuilder`, è possibile sostituire i provider predefiniti con quelli di propria scelta.</span><span class="sxs-lookup"><span data-stu-id="dbce8-581">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="dbce8-582">Chiamare <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e aggiungere i provider desiderati.</span><span class="sxs-lookup"><span data-stu-id="dbce8-582">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="dbce8-583">Altre informazioni sui [provider di registrazione predefiniti](#built-in-logging-providers) e sui [provider di registrazione di terze parti](#third-party-logging-providers) vengono fornite più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-583">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="dbce8-584">Creare log</span><span class="sxs-lookup"><span data-stu-id="dbce8-584">Create logs</span></span>

<span data-ttu-id="dbce8-585">Per creare i log, usare un oggetto <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="dbce8-585">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="dbce8-586">In un'app Web o in un servizio ospitato, ottenere un oggetto `ILogger` da un inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dbce8-586">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="dbce8-587">Nelle app console non host, usare `LoggerFactory` per creare un oggetto `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-587">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="dbce8-588">L'esempio seguente di ASP.NET Core crea un logger con `TodoApiSample.Pages.AboutModel` come categoria.</span><span class="sxs-lookup"><span data-stu-id="dbce8-588">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="dbce8-589">La *categoria* del log è una stringa associata a ogni log.</span><span class="sxs-lookup"><span data-stu-id="dbce8-589">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="dbce8-590">L'istanza di `ILogger<T>` fornita dall'inserimento delle dipendenze crea log con nome completo di tipo `T` come categoria.</span><span class="sxs-lookup"><span data-stu-id="dbce8-590">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="dbce8-591">Negli esempi seguenti di ASP.NET Core e app console, il logger viene usato per creare log con `Information` come livello.</span><span class="sxs-lookup"><span data-stu-id="dbce8-591">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="dbce8-592">Il *livello* del log indica la gravità dell'evento registrato.</span><span class="sxs-lookup"><span data-stu-id="dbce8-592">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="dbce8-593">[Livelli](#log-level) e [categorie](#log-category) vengono illustrati in modo dettagliato più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-593">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="dbce8-594">Creare log nella classe Startup</span><span class="sxs-lookup"><span data-stu-id="dbce8-594">Create logs in Startup</span></span>

<span data-ttu-id="dbce8-595">Per scrivere log nella classe `Startup`, includere un parametro `ILogger` nella firma del costruttore:</span><span class="sxs-lookup"><span data-stu-id="dbce8-595">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="dbce8-596">Creare log nella classe Program</span><span class="sxs-lookup"><span data-stu-id="dbce8-596">Create logs in the Program class</span></span>

<span data-ttu-id="dbce8-597">Per scrivere log nella classe `Program`, ottenere un'istanza di `ILogger` dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="dbce8-597">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="dbce8-598">La registrazione durante la costruzione dell'host non è supportata direttamente.</span><span class="sxs-lookup"><span data-stu-id="dbce8-598">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="dbce8-599">Tuttavia, è possibile utilizzare un logger separato.</span><span class="sxs-lookup"><span data-stu-id="dbce8-599">However, a separate logger can be used.</span></span> <span data-ttu-id="dbce8-600">Nell'esempio seguente viene utilizzato un logger [Serilog](https://serilog.net/) per l'accesso `CreateWebHostBuilder`a .</span><span class="sxs-lookup"><span data-stu-id="dbce8-600">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="dbce8-601">`AddSerilog`utilizza la configurazione `Log.Logger`statica specificata in :</span><span class="sxs-lookup"><span data-stu-id="dbce8-601">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="dbce8-602">Evitare l'uso di metodi logger asincroni</span><span class="sxs-lookup"><span data-stu-id="dbce8-602">No asynchronous logger methods</span></span>

<span data-ttu-id="dbce8-603">La registrazione deve essere così rapida da non giustificare l'impatto sulle prestazioni del codice asincrono.</span><span class="sxs-lookup"><span data-stu-id="dbce8-603">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="dbce8-604">Se l'archivio dati di registrazione è lento, non scrivere direttamente al suo interno.</span><span class="sxs-lookup"><span data-stu-id="dbce8-604">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="dbce8-605">Scrivere invece i messaggi di log prima in un archivio veloce e quindi spostarli nell'archivio lento in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="dbce8-605">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="dbce8-606">Ad esempio, se la registrazione viene eseguita in SQL Server, è preferibile non farlo direttamente in un metodo `Log`, poiché i metodi `Log` sono sincroni.</span><span class="sxs-lookup"><span data-stu-id="dbce8-606">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="dbce8-607">Al contrario, aggiungere i messaggi di log in modo sincrono a una coda in memoria e usare un ruolo di lavoro in background per eseguire il pull dei messaggi dalla coda per eseguire le operazioni asincrone di push dei dati in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="dbce8-607">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="dbce8-608">Per altre informazioni, vedere questo problema di GitHub.For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span><span class="sxs-lookup"><span data-stu-id="dbce8-608">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="dbce8-609">Configurazione</span><span class="sxs-lookup"><span data-stu-id="dbce8-609">Configuration</span></span>

<span data-ttu-id="dbce8-610">La configurazione dei provider di registrazione viene fornita da uno o più provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="dbce8-610">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="dbce8-611">Formati di file (INI, JSON e XML).</span><span class="sxs-lookup"><span data-stu-id="dbce8-611">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="dbce8-612">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="dbce8-612">Command-line arguments.</span></span>
* <span data-ttu-id="dbce8-613">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="dbce8-613">Environment variables.</span></span>
* <span data-ttu-id="dbce8-614">Oggetti .NET in memoria.</span><span class="sxs-lookup"><span data-stu-id="dbce8-614">In-memory .NET objects.</span></span>
* <span data-ttu-id="dbce8-615">Archiviazione con [Secret Manager](xref:security/app-secrets) senza crittografia.</span><span class="sxs-lookup"><span data-stu-id="dbce8-615">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="dbce8-616">Un archivio utente non crittografato, come [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="dbce8-616">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="dbce8-617">Provider personalizzati (installati o creati).</span><span class="sxs-lookup"><span data-stu-id="dbce8-617">Custom providers (installed or created).</span></span>

<span data-ttu-id="dbce8-618">Ad esempio, la configurazione di registrazione viene comunemente fornita dalla sezione `Logging` del file di impostazioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-618">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="dbce8-619">L'esempio seguente mostra il contenuto di un file *appsettings.Development.json* tipico:</span><span class="sxs-lookup"><span data-stu-id="dbce8-619">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="dbce8-620">La proprietà `Logging` può avere le proprietà `LogLevel` e quella del provider di log (nell'esempio, Console).</span><span class="sxs-lookup"><span data-stu-id="dbce8-620">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="dbce8-621">La proprietà `LogLevel` in `Logging` specifica il [livello](#log-level) minimo per la registrazione per determinate categorie.</span><span class="sxs-lookup"><span data-stu-id="dbce8-621">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="dbce8-622">Nell'esempio, le categorie `System` e `Microsoft` eseguono la registrazione al livello `Information`, mentre tutte le altre la eseguono al livello `Debug`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-622">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="dbce8-623">Altre proprietà in `Logging` specificano i provider di registrazione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-623">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="dbce8-624">L'esempio si riferisce al provider Console.</span><span class="sxs-lookup"><span data-stu-id="dbce8-624">The example is for the Console provider.</span></span> <span data-ttu-id="dbce8-625">Se un provider supporta gli `IncludeScopes` ambiti di [log](#log-scopes), indica se sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="dbce8-625">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="dbce8-626">Una proprietà del provider (ad esempio `Console` nell'esempio) può specificare anche una proprietà `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-626">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="dbce8-627">`LogLevel` in un provider specifica i livelli di registrazione per tale provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-627">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="dbce8-628">Se i livelli sono specificati in `Logging.{providername}.LogLevel`, eseguono l'override di eventuali valori impostati in `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-628">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="dbce8-629">L'API di registrazione non include uno scenario per modificare i livelli di log mentre un'app è in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-629">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="dbce8-630">Tuttavia, alcuni provider di configurazione sono in grado di ricaricare la configurazione, che ha effetto immediato sulla configurazione della registrazione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-630">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="dbce8-631">Ad esempio, il provider di configurazione `CreateDefaultBuilder` [file](xref:fundamentals/configuration/index#file-configuration-provider), che viene aggiunto da per leggere i file di impostazioni, ricarica la configurazione di registrazione per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="dbce8-631">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="dbce8-632">Se la configurazione viene modificata nel codice mentre un'app è in esecuzione, l'app può chiamare [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) per aggiornare la configurazione di registrazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-632">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="dbce8-633">Per informazioni sull'implementazione dei provider di configurazione, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="dbce8-633">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="dbce8-634">Esempio di output di registrazione</span><span class="sxs-lookup"><span data-stu-id="dbce8-634">Sample logging output</span></span>

<span data-ttu-id="dbce8-635">Con il codice di esempio illustrato nella sezione precedente i log vengono visualizzati nella console quando l'app viene eseguita dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="dbce8-635">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="dbce8-636">Ecco un esempio di output della console:</span><span class="sxs-lookup"><span data-stu-id="dbce8-636">Here's an example of console output:</span></span>

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

<span data-ttu-id="dbce8-637">I log precedenti sono stati generati inviando una richiesta HTTP Get all'app di esempio all'indirizzo `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-637">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="dbce8-638">Ecco un esempio degli stessi log visualizzati nella finestra Debug quando si esegue l'app di esempio in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="dbce8-638">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="dbce8-639">I log creati dalle chiamate a `ILogger` illustrate nella sezione precedente iniziano con "TodoApi".</span><span class="sxs-lookup"><span data-stu-id="dbce8-639">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="dbce8-640">I log che iniziano con le categorie "Microsoft" provengono dal codice del framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbce8-640">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="dbce8-641">ASP.NET Core e il codice dell'applicazione usano la stessa API di registrazione e gli stessi provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-641">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="dbce8-642">Il resto di questo articolo spiega alcuni dettagli e le opzioni per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-642">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="dbce8-643">Pacchetti NuGet</span><span class="sxs-lookup"><span data-stu-id="dbce8-643">NuGet packages</span></span>

<span data-ttu-id="dbce8-644">Le interfacce `ILogger` e `ILoggerFactory` si trovano in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) e le loro implementazioni predefinite si trovano in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="dbce8-644">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="dbce8-645">Categoria di log</span><span class="sxs-lookup"><span data-stu-id="dbce8-645">Log category</span></span>

<span data-ttu-id="dbce8-646">Quando viene creato un oggetto `ILogger`, viene specificata la relativa *categoria*.</span><span class="sxs-lookup"><span data-stu-id="dbce8-646">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="dbce8-647">La categoria è inclusa in ogni messaggio di log creato da tale istanza di `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-647">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="dbce8-648">La categoria può essere qualsiasi stringa, ma per convenzione si usa il nome della classe, ad esempio "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="dbce8-648">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="dbce8-649">Usare `ILogger<T>` per ottenere un'istanza di `ILogger` che usa il nome completo di tipo `T` come categoria:</span><span class="sxs-lookup"><span data-stu-id="dbce8-649">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="dbce8-650">Per specificare in modo esplicito la categoria, chiamare `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="dbce8-650">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="dbce8-651">L'uso di `ILogger<T>` equivale a chiamare `CreateLogger` con il nome completo di tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-651">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="dbce8-652">Livello di registrazione</span><span class="sxs-lookup"><span data-stu-id="dbce8-652">Log level</span></span>

<span data-ttu-id="dbce8-653">Ogni log specifica un valore <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="dbce8-653">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="dbce8-654">Il livello di registrazione indica la gravità o l'importanza.</span><span class="sxs-lookup"><span data-stu-id="dbce8-654">The log level indicates the severity or importance.</span></span> <span data-ttu-id="dbce8-655">È ad esempio possibile scrivere un log `Information` quando un metodo termina normalmente e un log `Warning` quando un metodo restituisce un codice di stato *404 Non trovato*.</span><span class="sxs-lookup"><span data-stu-id="dbce8-655">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="dbce8-656">Il codice seguente crea i log `Information` e `Warning`:</span><span class="sxs-lookup"><span data-stu-id="dbce8-656">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="dbce8-657">Nel codice precedente il primo parametro è l'[ID dell'evento di log](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="dbce8-657">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="dbce8-658">Il secondo parametro è un modello di messaggio con segnaposto per i valori degli argomenti forniti dai parametri dei metodi rimanenti.</span><span class="sxs-lookup"><span data-stu-id="dbce8-658">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="dbce8-659">I parametri dei metodi sono descritti nella [sezione relativa al modello di messaggio](#log-message-template) più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-659">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="dbce8-660">I metodi di registrazione che includono il livello nel nome del metodo (ad esempio `LogInformation` e `LogWarning`) sono [metodi di estensione per ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="dbce8-660">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="dbce8-661">Questi metodi chiamano un metodo `Log` che accetta un parametro `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-661">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="dbce8-662">È possibile chiamare il metodo `Log` direttamente anziché chiamare uno di questi metodi di estensione, ma la sintassi è relativamente complessa.</span><span class="sxs-lookup"><span data-stu-id="dbce8-662">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="dbce8-663">Per altre informazioni, vedere <xref:Microsoft.Extensions.Logging.ILogger> e il [codice sorgente delle estensioni del logger](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="dbce8-663">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="dbce8-664">ASP.NET Core definisce i livelli di registrazione seguenti, ordinati dal meno grave al più grave.</span><span class="sxs-lookup"><span data-stu-id="dbce8-664">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="dbce8-665">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="dbce8-665">Trace = 0</span></span>

  <span data-ttu-id="dbce8-666">Per informazioni in genere utili solo per il debug.</span><span class="sxs-lookup"><span data-stu-id="dbce8-666">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="dbce8-667">Questi messaggi possono contenere dati sensibili dell'applicazione e pertanto non devono essere abilitati in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-667">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="dbce8-668">*Disattivato per impostazione predefinita.*</span><span class="sxs-lookup"><span data-stu-id="dbce8-668">*Disabled by default.*</span></span>

* <span data-ttu-id="dbce8-669">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="dbce8-669">Debug = 1</span></span>

  <span data-ttu-id="dbce8-670">Per informazioni che possono essere utili per lo sviluppo e il debug.</span><span class="sxs-lookup"><span data-stu-id="dbce8-670">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="dbce8-671">Esempio: `Entering method Configure with flag set to true.` Abilitare i livelli di registrazione `Debug` nell'ambiente di produzione solo in fase di risoluzione dei problemi, per non fare aumentare eccessivamente il volume dei log.</span><span class="sxs-lookup"><span data-stu-id="dbce8-671">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="dbce8-672">Information = 2</span><span class="sxs-lookup"><span data-stu-id="dbce8-672">Information = 2</span></span>

  <span data-ttu-id="dbce8-673">Per tenere traccia del flusso generale dell'app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-673">For tracking the general flow of the app.</span></span> <span data-ttu-id="dbce8-674">Queste registrazioni hanno in genere un valore a lungo termine.</span><span class="sxs-lookup"><span data-stu-id="dbce8-674">These logs typically have some long-term value.</span></span> <span data-ttu-id="dbce8-675">Esempio: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="dbce8-675">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="dbce8-676">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="dbce8-676">Warning = 3</span></span>

  <span data-ttu-id="dbce8-677">Per gli eventi imprevisti o anomali nel flusso dell'app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-677">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="dbce8-678">Tali eventi potrebbero includere errori o altre condizioni che non provocano l'arresto dell'app, ma che potrebbe essere necessario analizzare.</span><span class="sxs-lookup"><span data-stu-id="dbce8-678">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="dbce8-679">Le eccezioni gestite sono una situazione comune in cui usare il livello di registrazione `Warning`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-679">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="dbce8-680">Esempio: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="dbce8-680">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="dbce8-681">Error = 4</span><span class="sxs-lookup"><span data-stu-id="dbce8-681">Error = 4</span></span>

  <span data-ttu-id="dbce8-682">Per errori ed eccezioni che non possono essere gestiti.</span><span class="sxs-lookup"><span data-stu-id="dbce8-682">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="dbce8-683">Questi messaggi indicano un errore nell'operazione o nell'attività in corso (ad esempio la richiesta HTTP corrente), non un errore a livello di app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-683">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="dbce8-684">Messaggio di registrazione di esempio:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="dbce8-684">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="dbce8-685">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="dbce8-685">Critical = 5</span></span>

  <span data-ttu-id="dbce8-686">Per gli errori che richiedono attenzione immediata.</span><span class="sxs-lookup"><span data-stu-id="dbce8-686">For failures that require immediate attention.</span></span> <span data-ttu-id="dbce8-687">Esempi: scenari di perdita di dati, spazio su disco insufficiente.</span><span class="sxs-lookup"><span data-stu-id="dbce8-687">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="dbce8-688">Usare il livello di registrazione per controllare la quantità di output di log scritto in un supporto di archiviazione specifico o in una finestra.</span><span class="sxs-lookup"><span data-stu-id="dbce8-688">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="dbce8-689">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="dbce8-689">For example:</span></span>

* <span data-ttu-id="dbce8-690">In produzione:</span><span class="sxs-lookup"><span data-stu-id="dbce8-690">In production:</span></span>
  * <span data-ttu-id="dbce8-691">La registrazione `Trace` `Information` ai livelli through produce un volume elevato di messaggi di log dettagliati.</span><span class="sxs-lookup"><span data-stu-id="dbce8-691">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="dbce8-692">Per controllare i costi e non `Trace` `Information` superare i limiti di archiviazione dei dati, eseguire il log-through dei messaggi di livello in un archivio dati con volumi elevati e a basso costo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-692">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="dbce8-693">La `Warning` registrazione `Critical` a livelli tramite livelli produce in genere un numero inferiore di messaggi di log più piccoli.</span><span class="sxs-lookup"><span data-stu-id="dbce8-693">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="dbce8-694">Pertanto, i costi e i limiti di archiviazione in genere non sono un problema, il che si traduce in una maggiore flessibilità di scelta dell'archivio dati.</span><span class="sxs-lookup"><span data-stu-id="dbce8-694">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="dbce8-695">Durante lo sviluppo:</span><span class="sxs-lookup"><span data-stu-id="dbce8-695">During development:</span></span>
  * <span data-ttu-id="dbce8-696">`Warning` Registrare `Critical` i messaggi nella console.</span><span class="sxs-lookup"><span data-stu-id="dbce8-696">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="dbce8-697">Aggiungere `Trace` `Information` i messaggi durante la risoluzione dei problemi.</span><span class="sxs-lookup"><span data-stu-id="dbce8-697">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="dbce8-698">La sezione [Filtro dei log](#log-filtering) più avanti in questo articolo descrive come controllare quali livelli di registrazione gestisce un provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-698">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="dbce8-699">ASP.NET Core scrive log per gli eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="dbce8-699">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="dbce8-700">Gli esempi di log illustrati in precedenza in questo articolo escludono i log al di sotto del livello `Information`, quindi non vengono creati log di livello `Debug` o `Trace`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-700">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="dbce8-701">Ecco un esempio di log della console prodotti eseguendo l'app di esempio configurata in modo da mostrare i log di livello `Debug`:</span><span class="sxs-lookup"><span data-stu-id="dbce8-701">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="dbce8-702">ID evento di registrazione</span><span class="sxs-lookup"><span data-stu-id="dbce8-702">Log event ID</span></span>

<span data-ttu-id="dbce8-703">Ogni log può specificare un *ID evento*.</span><span class="sxs-lookup"><span data-stu-id="dbce8-703">Each log can specify an *event ID*.</span></span> <span data-ttu-id="dbce8-704">A tale scopo, l'app di esempio usa una classe `LoggingEvents` definita a livello locale:</span><span class="sxs-lookup"><span data-stu-id="dbce8-704">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="dbce8-705">Un ID evento associa un set di eventi.</span><span class="sxs-lookup"><span data-stu-id="dbce8-705">An event ID associates a set of events.</span></span> <span data-ttu-id="dbce8-706">Ad esempio, tutti i log correlati alla visualizzazione di un elenco di elementi in una pagina potrebbero essere indicati con 1001.</span><span class="sxs-lookup"><span data-stu-id="dbce8-706">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="dbce8-707">Il provider di log può archiviare l'ID evento in un campo ID o nel messaggio di registrazione oppure non archiviarlo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-707">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="dbce8-708">Il provider Debug non visualizza gli ID evento.</span><span class="sxs-lookup"><span data-stu-id="dbce8-708">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="dbce8-709">Il provider Console visualizza gli ID evento tra parentesi quadre dopo la categoria:</span><span class="sxs-lookup"><span data-stu-id="dbce8-709">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="dbce8-710">Modello di messaggio di registrazione</span><span class="sxs-lookup"><span data-stu-id="dbce8-710">Log message template</span></span>

<span data-ttu-id="dbce8-711">Ogni log specifica un modello di messaggio.</span><span class="sxs-lookup"><span data-stu-id="dbce8-711">Each log specifies a message template.</span></span> <span data-ttu-id="dbce8-712">Il modello di messaggio può contenere segnaposto per i quali vengono forniti argomenti.</span><span class="sxs-lookup"><span data-stu-id="dbce8-712">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="dbce8-713">Usare nomi per i segnaposto, non numeri.</span><span class="sxs-lookup"><span data-stu-id="dbce8-713">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="dbce8-714">L'ordine dei segnaposto, non il loro nome, determina i parametri da usare per fornire i valori corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="dbce8-714">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="dbce8-715">Nel codice seguente, si noti che i nomi dei parametri non sono in sequenza nel modello di messaggio:</span><span class="sxs-lookup"><span data-stu-id="dbce8-715">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="dbce8-716">Questo codice crea un messaggio di log con i valori dei parametri in sequenza:</span><span class="sxs-lookup"><span data-stu-id="dbce8-716">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="dbce8-717">Il framework di registrazione funziona in questo modo per consentire ai provider di registrazione di implementare la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="dbce8-717">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="dbce8-718">Al sistema di registrazione vengono passati gli argomenti e non solo il modello di messaggio formattato.</span><span class="sxs-lookup"><span data-stu-id="dbce8-718">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="dbce8-719">Queste informazioni consentono ai provider di registrazione di archiviare i valori dei parametri come campi.</span><span class="sxs-lookup"><span data-stu-id="dbce8-719">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="dbce8-720">Si supponga, ad esempio, che il metodo logger esegua chiamate simili alla seguente:</span><span class="sxs-lookup"><span data-stu-id="dbce8-720">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="dbce8-721">Se si inviano i log all'archiviazione tabelle di Azure, ogni entità tabella di Azure può avere le proprietà `ID` e `RequestTime`, il che semplifica le query sui dati dei log.</span><span class="sxs-lookup"><span data-stu-id="dbce8-721">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="dbce8-722">Una query può trovare tutti i log entro un determinato intervallo `RequestTime` senza che sia necessario analizzare il messaggio di testo per determinare l'intervallo di tempo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-722">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="dbce8-723">Registrazione delle eccezioni</span><span class="sxs-lookup"><span data-stu-id="dbce8-723">Logging exceptions</span></span>

<span data-ttu-id="dbce8-724">I metodi logger dispongono di overload che consentono di passare un'eccezione, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="dbce8-724">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="dbce8-725">Provider diversi gestiscono le informazioni sulle eccezioni in modi diversi.</span><span class="sxs-lookup"><span data-stu-id="dbce8-725">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="dbce8-726">Ecco un esempio di output del provider Debug dal codice sopra riportato.</span><span class="sxs-lookup"><span data-stu-id="dbce8-726">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="dbce8-727">Filtro del log</span><span class="sxs-lookup"><span data-stu-id="dbce8-727">Log filtering</span></span>

<span data-ttu-id="dbce8-728">È possibile specificare un livello di registrazione minimo per un provider e una categoria specifici oppure per tutti i provider o tutte le categorie.</span><span class="sxs-lookup"><span data-stu-id="dbce8-728">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="dbce8-729">Tutti i log sotto il livello minimo non sono passati al provider, quindi non vengono visualizzati o archiviati.</span><span class="sxs-lookup"><span data-stu-id="dbce8-729">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="dbce8-730">Per eliminare tutti i log, specificare `LogLevel.None` come livello di registrazione minimo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-730">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="dbce8-731">Il valore intero di `LogLevel.None` è 6, maggiore di `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="dbce8-731">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="dbce8-732">Creare regole di filtro nella configurazione</span><span class="sxs-lookup"><span data-stu-id="dbce8-732">Create filter rules in configuration</span></span>

<span data-ttu-id="dbce8-733">Il codice del `CreateDefaultBuilder` modello di progetto chiama per impostare la registrazione per i provider Console, Debug e EventSource (ASP.NET Core 2.2 o versioni successive).</span><span class="sxs-lookup"><span data-stu-id="dbce8-733">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="dbce8-734">Il metodo `CreateDefaultBuilder` imposta inoltre la registrazione per la ricerca della configurazione in una sezione `Logging`, come illustrato [in precedenza in questo articolo](#configuration).</span><span class="sxs-lookup"><span data-stu-id="dbce8-734">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="dbce8-735">I dati di configurazione specificano i livelli di registrazione minimi in base al provider e alla categoria, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="dbce8-735">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="dbce8-736">Questo codice JSON crea sei regole di filtro, una per il provider Debug, quattro per il provider Console e una per tutti i provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-736">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="dbce8-737">Quando viene creato un oggetto `ILogger`, viene scelta una singola regola per ogni provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-737">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="dbce8-738">Regole di filtro nel codice</span><span class="sxs-lookup"><span data-stu-id="dbce8-738">Filter rules in code</span></span>

<span data-ttu-id="dbce8-739">L'esempio seguente illustra come registrare le regole di filtro nel codice:</span><span class="sxs-lookup"><span data-stu-id="dbce8-739">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="dbce8-740">Il secondo `AddFilter` specifica il provider Debug tramite il nome del tipo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-740">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="dbce8-741">Il primo `AddFilter` si applica a tutti i provider in quanto non consente di specificare un tipo di provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-741">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="dbce8-742">Applicazione delle regole di filtro</span><span class="sxs-lookup"><span data-stu-id="dbce8-742">How filtering rules are applied</span></span>

<span data-ttu-id="dbce8-743">I dati di configurazione e il codice `AddFilter` illustrato negli esempi precedenti creano le regole indicate nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="dbce8-743">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="dbce8-744">I primi sei provengono dall'esempio di configurazione e gli ultimi due dall'esempio di codice.</span><span class="sxs-lookup"><span data-stu-id="dbce8-744">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="dbce8-745">Number</span><span class="sxs-lookup"><span data-stu-id="dbce8-745">Number</span></span> | <span data-ttu-id="dbce8-746">Provider</span><span class="sxs-lookup"><span data-stu-id="dbce8-746">Provider</span></span>      | <span data-ttu-id="dbce8-747">Categorie che iniziano con...</span><span class="sxs-lookup"><span data-stu-id="dbce8-747">Categories that begin with ...</span></span>          | <span data-ttu-id="dbce8-748">Livello di registrazione minimo</span><span class="sxs-lookup"><span data-stu-id="dbce8-748">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="dbce8-749">1</span><span class="sxs-lookup"><span data-stu-id="dbce8-749">1</span></span>      | <span data-ttu-id="dbce8-750">Debug</span><span class="sxs-lookup"><span data-stu-id="dbce8-750">Debug</span></span>         | <span data-ttu-id="dbce8-751">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="dbce8-751">All categories</span></span>                          | <span data-ttu-id="dbce8-752">Informazioni</span><span class="sxs-lookup"><span data-stu-id="dbce8-752">Information</span></span>       |
| <span data-ttu-id="dbce8-753">2</span><span class="sxs-lookup"><span data-stu-id="dbce8-753">2</span></span>      | <span data-ttu-id="dbce8-754">Console</span><span class="sxs-lookup"><span data-stu-id="dbce8-754">Console</span></span>       | <span data-ttu-id="dbce8-755">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="dbce8-755">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="dbce8-756">Avviso</span><span class="sxs-lookup"><span data-stu-id="dbce8-756">Warning</span></span>           |
| <span data-ttu-id="dbce8-757">3</span><span class="sxs-lookup"><span data-stu-id="dbce8-757">3</span></span>      | <span data-ttu-id="dbce8-758">Console</span><span class="sxs-lookup"><span data-stu-id="dbce8-758">Console</span></span>       | <span data-ttu-id="dbce8-759">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="dbce8-759">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="dbce8-760">Debug</span><span class="sxs-lookup"><span data-stu-id="dbce8-760">Debug</span></span>             |
| <span data-ttu-id="dbce8-761">4</span><span class="sxs-lookup"><span data-stu-id="dbce8-761">4</span></span>      | <span data-ttu-id="dbce8-762">Console</span><span class="sxs-lookup"><span data-stu-id="dbce8-762">Console</span></span>       | <span data-ttu-id="dbce8-763">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="dbce8-763">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="dbce8-764">Errore</span><span class="sxs-lookup"><span data-stu-id="dbce8-764">Error</span></span>             |
| <span data-ttu-id="dbce8-765">5</span><span class="sxs-lookup"><span data-stu-id="dbce8-765">5</span></span>      | <span data-ttu-id="dbce8-766">Console</span><span class="sxs-lookup"><span data-stu-id="dbce8-766">Console</span></span>       | <span data-ttu-id="dbce8-767">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="dbce8-767">All categories</span></span>                          | <span data-ttu-id="dbce8-768">Informazioni</span><span class="sxs-lookup"><span data-stu-id="dbce8-768">Information</span></span>       |
| <span data-ttu-id="dbce8-769">6</span><span class="sxs-lookup"><span data-stu-id="dbce8-769">6</span></span>      | <span data-ttu-id="dbce8-770">Tutti i provider</span><span class="sxs-lookup"><span data-stu-id="dbce8-770">All providers</span></span> | <span data-ttu-id="dbce8-771">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="dbce8-771">All categories</span></span>                          | <span data-ttu-id="dbce8-772">Debug</span><span class="sxs-lookup"><span data-stu-id="dbce8-772">Debug</span></span>             |
| <span data-ttu-id="dbce8-773">7</span><span class="sxs-lookup"><span data-stu-id="dbce8-773">7</span></span>      | <span data-ttu-id="dbce8-774">Tutti i provider</span><span class="sxs-lookup"><span data-stu-id="dbce8-774">All providers</span></span> | <span data-ttu-id="dbce8-775">Sistema</span><span class="sxs-lookup"><span data-stu-id="dbce8-775">System</span></span>                                  | <span data-ttu-id="dbce8-776">Debug</span><span class="sxs-lookup"><span data-stu-id="dbce8-776">Debug</span></span>             |
| <span data-ttu-id="dbce8-777">8</span><span class="sxs-lookup"><span data-stu-id="dbce8-777">8</span></span>      | <span data-ttu-id="dbce8-778">Debug</span><span class="sxs-lookup"><span data-stu-id="dbce8-778">Debug</span></span>         | <span data-ttu-id="dbce8-779">Microsoft</span><span class="sxs-lookup"><span data-stu-id="dbce8-779">Microsoft</span></span>                               | <span data-ttu-id="dbce8-780">Trace</span><span class="sxs-lookup"><span data-stu-id="dbce8-780">Trace</span></span>             |

<span data-ttu-id="dbce8-781">Quando viene creato un oggetto `ILogger`, l'oggetto `ILoggerFactory` seleziona una singola regola per ogni provider da applicare al logger.</span><span class="sxs-lookup"><span data-stu-id="dbce8-781">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="dbce8-782">Tutti i messaggi scritti da un'istanza di `ILogger` vengono filtrati in base alle regole selezionate.</span><span class="sxs-lookup"><span data-stu-id="dbce8-782">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="dbce8-783">Tra le regole disponibili viene selezionata la regola più specifica possibile per ogni coppia di categoria e provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-783">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="dbce8-784">L'algoritmo seguente viene usato per ogni provider quando viene creato un `ILogger` per una determinata categoria:</span><span class="sxs-lookup"><span data-stu-id="dbce8-784">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="dbce8-785">Selezionare tutte le regole corrispondenti al provider o al relativo alias.</span><span class="sxs-lookup"><span data-stu-id="dbce8-785">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="dbce8-786">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole con un provider vuoto.</span><span class="sxs-lookup"><span data-stu-id="dbce8-786">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="dbce8-787">Dal risultato del passaggio precedente, selezionare le regole con il prefisso di categoria corrispondente più lungo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-787">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="dbce8-788">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole che non specificano una categoria.</span><span class="sxs-lookup"><span data-stu-id="dbce8-788">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="dbce8-789">Se sono selezionate più regole, scegliere l'**ultima**.</span><span class="sxs-lookup"><span data-stu-id="dbce8-789">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="dbce8-790">Se non è selezionata alcuna regola, usare `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-790">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="dbce8-791">Con l'elenco di regole precedente, si supponga di creare un oggetto `ILogger` per la categoria "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="dbce8-791">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="dbce8-792">Per il provider Debug si applicano le regole 1, 6 e 8.</span><span class="sxs-lookup"><span data-stu-id="dbce8-792">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="dbce8-793">La regola 8 è più specifica, così sarà quella selezionata.</span><span class="sxs-lookup"><span data-stu-id="dbce8-793">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="dbce8-794">Per il provider Console si applicano le regole 3, 4, 5 e 6.</span><span class="sxs-lookup"><span data-stu-id="dbce8-794">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="dbce8-795">La regola 3 è la più specifica.</span><span class="sxs-lookup"><span data-stu-id="dbce8-795">Rule 3 is most specific.</span></span>

<span data-ttu-id="dbce8-796">L'istanza di `ILogger` risultante invia i log di livello `Trace` o superiore al provider Debug.</span><span class="sxs-lookup"><span data-stu-id="dbce8-796">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="dbce8-797">I log di livello `Debug` o superiore vengono inviati al provider Console.</span><span class="sxs-lookup"><span data-stu-id="dbce8-797">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="dbce8-798">Alias dei provider</span><span class="sxs-lookup"><span data-stu-id="dbce8-798">Provider aliases</span></span>

<span data-ttu-id="dbce8-799">Ogni provider definisce un *alias* che può essere utilizzato nella configurazione al posto del nome completo di tipo.</span><span class="sxs-lookup"><span data-stu-id="dbce8-799">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="dbce8-800">Per i provider predefiniti, usare gli alias seguenti:</span><span class="sxs-lookup"><span data-stu-id="dbce8-800">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="dbce8-801">Console</span><span class="sxs-lookup"><span data-stu-id="dbce8-801">Console</span></span>
* <span data-ttu-id="dbce8-802">Debug</span><span class="sxs-lookup"><span data-stu-id="dbce8-802">Debug</span></span>
* <span data-ttu-id="dbce8-803">EventSource</span><span class="sxs-lookup"><span data-stu-id="dbce8-803">EventSource</span></span>
* <span data-ttu-id="dbce8-804">EventLog</span><span class="sxs-lookup"><span data-stu-id="dbce8-804">EventLog</span></span>
* <span data-ttu-id="dbce8-805">TraceSource</span><span class="sxs-lookup"><span data-stu-id="dbce8-805">TraceSource</span></span>
* <span data-ttu-id="dbce8-806">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="dbce8-806">AzureAppServicesFile</span></span>
* <span data-ttu-id="dbce8-807">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="dbce8-807">AzureAppServicesBlob</span></span>
* <span data-ttu-id="dbce8-808">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="dbce8-808">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="dbce8-809">Livello minimo predefinito</span><span class="sxs-lookup"><span data-stu-id="dbce8-809">Default minimum level</span></span>

<span data-ttu-id="dbce8-810">Esiste un'impostazione del livello minimo che diventa effettiva solo se non si applica alcuna regola di configurazione o codice per un provider e una categoria specifici.</span><span class="sxs-lookup"><span data-stu-id="dbce8-810">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="dbce8-811">L'esempio seguente illustra come impostare il livello minimo:</span><span class="sxs-lookup"><span data-stu-id="dbce8-811">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="dbce8-812">Se non si imposta in modo esplicito il livello minimo, il valore predefinito è `Information`, che indica che i log `Trace` e `Debug` vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="dbce8-812">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="dbce8-813">Funzioni di filtro</span><span class="sxs-lookup"><span data-stu-id="dbce8-813">Filter functions</span></span>

<span data-ttu-id="dbce8-814">Una funzione di filtro viene richiamata per tutti i provider e le categorie a cui non sono assegnate regole tramite la configurazione o il codice.</span><span class="sxs-lookup"><span data-stu-id="dbce8-814">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="dbce8-815">Il codice della funzione ha accesso al tipo di provider, alla categoria e al livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-815">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="dbce8-816">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="dbce8-816">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="dbce8-817">Livelli e categorie di sistema</span><span class="sxs-lookup"><span data-stu-id="dbce8-817">System categories and levels</span></span>

<span data-ttu-id="dbce8-818">Di seguito sono elencate alcune categorie usate da ASP.NET Core ed Entity Framework Core, con note sui log previsti per ognuna:</span><span class="sxs-lookup"><span data-stu-id="dbce8-818">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="dbce8-819">Category</span><span class="sxs-lookup"><span data-stu-id="dbce8-819">Category</span></span>                            | <span data-ttu-id="dbce8-820">Note</span><span class="sxs-lookup"><span data-stu-id="dbce8-820">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="dbce8-821">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="dbce8-821">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="dbce8-822">Diagnostica generale di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbce8-822">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="dbce8-823">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="dbce8-823">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="dbce8-824">Chiavi considerate, trovate e usate.</span><span class="sxs-lookup"><span data-stu-id="dbce8-824">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="dbce8-825">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="dbce8-825">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="dbce8-826">Host consentiti.</span><span class="sxs-lookup"><span data-stu-id="dbce8-826">Hosts allowed.</span></span> |
| <span data-ttu-id="dbce8-827">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="dbce8-827">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="dbce8-828">Tempo impiegato per il completamento delle richieste HTTP e ora di inizio delle richieste.</span><span class="sxs-lookup"><span data-stu-id="dbce8-828">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="dbce8-829">Assembly di avvio dell'hosting caricati.</span><span class="sxs-lookup"><span data-stu-id="dbce8-829">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="dbce8-830">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="dbce8-830">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="dbce8-831">Diagnostica di MVC e Razor.</span><span class="sxs-lookup"><span data-stu-id="dbce8-831">MVC and Razor diagnostics.</span></span> <span data-ttu-id="dbce8-832">Associazione di modelli, esecuzione di filtri, compilazione di viste, selezione di azioni.</span><span class="sxs-lookup"><span data-stu-id="dbce8-832">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="dbce8-833">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="dbce8-833">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="dbce8-834">Informazioni sulla corrispondenza di route.</span><span class="sxs-lookup"><span data-stu-id="dbce8-834">Route matching information.</span></span> |
| <span data-ttu-id="dbce8-835">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="dbce8-835">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="dbce8-836">Risposte di avvio, arresto e keep-alive per la connessione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-836">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="dbce8-837">Informazioni sui certificati HTTPS.</span><span class="sxs-lookup"><span data-stu-id="dbce8-837">HTTPS certificate information.</span></span> |
| <span data-ttu-id="dbce8-838">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="dbce8-838">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="dbce8-839">File forniti.</span><span class="sxs-lookup"><span data-stu-id="dbce8-839">Files served.</span></span> |
| <span data-ttu-id="dbce8-840">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="dbce8-840">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="dbce8-841">Diagnostica generale di Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="dbce8-841">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="dbce8-842">Attività e configurazione del database, rilevamento delle modifiche, migrazioni.</span><span class="sxs-lookup"><span data-stu-id="dbce8-842">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="dbce8-843">Ambiti dei log</span><span class="sxs-lookup"><span data-stu-id="dbce8-843">Log scopes</span></span>

 <span data-ttu-id="dbce8-844">Un *ambito* può raggruppare un set di operazioni logiche.</span><span class="sxs-lookup"><span data-stu-id="dbce8-844">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="dbce8-845">Questo raggruppamento può essere usato per collegare gli stessi dati a ogni log creato come parte di un set.</span><span class="sxs-lookup"><span data-stu-id="dbce8-845">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="dbce8-846">Ad esempio, ogni log creato come parte dell'elaborazione di una transazione può includere l'ID transazione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-846">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="dbce8-847">Un ambito è un tipo `IDisposable` restituito dal metodo <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> e viene mantenuto fino all'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-847">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="dbce8-848">Un ambito si usa mediante il wrapping delle chiamate del logger in un blocco `using`:</span><span class="sxs-lookup"><span data-stu-id="dbce8-848">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="dbce8-849">Il codice seguente abilita gli ambiti per il provider Console:</span><span class="sxs-lookup"><span data-stu-id="dbce8-849">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="dbce8-850">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="dbce8-850">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="dbce8-851">La configurazione dell'opzione del logger della console `IncludeScopes` è necessaria per abilitare la registrazione basata sull'ambito.</span><span class="sxs-lookup"><span data-stu-id="dbce8-851">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="dbce8-852">Per informazioni sulla configurazione, vedere la sezione [Configurazione](#configuration).</span><span class="sxs-lookup"><span data-stu-id="dbce8-852">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="dbce8-853">Ogni messaggio di registrazione include le informazioni sull'ambito:</span><span class="sxs-lookup"><span data-stu-id="dbce8-853">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="dbce8-854">Provider di registrazione predefiniti</span><span class="sxs-lookup"><span data-stu-id="dbce8-854">Built-in logging providers</span></span>

<span data-ttu-id="dbce8-855">ASP.NET Core include i provider seguenti:</span><span class="sxs-lookup"><span data-stu-id="dbce8-855">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="dbce8-856">Console</span><span class="sxs-lookup"><span data-stu-id="dbce8-856">Console</span></span>](#console-provider)
* [<span data-ttu-id="dbce8-857">Debug</span><span class="sxs-lookup"><span data-stu-id="dbce8-857">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="dbce8-858">Eventsource</span><span class="sxs-lookup"><span data-stu-id="dbce8-858">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="dbce8-859">EventLog</span><span class="sxs-lookup"><span data-stu-id="dbce8-859">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="dbce8-860">TraceSource</span><span class="sxs-lookup"><span data-stu-id="dbce8-860">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="dbce8-861">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="dbce8-861">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="dbce8-862">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="dbce8-862">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="dbce8-863">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="dbce8-863">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="dbce8-864">Per informazioni su StdOut e la registrazione debug con il modulo ASP.NET Core, vedere <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="dbce8-864">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="dbce8-865">Provider Console</span><span class="sxs-lookup"><span data-stu-id="dbce8-865">Console provider</span></span>

<span data-ttu-id="dbce8-866">Il pacchetto del provider [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) invia l'output della registrazione alla console.</span><span class="sxs-lookup"><span data-stu-id="dbce8-866">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="dbce8-867">Per visualizzare l'output di registrazione del provider Console, aprire un prompt dei comandi nella cartella del progetto ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="dbce8-867">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="dbce8-868">Provider Debug</span><span class="sxs-lookup"><span data-stu-id="dbce8-868">Debug provider</span></span>

<span data-ttu-id="dbce8-869">Il pacchetto del provider [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) scrive l'output della registrazione usando la classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (chiamate del metodo `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="dbce8-869">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="dbce8-870">In Linux, questo provider scrive i log in */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="dbce8-870">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="dbce8-871">Provider dell'origine eventi</span><span class="sxs-lookup"><span data-stu-id="dbce8-871">Event Source provider</span></span>

<span data-ttu-id="dbce8-872">Il pacchetto del provider [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) viene scritto in `Microsoft-Extensions-Logging`una piattaforma multipiattaforma Origine evento con il nome .</span><span class="sxs-lookup"><span data-stu-id="dbce8-872">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="dbce8-873">In Windows, il provider utilizza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="dbce8-873">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="dbce8-874">Il provider di origine `CreateDefaultBuilder` evento viene aggiunto automaticamente quando viene chiamato per compilare l'host.</span><span class="sxs-lookup"><span data-stu-id="dbce8-874">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="dbce8-875">Utilizzare [l'utilità PerfView](https://github.com/Microsoft/perfview) per raccogliere e visualizzare i log.</span><span class="sxs-lookup"><span data-stu-id="dbce8-875">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="dbce8-876">Sono disponibili altri strumenti per la visualizzazione dei log ETW, ma PerfView fornisce un'esperienza ottimale per l'uso con gli eventi ETW generati da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbce8-876">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="dbce8-877">Per configurare PerfView per la raccolta degli eventi registrati da questo provider, aggiungere la stringa `*Microsoft-Extensions-Logging` nell'elenco **Provider aggiuntivi**</span><span class="sxs-lookup"><span data-stu-id="dbce8-877">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="dbce8-878">(non dimenticare l'asterisco all'inizio della stringa).</span><span class="sxs-lookup"><span data-stu-id="dbce8-878">(Don't miss the asterisk at the start of the string.)</span></span>

![Provider aggiuntivi di PerfView](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="dbce8-880">Provider EventLog di Windows</span><span class="sxs-lookup"><span data-stu-id="dbce8-880">Windows EventLog provider</span></span>

<span data-ttu-id="dbce8-881">Il pacchetto di provider [Microsoft.Extensions.Logging.AventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) invia l'output del log al registro eventi di Windows.</span><span class="sxs-lookup"><span data-stu-id="dbce8-881">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="dbce8-882">Gli [overload di AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) consentono di passare <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="dbce8-882">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="dbce8-883">Se `null` specificato o non specificato, vengono utilizzate le seguenti impostazioni predefinite:</span><span class="sxs-lookup"><span data-stu-id="dbce8-883">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="dbce8-884">`LogName`&ndash; "Applicazione"</span><span class="sxs-lookup"><span data-stu-id="dbce8-884">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="dbce8-885">`SourceName`&ndash; "Runtime .NET"</span><span class="sxs-lookup"><span data-stu-id="dbce8-885">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="dbce8-886">`MachineName` &ndash; computer locale</span><span class="sxs-lookup"><span data-stu-id="dbce8-886">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="dbce8-887">Gli eventi vengono registrati per [il livello di avviso e superiore](#log-level).</span><span class="sxs-lookup"><span data-stu-id="dbce8-887">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="dbce8-888">Per registrare `Warning`eventi inferiori a , impostare in modo esplicito il livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-888">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="dbce8-889">Ad esempio, aggiungere quanto segue al file *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="dbce8-889">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="dbce8-890">Provider TraceSource</span><span class="sxs-lookup"><span data-stu-id="dbce8-890">TraceSource provider</span></span>

<span data-ttu-id="dbce8-891">Il pacchetto di provider [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa le librerie e i provider <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="dbce8-891">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="dbce8-892">Gli [overload di AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) consentono di passare un cambio di origine e un listener di traccia.</span><span class="sxs-lookup"><span data-stu-id="dbce8-892">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="dbce8-893">Per usare questo provider, un'app deve essere in esecuzione in .NET Framework (invece che in .NET Core).</span><span class="sxs-lookup"><span data-stu-id="dbce8-893">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="dbce8-894">Il provider può instradare i messaggi a diversi [listener](/dotnet/framework/debug-trace-profile/trace-listeners), come <xref:System.Diagnostics.TextWriterTraceListener>, usato nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="dbce8-894">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="dbce8-895">Provider del Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="dbce8-895">Azure App Service provider</span></span>

<span data-ttu-id="dbce8-896">Il pacchetto di provider [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) scrive i log in file di testo nel file system di un'app del Servizio app di Azure e nell'[archivio di BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in un account di archiviazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="dbce8-896">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="dbce8-897">Il pacchetto del provider non è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="dbce8-897">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="dbce8-898">Quando la destinazione è .NET Framework o il riferimento è il metapacchetto `Microsoft.AspNetCore.App`, aggiungere il pacchetto del provider al progetto.</span><span class="sxs-lookup"><span data-stu-id="dbce8-898">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="dbce8-899">Un overload di <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> consente di passare <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="dbce8-899">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="dbce8-900">L'oggetto impostazioni può eseguire l'override delle impostazioni predefinite, ad esempio il modello di output di registrazione, il nome di BLOB e il limite di dimensioni di file.</span><span class="sxs-lookup"><span data-stu-id="dbce8-900">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="dbce8-901">Il *modello di output* è un modello di messaggio che viene applicato a tutti i log in aggiunta a quanto specificato quando si chiama un metodo `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-901">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="dbce8-902">Quando si distribuisce un'app del servizio app, l'applicazione applica le impostazioni della sezione [Log del servizio app](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) della pagina **Servizio app** del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="dbce8-902">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="dbce8-903">Quando le impostazioni seguenti vengono aggiornate, le modifiche hanno effetto immediatamente senza richiedere un riavvio o la ridistribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-903">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="dbce8-904">**Registrazione applicazioni (file system)**</span><span class="sxs-lookup"><span data-stu-id="dbce8-904">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="dbce8-905">**Registrazione applicazione (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="dbce8-905">**Application Logging (Blob)**</span></span>

<span data-ttu-id="dbce8-906">Il percorso predefinito per i file di log è nella cartella *D:\\home\\LogFiles\\Application* e il nome di file predefinito è *diagnostics-aaaammgg.txt*.</span><span class="sxs-lookup"><span data-stu-id="dbce8-906">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="dbce8-907">Il limite predefinito per le dimensioni del file è 10 MB e il numero massimo predefinito di file conservati è 2.</span><span class="sxs-lookup"><span data-stu-id="dbce8-907">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="dbce8-908">Il nome BLOB predefinito è *{nome-app}{timestamp}/aaaa/mm/gg/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="dbce8-908">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="dbce8-909">Il provider funziona solo quando il progetto viene eseguito nell'ambiente di Azure.</span><span class="sxs-lookup"><span data-stu-id="dbce8-909">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="dbce8-910">Non ha alcun effetto quando il progetto viene eseguito localmente, in quanto non scrive nulla in file locali o in archivi di sviluppo locali per i BLOB.</span><span class="sxs-lookup"><span data-stu-id="dbce8-910">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="dbce8-911">Flusso di registrazione di Azure</span><span class="sxs-lookup"><span data-stu-id="dbce8-911">Azure log streaming</span></span>

<span data-ttu-id="dbce8-912">Il flusso di registrazione di Azure consente di visualizzare l'attività di registrazione in tempo reale da:</span><span class="sxs-lookup"><span data-stu-id="dbce8-912">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="dbce8-913">Server applicazioni</span><span class="sxs-lookup"><span data-stu-id="dbce8-913">The app server</span></span>
* <span data-ttu-id="dbce8-914">Server Web</span><span class="sxs-lookup"><span data-stu-id="dbce8-914">The web server</span></span>
* <span data-ttu-id="dbce8-915">Traccia delle richieste non riuscite</span><span class="sxs-lookup"><span data-stu-id="dbce8-915">Failed request tracing</span></span>

<span data-ttu-id="dbce8-916">Per configurare il flusso di registrazione di Azure:</span><span class="sxs-lookup"><span data-stu-id="dbce8-916">To configure Azure log streaming:</span></span>

* <span data-ttu-id="dbce8-917">Passare alla pagina **Log del servizio app** dalla pagina del portale dell'app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-917">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="dbce8-918">Impostare **Registrazione applicazioni (file system)** su **Attiva**.</span><span class="sxs-lookup"><span data-stu-id="dbce8-918">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="dbce8-919">Scegliere il livello di registrazione in **Livello**.</span><span class="sxs-lookup"><span data-stu-id="dbce8-919">Choose the log **Level**.</span></span> <span data-ttu-id="dbce8-920">Questa impostazione si applica solo allo streaming dei log di Azure, non agli altri provider di registrazione nell'app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-920">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="dbce8-921">Passare alla pagina **Flusso di registrazione** per visualizzare i messaggi dell'app.</span><span class="sxs-lookup"><span data-stu-id="dbce8-921">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="dbce8-922">I messaggi vengono registrati dall'app tramite l'interfaccia `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="dbce8-922">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="dbce8-923">Registrazione di traccia di Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="dbce8-923">Azure Application Insights trace logging</span></span>

<span data-ttu-id="dbce8-924">Il pacchetto di provider [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) scrive log in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="dbce8-924">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="dbce8-925">Application Insights è un servizio che monitora un'app Web e fornisce gli strumenti per l'esecuzione di query sui dati di telemetria e la loro analisi.</span><span class="sxs-lookup"><span data-stu-id="dbce8-925">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="dbce8-926">Se si usa questo provider, è possibile eseguire query sui log e analizzarli usando gli strumenti di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="dbce8-926">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="dbce8-927">Il provider di registrazione è incluso come dipendenza di [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), ovvero il pacchetto che fornisce tutti i dati di telemetria disponibili per ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbce8-927">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="dbce8-928">Se si usa questo pacchetto, non è necessario installare il pacchetto di provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-928">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="dbce8-929">Non usare il pacchetto [](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)Microsoft.ApplicationInsights.Web&mdash;, che è per ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="dbce8-929">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="dbce8-930">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="dbce8-930">For more information, see the following resources:</span></span>

* [<span data-ttu-id="dbce8-931">Panoramica di Application Insights</span><span class="sxs-lookup"><span data-stu-id="dbce8-931">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="dbce8-932">[Application Insights per applicazioni ASP.NET Core](/azure/azure-monitor/app/asp-net-core): iniziare da qui se si vuole implementare l'intervallo completo dei dati di telemetria di Application Insights insieme alla registrazione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-932">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="dbce8-933">[ApplicationInsightsLoggerProvider per log ILogger .NET Core](/azure/azure-monitor/app/ilogger): iniziare da qui se si vuole implementare il provider di registrazione senza gli altri dati di telemetria di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="dbce8-933">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="dbce8-934">[Adattatori di registrazione di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="dbce8-934">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="dbce8-935">[Installare, configurare e inizializzare Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): esercitazione interattiva nel sito Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="dbce8-935">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="dbce8-936">Provider di registrazione di terze parti</span><span class="sxs-lookup"><span data-stu-id="dbce8-936">Third-party logging providers</span></span>

<span data-ttu-id="dbce8-937">Framework di registrazione di terze parti che usano ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="dbce8-937">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="dbce8-938">[elmah.io](https://elmah.io/) ([repository GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="dbce8-938">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="dbce8-939">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repository GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="dbce8-939">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="dbce8-940">[JSNLog](https://jsnlog.com/) ([repository GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="dbce8-940">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="dbce8-941">[KissLog.net](https://kisslog.net/) ([repository GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="dbce8-941">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="dbce8-942">[Log4Net](https://logging.apache.org/log4net/) ([repository GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="dbce8-942">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="dbce8-943">[Loggr](https://loggr.net/) ([repository GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="dbce8-943">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="dbce8-944">[NLog](https://nlog-project.org/) ([repository GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="dbce8-944">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="dbce8-945">[Sentry](https://sentry.io/welcome/) ([repository GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="dbce8-945">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="dbce8-946">[Serilog](https://serilog.net/) ([repository GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="dbce8-946">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="dbce8-947">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repository Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="dbce8-947">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="dbce8-948">Alcuni framework di terze parti possono eseguire la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="dbce8-948">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="dbce8-949">L'uso di un framework di terze parti è simile a quello di uno dei provider predefiniti:</span><span class="sxs-lookup"><span data-stu-id="dbce8-949">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="dbce8-950">Aggiungere un pacchetto NuGet al progetto.</span><span class="sxs-lookup"><span data-stu-id="dbce8-950">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="dbce8-951">Chiamare `ILoggerFactory` un metodo di estensione fornito dal framework di registrazione.</span><span class="sxs-lookup"><span data-stu-id="dbce8-951">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="dbce8-952">Per altre informazioni, vedere la documentazione di ogni provider.</span><span class="sxs-lookup"><span data-stu-id="dbce8-952">For more information, see each provider's documentation.</span></span> <span data-ttu-id="dbce8-953">I provider di registrazione di terze parti non sono supportati da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="dbce8-953">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dbce8-954">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="dbce8-954">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
