---
title: Registrazione e diagnostica in gRPC su .NET
author: jamesnk
description: Scopri come raccogliere la diagnostica dall'app gRPC su .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 131144bf7a2c637eb2c1a1d5c54990dd4d429502
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417510"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="206c8-103">Registrazione e diagnostica in gRPC su .NET</span><span class="sxs-lookup"><span data-stu-id="206c8-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="206c8-104">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="206c8-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="206c8-105">Questo articolo fornisce indicazioni per la raccolta di diagnostica da un'app gRPC per facilitare la risoluzione dei problemi.</span><span class="sxs-lookup"><span data-stu-id="206c8-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="206c8-106">Gli argomenti trattati includono:</span><span class="sxs-lookup"><span data-stu-id="206c8-106">Topics covered include:</span></span>

* <span data-ttu-id="206c8-107">**Registrazione:** log strutturati scritti nella [registrazione .NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="206c8-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="206c8-108"><xref:Microsoft.Extensions.Logging.ILogger>viene usato dai framework dell'app per scrivere i log e dagli utenti per la propria registrazione in un'app.</span><span class="sxs-lookup"><span data-stu-id="206c8-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="206c8-109">**Traccia:** eventi correlati a `DiaganosticSource` un'operazione scritta utilizzando e `Activity`.</span><span class="sxs-lookup"><span data-stu-id="206c8-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="206c8-110">Le tracce dall'origine diagnostica vengono comunemente usate per raccogliere dati di telemetria dell'app da librerie quali [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) e [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span><span class="sxs-lookup"><span data-stu-id="206c8-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="206c8-111">**Metriche:** rappresentazione delle misure dei dati in intervalli di tempo, ad esempio richieste al secondo.</span><span class="sxs-lookup"><span data-stu-id="206c8-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="206c8-112">Le metriche `EventCounter` vengono generate utilizzando e possono essere osservate utilizzando lo strumento da riga di comando [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) o con [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span><span class="sxs-lookup"><span data-stu-id="206c8-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="206c8-113">Registrazione</span><span class="sxs-lookup"><span data-stu-id="206c8-113">Logging</span></span>

<span data-ttu-id="206c8-114">servizi gRPC e il client gRPC scrivere i registri utilizzando [la registrazione .NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="206c8-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="206c8-115">I log sono un buon punto di partenza quando è necessario eseguire il debug di un comportamento imprevisto nelle app.</span><span class="sxs-lookup"><span data-stu-id="206c8-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="206c8-116">Registrazione dei servizi gRPC</span><span class="sxs-lookup"><span data-stu-id="206c8-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="206c8-117">I log sul lato server possono contenere informazioni riservate dell'app.</span><span class="sxs-lookup"><span data-stu-id="206c8-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="206c8-118">**Non** pubblicare mai log non elaborati dalle app di produzione ai forum pubblici come GitHub.</span><span class="sxs-lookup"><span data-stu-id="206c8-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="206c8-119">Poiché i servizi gRPC sono ospitati in ASP.NET Core, utilizza il sistema di registrazione ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="206c8-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="206c8-120">Nella configurazione predefinita, gRPC registra pochissime informazioni, ma questo può essere configurato.</span><span class="sxs-lookup"><span data-stu-id="206c8-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="206c8-121">Vedere la documentazione sulla [registrazione ASP.NET Core](xref:fundamentals/logging/index#configuration) per informazioni dettagliate sulla configurazione della registrazione ASP.NET Core.See the documentation on ASP.NET Core logging for details on configuring ASP.NET Core logging.</span><span class="sxs-lookup"><span data-stu-id="206c8-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="206c8-122">gRPC aggiunge i `Grpc` registri nella categoria.</span><span class="sxs-lookup"><span data-stu-id="206c8-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="206c8-123">Per abilitare i log dettagliati `Grpc` da gRPC, configurare i prefissi al `Debug` livello nel `LogLevel` file *appsettings.json* aggiungendo gli elementi seguenti alla sottosezione in: `Logging`</span><span class="sxs-lookup"><span data-stu-id="206c8-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="206c8-124">È inoltre possibile *Startup.cs* configurare `ConfigureLogging`questo in Startup.cs con:</span><span class="sxs-lookup"><span data-stu-id="206c8-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="206c8-125">Se non si usa la configurazione basata su JSON, impostare il valore di configurazione seguente nel sistema di configurazione:If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span><span class="sxs-lookup"><span data-stu-id="206c8-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="206c8-126">Consultare la documentazione del sistema di configurazione per determinare come specificare i valori di configurazione annidati.</span><span class="sxs-lookup"><span data-stu-id="206c8-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="206c8-127">Ad esempio, quando si `_` utilizzano variabili di `:` ambiente, `Logging__LogLevel__Grpc`vengono utilizzati due caratteri anziché il (ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="206c8-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="206c8-128">Ti consigliamo `Debug` di usare il livello durante la raccolta di diagnostica più dettagliata per la tua app.</span><span class="sxs-lookup"><span data-stu-id="206c8-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="206c8-129">Il `Trace` livello produce una diagnostica di basso livello ed è raramente necessario per diagnosticare i problemi nell'app.</span><span class="sxs-lookup"><span data-stu-id="206c8-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="206c8-130">Esempio di output di registrazione</span><span class="sxs-lookup"><span data-stu-id="206c8-130">Sample logging output</span></span>

<span data-ttu-id="206c8-131">Di seguito è riportato `Debug` un esempio di output della console a livello di servizio gRPC:Here is an example of console output at the level of a gRPC service:</span><span class="sxs-lookup"><span data-stu-id="206c8-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a><span data-ttu-id="206c8-132">Accedere ai registri lato server</span><span class="sxs-lookup"><span data-stu-id="206c8-132">Access server-side logs</span></span>

<span data-ttu-id="206c8-133">La modalità di accesso ai log sul lato server dipende dall'ambiente in cui si esegue.</span><span class="sxs-lookup"><span data-stu-id="206c8-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="206c8-134">Come app console</span><span class="sxs-lookup"><span data-stu-id="206c8-134">As a console app</span></span>

<span data-ttu-id="206c8-135">Se si esegue un'app console, il [logger](xref:fundamentals/logging/index#console-provider) di console deve essere abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="206c8-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="206c8-136">I registri gRPC verranno visualizzati nella console.</span><span class="sxs-lookup"><span data-stu-id="206c8-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="206c8-137">Altri ambienti</span><span class="sxs-lookup"><span data-stu-id="206c8-137">Other environments</span></span>

<span data-ttu-id="206c8-138">Se l'app viene distribuita in un altro ambiente (ad esempio, Docker, Kubernetes o Servizio Windows), vedere <xref:fundamentals/logging/index> ulteriori informazioni su come configurare i provider di registrazione adatti all'ambiente.</span><span class="sxs-lookup"><span data-stu-id="206c8-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="206c8-139">Registrazione client gRPC</span><span class="sxs-lookup"><span data-stu-id="206c8-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="206c8-140">I log sul lato client possono contenere informazioni riservate dell'app.</span><span class="sxs-lookup"><span data-stu-id="206c8-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="206c8-141">**Non** pubblicare mai log non elaborati dalle app di produzione ai forum pubblici come GitHub.</span><span class="sxs-lookup"><span data-stu-id="206c8-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="206c8-142">Per ottenere i log dal client .NET, è possibile impostare la `GrpcChannelOptions.LoggerFactory` proprietà quando viene creato il canale del client.</span><span class="sxs-lookup"><span data-stu-id="206c8-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="206c8-143">Se si chiama un servizio gRPC da un'app ASP.NET Core, la factory del logger può essere risolta dall'inserimento delle dipendenze (DI):</span><span class="sxs-lookup"><span data-stu-id="206c8-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="206c8-144">Un modo alternativo per abilitare la registrazione client consiste nell'utilizzare la [factory client gRPC](xref:grpc/clientfactory) per creare il client.</span><span class="sxs-lookup"><span data-stu-id="206c8-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="206c8-145">Un client gRPC registrato con la factory del client e risolto da DI utilizzerà automaticamente la registrazione configurata dell'app.</span><span class="sxs-lookup"><span data-stu-id="206c8-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="206c8-146">Se l'app non usa DI, puoi `ILoggerFactory` creare una nuova istanza con [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span><span class="sxs-lookup"><span data-stu-id="206c8-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="206c8-147">Per accedere a questo metodo aggiungi il pacchetto [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) alla tua app.</span><span class="sxs-lookup"><span data-stu-id="206c8-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="206c8-148">Ambiti di registro client gRPC</span><span class="sxs-lookup"><span data-stu-id="206c8-148">gRPC client log scopes</span></span>

<span data-ttu-id="206c8-149">Il client gRPC aggiunge un [ambito di registrazione](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) ai registri effettuati durante una chiamata gRPC.</span><span class="sxs-lookup"><span data-stu-id="206c8-149">The gRPC client adds a [logging scope](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="206c8-150">L'ambito contiene metadati relativi alla chiamata gRPC:</span><span class="sxs-lookup"><span data-stu-id="206c8-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="206c8-151">**GrpcMethodType** - Il tipo di metodo gRPC.</span><span class="sxs-lookup"><span data-stu-id="206c8-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="206c8-152">I valori possibili `Grpc.Core.MethodType` sono i nomi di enum, ad esempio Unary</span><span class="sxs-lookup"><span data-stu-id="206c8-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="206c8-153">**GrpcUri:** URI relativo del metodo gRPC, ad esempio /greet. Greeter/SayHellos</span><span class="sxs-lookup"><span data-stu-id="206c8-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="206c8-154">Esempio di output di registrazione</span><span class="sxs-lookup"><span data-stu-id="206c8-154">Sample logging output</span></span>

<span data-ttu-id="206c8-155">Di seguito è riportato `Debug` un esempio di output della console a livello di client gRPC:</span><span class="sxs-lookup"><span data-stu-id="206c8-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a><span data-ttu-id="206c8-156">Traccia</span><span class="sxs-lookup"><span data-stu-id="206c8-156">Tracing</span></span>

<span data-ttu-id="206c8-157">I servizi gRPC e il client gRPC forniscono informazioni sulle chiamate gRPC utilizzando [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) e [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span><span class="sxs-lookup"><span data-stu-id="206c8-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="206c8-158">.NET gRPC utilizza un'attività per rappresentare una chiamata gRPC.</span><span class="sxs-lookup"><span data-stu-id="206c8-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="206c8-159">Gli eventi di traccia vengono scritti nell'origine diagnostica all'inizio e all'arresto dell'attività di chiamata gRPC.</span><span class="sxs-lookup"><span data-stu-id="206c8-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="206c8-160">La traccia non acquisisce informazioni su quando i messaggi vengono inviati nel corso della durata delle chiamate di streaming gRPC.</span><span class="sxs-lookup"><span data-stu-id="206c8-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="206c8-161">Traccia del servizio gRPC</span><span class="sxs-lookup"><span data-stu-id="206c8-161">gRPC service tracing</span></span>

<span data-ttu-id="206c8-162">I servizi gRPC sono ospitati in ASP.NET Core che segnala gli eventi relativi alle richieste HTTP in ingresso.</span><span class="sxs-lookup"><span data-stu-id="206c8-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="206c8-163">Metadati specifici gRPC vengono aggiunti alla diagnostica delle richieste HTTP esistente fornita ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="206c8-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="206c8-164">Il nome `Microsoft.AspNetCore`dell'origine diagnostica è .</span><span class="sxs-lookup"><span data-stu-id="206c8-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="206c8-165">Il nome `Microsoft.AspNetCore.Hosting.HttpRequestIn`dell'attività è .</span><span class="sxs-lookup"><span data-stu-id="206c8-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="206c8-166">Il nome del metodo gRPC richiamato dalla chiamata gRPC `grpc.method`viene aggiunto come tag con il nome .</span><span class="sxs-lookup"><span data-stu-id="206c8-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="206c8-167">Il codice di stato della chiamata gRPC al termine `grpc.status_code`viene aggiunto come tag con il nome .</span><span class="sxs-lookup"><span data-stu-id="206c8-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="206c8-168">Traccia client gRPC</span><span class="sxs-lookup"><span data-stu-id="206c8-168">gRPC client tracing</span></span>

<span data-ttu-id="206c8-169">Il client gRPC `HttpClient` .NET utilizza per effettuare chiamate gRPC.</span><span class="sxs-lookup"><span data-stu-id="206c8-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="206c8-170">Sebbene `HttpClient` scriva gli eventi di diagnostica, il client gRPC .NET fornisce un'origine di diagnostica personalizzata, attività ed eventi in modo che sia possibile raccogliere informazioni complete su una chiamata gRPC.</span><span class="sxs-lookup"><span data-stu-id="206c8-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="206c8-171">Il nome `Grpc.Net.Client`dell'origine diagnostica è .</span><span class="sxs-lookup"><span data-stu-id="206c8-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="206c8-172">Il nome `Grpc.Net.Client.GrpcOut`dell'attività è .</span><span class="sxs-lookup"><span data-stu-id="206c8-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="206c8-173">Il nome del metodo gRPC richiamato dalla chiamata gRPC `grpc.method`viene aggiunto come tag con il nome .</span><span class="sxs-lookup"><span data-stu-id="206c8-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="206c8-174">Il codice di stato della chiamata gRPC al termine `grpc.status_code`viene aggiunto come tag con il nome .</span><span class="sxs-lookup"><span data-stu-id="206c8-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="206c8-175">Raccolta dell'analisi</span><span class="sxs-lookup"><span data-stu-id="206c8-175">Collecting tracing</span></span>

<span data-ttu-id="206c8-176">Il modo più `DiagnosticSource` semplice per usare consiste nel configurare una libreria di telemetria, ad esempio [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) o [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) nell'app.</span><span class="sxs-lookup"><span data-stu-id="206c8-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="206c8-177">La libreria elaborerà le informazioni sulle chiamate gRPC lungo altri dati di telemetria dell'app.</span><span class="sxs-lookup"><span data-stu-id="206c8-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="206c8-178">La traccia può essere visualizzata in un servizio gestito come Application Insights oppure è possibile scegliere di eseguire un sistema di traccia distribuito personalizzato.</span><span class="sxs-lookup"><span data-stu-id="206c8-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="206c8-179">OpenTelemetry supporta l'esportazione dei dati di tracciamento in [Jaeger](https://www.jaegertracing.io/) e [.](https://zipkin.io/)</span><span class="sxs-lookup"><span data-stu-id="206c8-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="206c8-180">`DiagnosticSource`può utilizzare gli eventi `DiagnosticListener`di traccia nel codice utilizzando .</span><span class="sxs-lookup"><span data-stu-id="206c8-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="206c8-181">Per informazioni sull'ascolto di un'origine diagnostica con codice, vedere il [Manuale dell'utente di DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span><span class="sxs-lookup"><span data-stu-id="206c8-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="206c8-182">Le librerie di telemetria non acquisiscono attualmente i dati di telemetria specifici `Grpc.Net.Client.GrpcOut` gRPC.</span><span class="sxs-lookup"><span data-stu-id="206c8-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="206c8-183">Sono in corso operazioni per migliorare le librerie di telemetria che acquisiscono questa traccia.</span><span class="sxs-lookup"><span data-stu-id="206c8-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="206c8-184">Metriche</span><span class="sxs-lookup"><span data-stu-id="206c8-184">Metrics</span></span>

<span data-ttu-id="206c8-185">Le metriche sono una rappresentazione delle misure dei dati in intervalli di tempo, ad esempio richieste al secondo.</span><span class="sxs-lookup"><span data-stu-id="206c8-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="206c8-186">I dati delle metriche consentono l'osservazione dello stato di un'app a livello generale.</span><span class="sxs-lookup"><span data-stu-id="206c8-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="206c8-187">Le metriche gRPC .NET vengono generate utilizzando `EventCounter`.</span><span class="sxs-lookup"><span data-stu-id="206c8-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="206c8-188">Metriche del servizio gRPC</span><span class="sxs-lookup"><span data-stu-id="206c8-188">gRPC service metrics</span></span>

<span data-ttu-id="206c8-189">Le metriche del `Grpc.AspNetCore.Server` server gRPC vengono segnalate nell'origine evento.</span><span class="sxs-lookup"><span data-stu-id="206c8-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="206c8-190">Nome</span><span class="sxs-lookup"><span data-stu-id="206c8-190">Name</span></span>                      | <span data-ttu-id="206c8-191">Descrizione</span><span class="sxs-lookup"><span data-stu-id="206c8-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="206c8-192">Totale chiamate</span><span class="sxs-lookup"><span data-stu-id="206c8-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="206c8-193">Chiamate correnti</span><span class="sxs-lookup"><span data-stu-id="206c8-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="206c8-194">Totale chiamate non riuscite</span><span class="sxs-lookup"><span data-stu-id="206c8-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="206c8-195">Termine totale chiamate superato</span><span class="sxs-lookup"><span data-stu-id="206c8-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="206c8-196">Totale messaggi inviati</span><span class="sxs-lookup"><span data-stu-id="206c8-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="206c8-197">Totale messaggi ricevuti</span><span class="sxs-lookup"><span data-stu-id="206c8-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="206c8-198">Totale chiamate non implementate</span><span class="sxs-lookup"><span data-stu-id="206c8-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="206c8-199">ASP.NET Core fornisce anche `Microsoft.AspNetCore.Hosting` le proprie metriche nell'origine evento.</span><span class="sxs-lookup"><span data-stu-id="206c8-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="206c8-200">Metriche client gRPC</span><span class="sxs-lookup"><span data-stu-id="206c8-200">gRPC client metrics</span></span>

<span data-ttu-id="206c8-201">Le metriche client `Grpc.Net.Client` gRPC vengono segnalate nell'origine evento.</span><span class="sxs-lookup"><span data-stu-id="206c8-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="206c8-202">Nome</span><span class="sxs-lookup"><span data-stu-id="206c8-202">Name</span></span>                      | <span data-ttu-id="206c8-203">Descrizione</span><span class="sxs-lookup"><span data-stu-id="206c8-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="206c8-204">Totale chiamate</span><span class="sxs-lookup"><span data-stu-id="206c8-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="206c8-205">Chiamate correnti</span><span class="sxs-lookup"><span data-stu-id="206c8-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="206c8-206">Totale chiamate non riuscite</span><span class="sxs-lookup"><span data-stu-id="206c8-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="206c8-207">Termine totale chiamate superato</span><span class="sxs-lookup"><span data-stu-id="206c8-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="206c8-208">Totale messaggi inviati</span><span class="sxs-lookup"><span data-stu-id="206c8-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="206c8-209">Totale messaggi ricevuti</span><span class="sxs-lookup"><span data-stu-id="206c8-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="206c8-210">Osservare le metriche</span><span class="sxs-lookup"><span data-stu-id="206c8-210">Observe metrics</span></span>

<span data-ttu-id="206c8-211">[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) è uno strumento di monitoraggio delle prestazioni per il monitoraggio dello stato ad hoc e l'analisi delle prestazioni di primo livello.</span><span class="sxs-lookup"><span data-stu-id="206c8-211">[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="206c8-212">Monitorare un'app `Grpc.AspNetCore.Server` .NET con uno o `Grpc.Net.Client` come nome del provider.</span><span class="sxs-lookup"><span data-stu-id="206c8-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

<span data-ttu-id="206c8-213">Un altro modo per osservare le metriche gRPC consiste nell'acquisire i dati dei contatori utilizzando il [pacchetto Microsoft.ApplicationInsights.EventCounterCollector](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="206c8-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="206c8-214">Dopo l'installazione, Application Insights raccoglie i contatori .NET comuni in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="206c8-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="206c8-215">I contatori di gRPC non vengono raccolti per impostazione predefinita, ma App Insights può essere [personalizzato per includere contatori aggiuntivi.](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected)</span><span class="sxs-lookup"><span data-stu-id="206c8-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="206c8-216">Specificare i contatori gRPC per Analisi applicazioni da raccogliere in *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="206c8-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs*:</span></span>

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a><span data-ttu-id="206c8-217">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="206c8-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
