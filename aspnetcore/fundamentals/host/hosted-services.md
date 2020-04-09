---
title: Attività in background con servizi ospitati in ASP.NET Core
author: rick-anderson
description: Informazioni su come implementare attività in background con servizi ospitati in ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/host/hosted-services
ms.openlocfilehash: d3f409170eedd281fd7608c4b9835bf9443c49b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666201"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>Attività in background con servizi ospitati in ASP.NET Core

Di [Jeow Li Huan](https://github.com/huan086)

::: moniker range=">= aspnetcore-3.0"

In ASP.NET Core le attività in background possono essere implementate come *servizi ospitati*. Un servizio ospitato è una classe con logica di attività in background che implementa l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostedService>. In questo argomento vengono forniti tre esempi di servizio ospitato:

* Attività in background eseguita su un timer.
* Servizio ospitato che attiva un [servizio con ambito.](xref:fundamentals/dependency-injection#service-lifetimes) Il servizio con ambito può utilizzare [l'inserimento delle dipendenze (DI)](xref:fundamentals/dependency-injection).
* Attività in background in coda che vengono eseguite in sequenza.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="worker-service-template"></a>Modello di servizio di ruolo di lavoro

Il modello di servizio di ruolo di lavoro di ASP.NET Core rappresenta un punto di partenza per la scrittura di app di servizi a esecuzione prolungata. Un'app creata dal modello Servizio di lavoro specifica l'SDK di lavoro nel file di progetto:An app created from the Worker Service template specifies the Worker SDK in its project file:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

Per usare il modello come base per un'app di servizi ospitati:

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a>Pacchetto

Un'app basata sul modello `Microsoft.NET.Sdk.Worker` del servizio di lavoro usa l'SDK e dispone di un riferimento esplicito al pacchetto [Microsoft.Extensions.Hosting.An](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) app based on the Worker Service template uses the SDK and has an explicit package reference to the Microsoft.Extensions.Hosting package. Ad esempio, vedere il file di progetto dell'app di esempio (*BackgroundTasksSample.csproj*).

Per le app `Microsoft.NET.Sdk.Web` Web che usano l'SDK, al pacchetto [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) viene fatto riferimento in modo implicito dal framework condiviso. Non è necessario un riferimento esplicito al pacchetto nel file di progetto dell'app.

## <a name="ihostedservice-interface"></a>Interfaccia IHostedService

L'interfaccia <xref:Microsoft.Extensions.Hosting.IHostedService> definisce due metodi per gli oggetti gestiti dall'host:

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contiene la logica per avviare l'attività in background. `StartAsync`viene chiamato *prima*di :

  * La pipeline di elaborazione delle`Startup.Configure`richieste dell'app è configurata ( ).
  * Il server viene avviato e viene attivato [IApplicationLifetime.ApplicationStarted.](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*)

  Il comportamento predefinito può essere modificato in `StartAsync` modo che il servizio ospitato `ApplicationStarted` venga eseguito dopo che la pipeline dell'app è stata configurata e viene chiamata. Per modificare il comportamento predefinito, aggiungere`VideosWatcher` il servizio ospitato `ConfigureWebHostDefaults`(nell'esempio seguente) dopo la chiamata:

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

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
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Attivato quando l'host sta eseguendo un arresto normale. `StopAsync` contiene la logica per terminare l'attività in background. Implementare <xref:System.IDisposable> e i [finalizzatori (distruttori)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) per eliminare tutte le risorse non gestite.

  Il token di annullamento ha un timeout predefinito di cinque secondi che indica che il processo di arresto non è più normale. Quando viene richiesto l'annullamento sul token:

  * Tutte le operazioni in background rimanenti che sta eseguendo l'app devono essere interrotte.
  * Tutti i metodi eventuali chiamati in `StopAsync` devono essere completati rapidamente.

  Tuttavia, dopo la richiesta di annullamento le attività non vengono abbandonate: il chiamante attende il completamento di tutte le attività.

  Se l'app si arresta in modo imprevisto, ad esempio, il processo dell'app ha esito negativo, il metodo `StopAsync` potrebbe non essere chiamato. Pertanto è possibile che i metodi chiamati o le operazioni effettuate in `StopAsync` non vengano eseguiti.

  Per estendere il timeout di arresto predefinito di cinque secondi, impostare:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> quando si usa l'host generico. Per altre informazioni, vedere <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Impostazione di configurazione dell'host del timeout di arresto quando si usa l'host Web. Per altre informazioni, vedere <xref:fundamentals/host/web-host#shutdown-timeout>.

Il servizio ospitato viene attivato una volta all'avvio dell'app e arrestato normalmente all'arresto dell'applicazione. Se viene generato un errore durante l'esecuzione dell'attività in background, deve essere chiamato `Dispose` anche se `StopAsync` non viene chiamato.

## <a name="backgroundservice-base-class"></a>Classe base BackgroundService

<xref:Microsoft.Extensions.Hosting.BackgroundService>è una classe base per <xref:Microsoft.Extensions.Hosting.IHostedService>l'implementazione di un oggetto .

[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) viene chiamato per eseguire il servizio in background. L'implementazione <xref:System.Threading.Tasks.Task> restituisce un che rappresenta l'intera durata del servizio in background. Non vengono avviati altri servizi finché [ExecuteAsync non diventa asincrono,](https://github.com/dotnet/extensions/issues/2149)ad esempio chiamando `await`. Evitare di eseguire lunghe `ExecuteAsync`operazioni di inizializzazione di blocco in . I blocchi host in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) in attesa `ExecuteAsync` del completamento.

Il token di annullamento viene attivato quando viene chiamato [IHostedService.StopAsync.The](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) cancellation token is triggered when IHostedService.StopAsync is called. L'implementazione di deve terminare prontamente quando viene generato il token di `ExecuteAsync` annullamento per arrestare normalmente il servizio. In caso contrario, il servizio viene arrestato in modo ingrato al timeout dell'arresto. Per altre informazioni, vedere la sezione [relativa all'interfaccia IHostedService.For](#ihostedservice-interface) more information, see the IHostedService interface section.

## <a name="timed-background-tasks"></a>Attività in background programmate

Un'attività programmata in background utilizza la classe [System.Threading.Timer](xref:System.Threading.Timer). Il timer attiva il metodo `DoWork` dell'attività. Il timer viene disabilitato con `StopAsync` ed eliminato quando il contenitore dei servizi è eliminato con `Dispose`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

Il <xref:System.Threading.Timer> non attende per le `DoWork` esecuzioni precedenti di completare, pertanto l'approccio illustrato potrebbe non essere adatto per ogni scenario. [Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) viene utilizzato per incrementare il contatore di esecuzione come un'operazione atomica, che assicura che più thread non vengano aggiornati `executionCount` contemporaneamente.

Il servizio viene `IHostBuilder.ConfigureServices` registrato in `AddHostedService` (*Program.cs*) con il metodo di estensione:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Utilizzo di un servizio con ambito in un'attività in background

Per utilizzare [i servizi con ambito](xref:fundamentals/dependency-injection#service-lifetimes) all'interno di un [BackgroundService](#backgroundservice-base-class), creare un ambito. Non viene creato automaticamente alcun ambito per un servizio ospitato.

Il servizio dell'attività in background con ambito contiene la logica dell'attività in background. Nell'esempio seguente:

* Il servizio è asincrono. Il metodo `DoWork` restituisce un elemento `Task`. A scopo dimostrativo, si attende un `DoWork` ritardo di dieci secondi nel metodo.
* Un <xref:Microsoft.Extensions.Logging.ILogger> viene iniettato nel servizio.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Il servizio ospitato crea un ambito per risolvere il `DoWork` servizio attività in background con ambito per chiamare il relativo metodo. `DoWork`restituisce `Task`un oggetto , `ExecuteAsync`atteso in:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

I servizi sono `IHostBuilder.ConfigureServices` registrati in (*Program.cs*). Il servizio ospitato viene `AddHostedService` registrato con il metodo di estensione:The hosted service is registered with the extension method:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Attività in background in coda

Una coda di attività in background è <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> basata su .NET 4.x ([provvisoriamente pianificato per essere incorporato per ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

Nell'esempio `QueueHostedService` seguente:

* Il `BackgroundProcessing` metodo `Task`restituisce un oggetto `ExecuteAsync`, atteso in .
* Le attività in background nella coda vengono `BackgroundProcessing`rimosse dalla coda ed eseguite in .
* Gli elementi di lavoro sono `StopAsync`attesi prima dell'arresto del servizio in .

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

Un `MonitorLoop` servizio gestisce l'inserimento delle attività `w` per il servizio ospitato ogni volta che la chiave viene selezionata in un dispositivo di input:A service handles enqueuing tasks for the hosted service whenever the key is selected on an input device:

* Il `IBackgroundTaskQueue` viene iniettato nel `MonitorLoop` servizio.
* `IBackgroundTaskQueue.QueueBackgroundWorkItem`viene chiamato per accodare un elemento di lavoro.
* L'elemento di lavoro simula un'attività in background a esecuzione prolungata:The work item simulates a long-running background task:
  * Vengono eseguiti tre ritardi`Task.Delay`di 5 secondi ( ).
  * Un'istruzione `try-catch` <xref:System.OperationCanceledException> intercetta se l'attività viene annullata.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

I servizi sono `IHostBuilder.ConfigureServices` registrati in (*Program.cs*). Il servizio ospitato viene `AddHostedService` registrato con il metodo di estensione:The hosted service is registered with the extension method:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

`MontiorLoop`viene avviato in `Program.Main`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In ASP.NET Core le attività in background possono essere implementate come *servizi ospitati*. Un servizio ospitato è una classe con logica di attività in background che implementa l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostedService>. In questo argomento vengono forniti tre esempi di servizio ospitato:

* Attività in background eseguita su un timer.
* Servizio ospitato che attiva un [servizio con ambito.](xref:fundamentals/dependency-injection#service-lifetimes) Il servizio con ambito può usare l'inserimento delle [dipendenze (DI)The](xref:fundamentals/dependency-injection) scoped service can use dependency injection (DI)
* Attività in background in coda che vengono eseguite in sequenza.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="package"></a>Pacchetto

Fare riferimento al [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) oppure aggiungere un riferimento al pacchetto [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).

## <a name="ihostedservice-interface"></a>Interfaccia IHostedService

I servizi ospitati implementano l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostedService>. L'interfaccia definisce due metodi riservati agli oggetti gestiti dall'host:

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contiene la logica per avviare l'attività in background. Quando si utilizza `StartAsync` [l'host Web](xref:fundamentals/host/web-host), viene chiamato dopo l'avvio del server e l'attivazione di [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) . Quando si utilizza `StartAsync` l'host `ApplicationStarted` [generico](xref:fundamentals/host/generic-host), viene chiamato prima di essere attivato.

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Attivato quando l'host sta eseguendo un arresto normale. `StopAsync` contiene la logica per terminare l'attività in background. Implementare <xref:System.IDisposable> e i [finalizzatori (distruttori)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) per eliminare tutte le risorse non gestite.

  Il token di annullamento ha un timeout predefinito di cinque secondi che indica che il processo di arresto non è più normale. Quando viene richiesto l'annullamento sul token:

  * Tutte le operazioni in background rimanenti che sta eseguendo l'app devono essere interrotte.
  * Tutti i metodi eventuali chiamati in `StopAsync` devono essere completati rapidamente.

  Tuttavia, dopo la richiesta di annullamento le attività non vengono abbandonate: il chiamante attende il completamento di tutte le attività.

  Se l'app si arresta in modo imprevisto, ad esempio, il processo dell'app ha esito negativo, il metodo `StopAsync` potrebbe non essere chiamato. Pertanto è possibile che i metodi chiamati o le operazioni effettuate in `StopAsync` non vengano eseguiti.

  Per estendere il timeout di arresto predefinito di cinque secondi, impostare:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> quando si usa l'host generico. Per altre informazioni, vedere <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Impostazione di configurazione dell'host del timeout di arresto quando si usa l'host Web. Per altre informazioni, vedere <xref:fundamentals/host/web-host#shutdown-timeout>.

Il servizio ospitato viene attivato una volta all'avvio dell'app e arrestato normalmente all'arresto dell'applicazione. Se viene generato un errore durante l'esecuzione dell'attività in background, deve essere chiamato `Dispose` anche se `StopAsync` non viene chiamato.

## <a name="timed-background-tasks"></a>Attività in background programmate

Un'attività programmata in background utilizza la classe [System.Threading.Timer](xref:System.Threading.Timer). Il timer attiva il metodo `DoWork` dell'attività. Il timer viene disabilitato con `StopAsync` ed eliminato quando il contenitore dei servizi è eliminato con `Dispose`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

Il <xref:System.Threading.Timer> non attende per le `DoWork` esecuzioni precedenti di completare, pertanto l'approccio illustrato potrebbe non essere adatto per ogni scenario.

Il servizio registrato in `Startup.ConfigureServices` con il metodo di estensione `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Utilizzo di un servizio con ambito in un'attività in background

Per utilizzare i servizi `IHostedService`con [ambito](xref:fundamentals/dependency-injection#service-lifetimes) all'interno di un oggetto , creare un ambito. Non viene creato automaticamente alcun ambito per un servizio ospitato.

Il servizio dell'attività in background con ambito contiene la logica dell'attività in background. Nell'esempio seguente, <xref:Microsoft.Extensions.Logging.ILogger> viene inserito nel servizio:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Il servizio ospitato crea un ambito per risolvere il servizio dell'attività in background con ambito e chiamare il relativo metodo `DoWork`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

I servizi vengono registrati in `Startup.ConfigureServices`. L'implementazione di `IHostedService`viene registrata con il metodo di estensione `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Attività in background in coda

Una coda di attività in background è <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> basata su .NET Framework 4.x ([programmato provvisoriamente per essere incorporato per ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

In `QueueHostedService`, le attività in background in coda vengono rimosse dalla coda ed eseguite come [BackgroundService](#backgroundservice-base-class), ovvero una classe di base per l'implementazione di un `IHostedService` a esecuzione prolungata:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

I servizi vengono registrati in `Startup.ConfigureServices`. L'implementazione di `IHostedService`viene registrata con il metodo di estensione `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

Nella classe modello della pagina di indice:

* `IBackgroundTaskQueue` viene inserito nel costruttore e assegnato a `Queue`.
* Un'interfaccia <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> viene inserita e assegnata a `_serviceScopeFactory`. La factory viene usata per creare istanze di <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, che consente di creare servizi all'interno di un ambito. Viene creato un ambito per usare la classe `AppDbContext` dell'app (un [servizio con ambito](xref:fundamentals/dependency-injection#service-lifetimes)) per scrivere record di database in `IBackgroundTaskQueue` (un servizio singleton).

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

Quando si seleziona il pulsante **Aggiungi attività** nella pagina di indice, viene eseguito il metodo `OnPostAddTask`. `QueueBackgroundWorkItem`viene chiamato per accodare un elemento di lavoro:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a>Risorse aggiuntive

* [Implementare attività in background in microservizi con IHostedService e la classe BackgroundService](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [Eseguire attività in background con WebJobs nel servizio app di AzureRun background tasks with WebJobs in Azure App Service](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
