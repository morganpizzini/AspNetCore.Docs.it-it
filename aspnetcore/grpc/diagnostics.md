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
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>Registrazione e diagnostica in gRPC su .NET

Di [James Newton-King](https://twitter.com/jamesnk)

Questo articolo fornisce indicazioni per la raccolta di diagnostica da un'app gRPC per facilitare la risoluzione dei problemi. Gli argomenti trattati includono:

* **Registrazione:** log strutturati scritti nella [registrazione .NET Core](xref:fundamentals/logging/index). <xref:Microsoft.Extensions.Logging.ILogger>viene usato dai framework dell'app per scrivere i log e dagli utenti per la propria registrazione in un'app.
* **Traccia:** eventi correlati a `DiaganosticSource` un'operazione scritta utilizzando e `Activity`. Le tracce dall'origine diagnostica vengono comunemente usate per raccogliere dati di telemetria dell'app da librerie quali [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) e [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).
* **Metriche:** rappresentazione delle misure dei dati in intervalli di tempo, ad esempio richieste al secondo. Le metriche `EventCounter` vengono generate utilizzando e possono essere osservate utilizzando lo strumento da riga di comando [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) o con [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).

## <a name="logging"></a>Registrazione

servizi gRPC e il client gRPC scrivere i registri utilizzando [la registrazione .NET Core](xref:fundamentals/logging/index). I log sono un buon punto di partenza quando è necessario eseguire il debug di un comportamento imprevisto nelle app.

### <a name="grpc-services-logging"></a>Registrazione dei servizi gRPC

> [!WARNING]
> I log sul lato server possono contenere informazioni riservate dell'app. **Non** pubblicare mai log non elaborati dalle app di produzione ai forum pubblici come GitHub.

Poiché i servizi gRPC sono ospitati in ASP.NET Core, utilizza il sistema di registrazione ASP.NET Core. Nella configurazione predefinita, gRPC registra pochissime informazioni, ma questo può essere configurato. Vedere la documentazione sulla [registrazione ASP.NET Core](xref:fundamentals/logging/index#configuration) per informazioni dettagliate sulla configurazione della registrazione ASP.NET Core.See the documentation on ASP.NET Core logging for details on configuring ASP.NET Core logging.

gRPC aggiunge i `Grpc` registri nella categoria. Per abilitare i log dettagliati `Grpc` da gRPC, configurare i prefissi al `Debug` livello nel `LogLevel` file *appsettings.json* aggiungendo gli elementi seguenti alla sottosezione in: `Logging`

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

È inoltre possibile *Startup.cs* configurare `ConfigureLogging`questo in Startup.cs con:

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

Se non si usa la configurazione basata su JSON, impostare il valore di configurazione seguente nel sistema di configurazione:If you aren't using JSON-based configuration, set the following configuration value in your configuration system:

* `Logging:LogLevel:Grpc` = `Debug`

Consultare la documentazione del sistema di configurazione per determinare come specificare i valori di configurazione annidati. Ad esempio, quando si `_` utilizzano variabili di `:` ambiente, `Logging__LogLevel__Grpc`vengono utilizzati due caratteri anziché il (ad esempio, ).

Ti consigliamo `Debug` di usare il livello durante la raccolta di diagnostica più dettagliata per la tua app. Il `Trace` livello produce una diagnostica di basso livello ed è raramente necessario per diagnosticare i problemi nell'app.

#### <a name="sample-logging-output"></a>Esempio di output di registrazione

Di seguito è riportato `Debug` un esempio di output della console a livello di servizio gRPC:Here is an example of console output at the level of a gRPC service:

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

### <a name="access-server-side-logs"></a>Accedere ai registri lato server

La modalità di accesso ai log sul lato server dipende dall'ambiente in cui si esegue.

#### <a name="as-a-console-app"></a>Come app console

Se si esegue un'app console, il [logger](xref:fundamentals/logging/index#console-provider) di console deve essere abilitato per impostazione predefinita. I registri gRPC verranno visualizzati nella console.

#### <a name="other-environments"></a>Altri ambienti

Se l'app viene distribuita in un altro ambiente (ad esempio, Docker, Kubernetes o Servizio Windows), vedere <xref:fundamentals/logging/index> ulteriori informazioni su come configurare i provider di registrazione adatti all'ambiente.

### <a name="grpc-client-logging"></a>Registrazione client gRPC

> [!WARNING]
> I log sul lato client possono contenere informazioni riservate dell'app. **Non** pubblicare mai log non elaborati dalle app di produzione ai forum pubblici come GitHub.

Per ottenere i log dal client .NET, è possibile impostare la `GrpcChannelOptions.LoggerFactory` proprietà quando viene creato il canale del client. Se si chiama un servizio gRPC da un'app ASP.NET Core, la factory del logger può essere risolta dall'inserimento delle dipendenze (DI):

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

Un modo alternativo per abilitare la registrazione client consiste nell'utilizzare la [factory client gRPC](xref:grpc/clientfactory) per creare il client. Un client gRPC registrato con la factory del client e risolto da DI utilizzerà automaticamente la registrazione configurata dell'app.

Se l'app non usa DI, puoi `ILoggerFactory` creare una nuova istanza con [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*). Per accedere a questo metodo aggiungi il pacchetto [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) alla tua app.

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>Ambiti di registro client gRPC

Il client gRPC aggiunge un [ambito di registrazione](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) ai registri effettuati durante una chiamata gRPC. L'ambito contiene metadati relativi alla chiamata gRPC:

* **GrpcMethodType** - Il tipo di metodo gRPC. I valori possibili `Grpc.Core.MethodType` sono i nomi di enum, ad esempio Unary
* **GrpcUri:** URI relativo del metodo gRPC, ad esempio /greet. Greeter/SayHellos

#### <a name="sample-logging-output"></a>Esempio di output di registrazione

Di seguito è riportato `Debug` un esempio di output della console a livello di client gRPC:

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

## <a name="tracing"></a>Traccia

I servizi gRPC e il client gRPC forniscono informazioni sulle chiamate gRPC utilizzando [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) e [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).

* .NET gRPC utilizza un'attività per rappresentare una chiamata gRPC.
* Gli eventi di traccia vengono scritti nell'origine diagnostica all'inizio e all'arresto dell'attività di chiamata gRPC.
* La traccia non acquisisce informazioni su quando i messaggi vengono inviati nel corso della durata delle chiamate di streaming gRPC.

### <a name="grpc-service-tracing"></a>Traccia del servizio gRPC

I servizi gRPC sono ospitati in ASP.NET Core che segnala gli eventi relativi alle richieste HTTP in ingresso. Metadati specifici gRPC vengono aggiunti alla diagnostica delle richieste HTTP esistente fornita ASP.NET Core.

* Il nome `Microsoft.AspNetCore`dell'origine diagnostica è .
* Il nome `Microsoft.AspNetCore.Hosting.HttpRequestIn`dell'attività è .
  * Il nome del metodo gRPC richiamato dalla chiamata gRPC `grpc.method`viene aggiunto come tag con il nome .
  * Il codice di stato della chiamata gRPC al termine `grpc.status_code`viene aggiunto come tag con il nome .

### <a name="grpc-client-tracing"></a>Traccia client gRPC

Il client gRPC `HttpClient` .NET utilizza per effettuare chiamate gRPC. Sebbene `HttpClient` scriva gli eventi di diagnostica, il client gRPC .NET fornisce un'origine di diagnostica personalizzata, attività ed eventi in modo che sia possibile raccogliere informazioni complete su una chiamata gRPC.

* Il nome `Grpc.Net.Client`dell'origine diagnostica è .
* Il nome `Grpc.Net.Client.GrpcOut`dell'attività è .
  * Il nome del metodo gRPC richiamato dalla chiamata gRPC `grpc.method`viene aggiunto come tag con il nome .
  * Il codice di stato della chiamata gRPC al termine `grpc.status_code`viene aggiunto come tag con il nome .

### <a name="collecting-tracing"></a>Raccolta dell'analisi

Il modo più `DiagnosticSource` semplice per usare consiste nel configurare una libreria di telemetria, ad esempio [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) o [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) nell'app. La libreria elaborerà le informazioni sulle chiamate gRPC lungo altri dati di telemetria dell'app.

La traccia può essere visualizzata in un servizio gestito come Application Insights oppure è possibile scegliere di eseguire un sistema di traccia distribuito personalizzato. OpenTelemetry supporta l'esportazione dei dati di tracciamento in [Jaeger](https://www.jaegertracing.io/) e [.](https://zipkin.io/)

`DiagnosticSource`può utilizzare gli eventi `DiagnosticListener`di traccia nel codice utilizzando . Per informazioni sull'ascolto di un'origine diagnostica con codice, vedere il [Manuale dell'utente di DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).

> [!NOTE]
> Le librerie di telemetria non acquisiscono attualmente i dati di telemetria specifici `Grpc.Net.Client.GrpcOut` gRPC. Sono in corso operazioni per migliorare le librerie di telemetria che acquisiscono questa traccia.

## <a name="metrics"></a>Metriche

Le metriche sono una rappresentazione delle misure dei dati in intervalli di tempo, ad esempio richieste al secondo. I dati delle metriche consentono l'osservazione dello stato di un'app a livello generale. Le metriche gRPC .NET vengono generate utilizzando `EventCounter`.

### <a name="grpc-service-metrics"></a>Metriche del servizio gRPC

Le metriche del `Grpc.AspNetCore.Server` server gRPC vengono segnalate nell'origine evento.

| Nome                      | Descrizione                   |
| --------------------------|-------------------------------|
| `total-calls`             | Totale chiamate                   |
| `current-calls`           | Chiamate correnti                 |
| `calls-failed`            | Totale chiamate non riuscite            |
| `calls-deadline-exceeded` | Termine totale chiamate superato |
| `messages-sent`           | Totale messaggi inviati           |
| `messages-received`       | Totale messaggi ricevuti       |
| `calls-unimplemented`     | Totale chiamate non implementate     |

ASP.NET Core fornisce anche `Microsoft.AspNetCore.Hosting` le proprie metriche nell'origine evento.

### <a name="grpc-client-metrics"></a>Metriche client gRPC

Le metriche client `Grpc.Net.Client` gRPC vengono segnalate nell'origine evento.

| Nome                      | Descrizione                   |
| --------------------------|-------------------------------|
| `total-calls`             | Totale chiamate                   |
| `current-calls`           | Chiamate correnti                 |
| `calls-failed`            | Totale chiamate non riuscite            |
| `calls-deadline-exceeded` | Termine totale chiamate superato |
| `messages-sent`           | Totale messaggi inviati           |
| `messages-received`       | Totale messaggi ricevuti       |

### <a name="observe-metrics"></a>Osservare le metriche

[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) è uno strumento di monitoraggio delle prestazioni per il monitoraggio dello stato ad hoc e l'analisi delle prestazioni di primo livello. Monitorare un'app `Grpc.AspNetCore.Server` .NET con uno o `Grpc.Net.Client` come nome del provider.

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

Un altro modo per osservare le metriche gRPC consiste nell'acquisire i dati dei contatori utilizzando il [pacchetto Microsoft.ApplicationInsights.EventCounterCollector](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)di Application Insights. Dopo l'installazione, Application Insights raccoglie i contatori .NET comuni in fase di esecuzione. I contatori di gRPC non vengono raccolti per impostazione predefinita, ma App Insights può essere [personalizzato per includere contatori aggiuntivi.](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected)

Specificare i contatori gRPC per Analisi applicazioni da raccogliere in *Startup.cs:*

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

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
