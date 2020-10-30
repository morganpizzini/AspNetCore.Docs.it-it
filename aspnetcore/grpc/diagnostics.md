---
title: Registrazione e diagnostica in gRPC in .NET
author: jamesnk
description: Informazioni su come raccogliere dati diagnostici dall'app gRPC in .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
no-loc:
- appsettings.json
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
uid: grpc/diagnostics
ms.openlocfilehash: 1f25ae76e5a480e5e6f247e4ac78d06dd4e778e9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060443"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>Registrazione e diagnostica in gRPC in .NET

Di [James Newton-King](https://twitter.com/jamesnk)

Questo articolo fornisce indicazioni per la raccolta di dati diagnostici da un'app gRPC per semplificare la risoluzione dei problemi. Gli argomenti trattati includono:

* Log con struttura di **registrazione** scritti nella [registrazione di .NET Core](xref:fundamentals/logging/index). <xref:Microsoft.Extensions.Logging.ILogger> viene usato dai Framework app per scrivere log e dagli utenti per la propria registrazione in un'app.
* **Traccia** : eventi correlati a un'operazione scritta utilizzando `DiaganosticSource` e `Activity` . Le tracce dall'origine di diagnostica vengono in genere usate per raccogliere dati di telemetria delle app da librerie quali [Application Insights](/azure/azure-monitor/app/asp-net-core) e [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).
* **Metriche** : rappresentazione delle misure dei dati in intervalli temporali, ad esempio richieste al secondo. Le metriche vengono emesse usando `EventCounter` e possono essere osservate usando lo strumento da riga di comando [DotNet-contatori](/dotnet/core/diagnostics/dotnet-counters) o con [Application Insights](/azure/azure-monitor/app/eventcounters).

## <a name="logging"></a>Registrazione

i servizi gRPC e il client gRPC scrivono log con la [registrazione di .NET Core](xref:fundamentals/logging/index). I log rappresentano un punto di partenza ideale quando è necessario eseguire il debug di comportamenti imprevisti nelle app.

### <a name="grpc-services-logging"></a>registrazione dei servizi gRPC

> [!WARNING]
> I log lato server possono contenere informazioni riservate dall'app. **Non pubblicare mai log non** elaborati dalle app di produzione nei forum pubblici come github.

Poiché i servizi gRPC sono ospitati in ASP.NET Core, viene usato il sistema di registrazione ASP.NET Core. Nella configurazione predefinita, gRPC registra pochissime informazioni, ma ciò può essere configurato. Per informazioni dettagliate sulla configurazione della registrazione ASP.NET Core, vedere la documentazione relativa alla [registrazione di ASP.NET Core](xref:fundamentals/logging/index#configuration) .

gRPC aggiunge i log nella `Grpc` categoria. Per abilitare i log dettagliati da gRPC, configurare i `Grpc` prefissi per il `Debug` livello nel *appsettings.json* file aggiungendo gli elementi seguenti alla sottosezione `LogLevel` in `Logging` :

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

È anche possibile configurarlo in *Startup.cs* con `ConfigureLogging` :

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

Se non si usa la configurazione basata su JSON, impostare il valore di configurazione seguente nel sistema di configurazione:

* `Logging:LogLevel:Grpc` = `Debug`

Controllare la documentazione del sistema di configurazione per determinare come specificare i valori di configurazione annidati. Quando si usano le variabili di ambiente, ad esempio, `_` vengono usati due caratteri al posto di `:` (ad esempio, `Logging__LogLevel__Grpc` ).

È consigliabile usare il `Debug` livello quando si raccolgono dati di diagnostica più dettagliati per l'app. Il `Trace` livello produce diagnostica di basso livello e raramente è necessario per diagnosticare i problemi nell'app.

#### <a name="sample-logging-output"></a>Esempio di output di registrazione

Di seguito è riportato un esempio di output della console al `Debug` livello di un servizio gRPC:

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

### <a name="access-server-side-logs"></a>Accedere ai log lato server

La modalità di accesso ai log lato server dipende dall'ambiente in cui si esegue.

#### <a name="as-a-console-app"></a>Come app console

Se è in esecuzione un'app console, il [logger della console](xref:fundamentals/logging/index#console) deve essere abilitato per impostazione predefinita. i log gRPC verranno visualizzati nella console di.

#### <a name="other-environments"></a>Altri ambienti

Se l'app viene distribuita in un altro ambiente, ad esempio Docker, Kubernetes o il servizio Windows, vedere <xref:fundamentals/logging/index> per altre informazioni su come configurare i provider di registrazione appropriati per l'ambiente.

### <a name="grpc-client-logging"></a>registrazione client gRPC

> [!WARNING]
> I log lato client possono contenere informazioni riservate dall'app. **Non pubblicare mai log non** elaborati dalle app di produzione nei forum pubblici come github.

Per ottenere i log dal client .NET, è possibile impostare la `GrpcChannelOptions.LoggerFactory` proprietà quando viene creato il canale del client. Se si chiama un servizio gRPC da un'app ASP.NET Core, la factory del logger può essere risolta dall'inserimento delle dipendenze (DI):

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

Un modo alternativo per abilitare la registrazione client consiste nell'usare la [Factory client gRPC](xref:grpc/clientfactory) per creare il client. Un client DI gRPC registrato con la factory client ed è stato risolto da DI, userà automaticamente la registrazione configurata dell'app.

Se l'app non USA DI, è possibile creare una nuova `ILoggerFactory` istanza con [LoggerFactory. Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*). Per accedere a questo metodo, aggiungere il pacchetto [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) all'app.

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>ambiti del log del client di gRPC

Il client gRPC aggiunge un [ambito di registrazione](../fundamentals/logging/index.md#log-scopes) ai log effettuati durante una chiamata gRPC. L'ambito include metadati correlati alla chiamata gRPC:

* **GrpcMethodType** : tipo di metodo gRPC. I valori possibili sono i nomi di `Grpc.Core.MethodType` enum, ad esempio unario
* **GrpcUri** : URI relativo del metodo gRPC, ad esempio/greet. Greeter/SayHellos

#### <a name="sample-logging-output"></a>Esempio di output di registrazione

Di seguito è riportato un esempio di output della console a `Debug` livello di client gRPC:

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

i servizi gRPC e il client gRPC forniscono informazioni sulle chiamate gRPC usando [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) e l' [attività](/dotnet/api/system.diagnostics.activity).

* .NET gRPC usa un'attività per rappresentare una chiamata gRPC.
* Gli eventi di traccia vengono scritti nell'origine di diagnostica all'avvio e all'arresto dell'attività chiamata gRPC.
* La traccia non acquisisce informazioni sul momento in cui i messaggi vengono inviati per la durata delle chiamate di streaming gRPC.

### <a name="grpc-service-tracing"></a>traccia del servizio gRPC

i servizi gRPC sono ospitati in ASP.NET Core che segnala gli eventi relativi alle richieste HTTP in ingresso. i metadati specifici di gRPC vengono aggiunti alla diagnostica della richiesta HTTP esistente fornita da ASP.NET Core.

* Il nome dell'origine di diagnostica è `Microsoft.AspNetCore` .
* Il nome dell'attività è `Microsoft.AspNetCore.Hosting.HttpRequestIn` .
  * Il nome del metodo gRPC richiamato dalla chiamata gRPC viene aggiunto come tag con il nome `grpc.method` .
  * Il codice di stato della chiamata gRPC quando viene completato viene aggiunto come tag con il nome `grpc.status_code` .

### <a name="grpc-client-tracing"></a>traccia del client gRPC

Il client gRPC .NET usa `HttpClient` per eseguire chiamate gRPC. Sebbene `HttpClient` scriva eventi di diagnostica, il client gRPC .NET fornisce un'origine di diagnostica personalizzata, attività ed eventi in modo che sia possibile raccogliere informazioni complete su una chiamata gRPC.

* Il nome dell'origine di diagnostica è `Grpc.Net.Client` .
* Il nome dell'attività è `Grpc.Net.Client.GrpcOut` .
  * Il nome del metodo gRPC richiamato dalla chiamata gRPC viene aggiunto come tag con il nome `grpc.method` .
  * Il codice di stato della chiamata gRPC quando viene completato viene aggiunto come tag con il nome `grpc.status_code` .

### <a name="collecting-tracing"></a>Raccolta della traccia

Il modo più semplice per usare `DiagnosticSource` consiste nel configurare una libreria di telemetria, ad esempio [Application Insights](/azure/azure-monitor/app/asp-net-core) o [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) nell'app. La libreria elaborerà le informazioni sulle chiamate gRPC lungo il lato di altri dati di telemetria dell'app.

La traccia può essere visualizzata in un servizio gestito, ad esempio Application Insights, oppure è possibile scegliere di eseguire il proprio sistema di traccia distribuita. OpenTelemetry supporta l'esportazione dei dati di traccia in [Jaeger](https://www.jaegertracing.io/) e [Zipkin](https://zipkin.io/).

`DiagnosticSource` può utilizzare gli eventi di traccia nel codice utilizzando `DiagnosticListener` . Per informazioni sull'ascolto di un'origine diagnostica con codice, vedere il [manuale dell'utente di DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).

> [!NOTE]
> Attualmente le librerie di telemetria non acquisiscono dati di `Grpc.Net.Client.GrpcOut` telemetria specifici per gRPC. Il lavoro per migliorare le librerie di telemetrie che acquisiscono questa traccia è in corso.

## <a name="metrics"></a>Metriche

Metrica è una rappresentazione delle misure dei dati in intervalli di tempo, ad esempio richieste al secondo. I dati di metrica consentono di osservare lo stato di un'app a un livello elevato. Le metriche gRPC di .NET vengono emesse usando `EventCounter` .

### <a name="grpc-service-metrics"></a>metriche del servizio gRPC

le metriche del server gRPC sono segnalate nell' `Grpc.AspNetCore.Server` origine evento.

| Nome                      | Descrizione                   |
| --------------------------|-------------------------------|
| `total-calls`             | Totale chiamate                   |
| `current-calls`           | Chiamate correnti                 |
| `calls-failed`            | Totale chiamate non riuscite            |
| `calls-deadline-exceeded` | Scadenza totale chiamate superata |
| `messages-sent`           | Totale messaggi inviati           |
| `messages-received`       | Totale messaggi ricevuti       |
| `calls-unimplemented`     | Totale chiamate non implementate     |

ASP.NET Core fornisce anche le proprie metriche nell' `Microsoft.AspNetCore.Hosting` origine evento.

### <a name="grpc-client-metrics"></a>metriche client gRPC

le metriche client di gRPC sono segnalate nell' `Grpc.Net.Client` origine evento.

| Nome                      | Descrizione                   |
| --------------------------|-------------------------------|
| `total-calls`             | Totale chiamate                   |
| `current-calls`           | Chiamate correnti                 |
| `calls-failed`            | Totale chiamate non riuscite            |
| `calls-deadline-exceeded` | Scadenza totale chiamate superata |
| `messages-sent`           | Totale messaggi inviati           |
| `messages-received`       | Totale messaggi ricevuti       |

### <a name="observe-metrics"></a>Osservare le metriche

[DotNet-Counters](/dotnet/core/diagnostics/dotnet-counters) è uno strumento di monitoraggio delle prestazioni per il monitoraggio dell'integrità ad hoc e l'analisi delle prestazioni di primo livello. Monitorare un'app .NET con `Grpc.AspNetCore.Server` o `Grpc.Net.Client` come nome del provider.

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

Un altro modo per osservare le metriche di gRPC consiste nell'acquisire i dati del contatore usando il [pacchetto Microsoft. ApplicationInsights. EventCounterCollector](/azure/azure-monitor/app/eventcounters)di Application Insights. Al termine dell'installazione, Application Insights raccoglie i contatori .NET comuni in fase di esecuzione. i contatori di gRPC non vengono raccolti per impostazione predefinita, ma è possibile personalizzare app Insights [per includere altri contatori](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).

Specificare i contatori gRPC per Application Insights da raccogliere in *Startup.cs* :

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