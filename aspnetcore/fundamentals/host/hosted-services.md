---
<span data-ttu-id="f3a9b-101">title: attività in background con servizi ospitati in ASP.NET Core autore: Rick-Anderson Description: informazioni su come implementare attività in background con servizi ospitati in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-101">title: Background tasks with hosted services in ASP.NET Core author: rick-anderson description: Learn how to implement background tasks with hosted services in ASP.NET Core.</span></span>
<span data-ttu-id="f3a9b-102">monikerRange:' >= aspnetcore-2,1' ms. Author: Riande ms. Custom: MVC ms. Date: 02/10/2020 no-loc:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-102">monikerRange: '>= aspnetcore-2.1' ms.author: riande ms.custom: mvc ms.date: 02/10/2020 no-loc:</span></span>
- <span data-ttu-id="f3a9b-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3a9b-103">'Blazor'</span></span>
- <span data-ttu-id="f3a9b-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3a9b-104">'Identity'</span></span>
- <span data-ttu-id="f3a9b-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3a9b-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3a9b-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3a9b-106">'Razor'</span></span>
- <span data-ttu-id="f3a9b-107">' SignalR ' UID: Nozioni fondamentali/host/Hosted-Services</span><span class="sxs-lookup"><span data-stu-id="f3a9b-107">'SignalR' uid: fundamentals/host/hosted-services</span></span>

---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="f3a9b-108">Attività in background con servizi ospitati in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3a9b-108">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="f3a9b-109">Di [Jeow li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="f3a9b-109">By [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f3a9b-110">In ASP.NET Core le attività in background possono essere implementate come *servizi ospitati*.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-110">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="f3a9b-111">Un servizio ospitato è una classe con logica di attività in background che implementa l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-111">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="f3a9b-112">In questo argomento vengono forniti tre esempi di servizio ospitato:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-112">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="f3a9b-113">Attività in background eseguita su un timer.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-113">Background task that runs on a timer.</span></span>
* <span data-ttu-id="f3a9b-114">Servizio ospitato che attiva un [servizio con ambito](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="f3a9b-114">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="f3a9b-115">Il servizio con ambito può usare l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f3a9b-115">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="f3a9b-116">Attività in background in coda che vengono eseguite in sequenza.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-116">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="f3a9b-117">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f3a9b-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="f3a9b-118">Modello di servizio di ruolo di lavoro</span><span class="sxs-lookup"><span data-stu-id="f3a9b-118">Worker Service template</span></span>

<span data-ttu-id="f3a9b-119">Il modello di servizio di ruolo di lavoro di ASP.NET Core rappresenta un punto di partenza per la scrittura di app di servizi a esecuzione prolungata.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-119">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="f3a9b-120">Un'app creata dal modello del servizio Worker specifica l'SDK del ruolo di lavoro nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-120">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="f3a9b-121">Per usare il modello come base per un'app di servizi ospitati:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-121">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="f3a9b-122">Pacchetto</span><span class="sxs-lookup"><span data-stu-id="f3a9b-122">Package</span></span>

<span data-ttu-id="f3a9b-123">Un'app basata sul modello del servizio Worker usa l' `Microsoft.NET.Sdk.Worker` SDK e contiene un riferimento esplicito al pacchetto per il pacchetto [Microsoft. Extensions. host](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="f3a9b-123">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="f3a9b-124">Ad esempio, vedere il file di progetto dell'app di esempio (*BackgroundTasksSample. csproj*).</span><span class="sxs-lookup"><span data-stu-id="f3a9b-124">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="f3a9b-125">Per le app Web che usano l' `Microsoft.NET.Sdk.Web` SDK, viene fatto riferimento in modo implicito al pacchetto [Microsoft. Extensions. host](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) dal Framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-125">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="f3a9b-126">Un riferimento esplicito al pacchetto nel file di progetto dell'app non è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-126">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="f3a9b-127">Interfaccia IHostedService</span><span class="sxs-lookup"><span data-stu-id="f3a9b-127">IHostedService interface</span></span>

<span data-ttu-id="f3a9b-128">L' <xref:Microsoft.Extensions.Hosting.IHostedService> interfaccia definisce due metodi per gli oggetti gestiti dall'host:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-128">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="f3a9b-129">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contiene la logica per avviare l'attività in background.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-129">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="f3a9b-130">`StartAsync`viene chiamato *prima*di:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-130">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="f3a9b-131">La pipeline di elaborazione delle richieste dell'app è configurata ( `Startup.Configure` ).</span><span class="sxs-lookup"><span data-stu-id="f3a9b-131">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="f3a9b-132">Il server viene avviato e viene attivato [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="f3a9b-132">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="f3a9b-133">Il comportamento predefinito può essere modificato in modo che il servizio ospitato `StartAsync` venga eseguito dopo che la pipeline dell'app è stata configurata e `ApplicationStarted` chiamata.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-133">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="f3a9b-134">Per modificare il comportamento predefinito, aggiungere il servizio ospitato ( `VideosWatcher` nell'esempio seguente) dopo la chiamata a `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="f3a9b-134">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="f3a9b-135">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): attivato quando l'host esegue un arresto normale.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-135">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="f3a9b-136">`StopAsync` contiene la logica per terminare l'attività in background.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-136">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="f3a9b-137">Implementare <xref:System.IDisposable> e i [finalizzatori (distruttori)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) per eliminare tutte le risorse non gestite.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-137">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="f3a9b-138">Il token di annullamento ha un timeout predefinito di cinque secondi che indica che il processo di arresto non è più normale.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-138">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="f3a9b-139">Quando viene richiesto l'annullamento sul token:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-139">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="f3a9b-140">Tutte le operazioni in background rimanenti che sta eseguendo l'app devono essere interrotte.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-140">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="f3a9b-141">Tutti i metodi eventuali chiamati in `StopAsync` devono essere completati rapidamente.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-141">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="f3a9b-142">Tuttavia, dopo la richiesta di annullamento le attività non vengono abbandonate: il chiamante attende il completamento di tutte le attività.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-142">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="f3a9b-143">Se l'app si arresta in modo imprevisto, ad esempio, il processo dell'app ha esito negativo, il metodo `StopAsync` potrebbe non essere chiamato.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-143">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="f3a9b-144">Pertanto è possibile che i metodi chiamati o le operazioni effettuate in `StopAsync` non vengano eseguiti.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-144">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="f3a9b-145">Per estendere il timeout di arresto predefinito di cinque secondi, impostare:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-145">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="f3a9b-146"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> quando si usa l'host generico.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-146"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="f3a9b-147">Per altre informazioni, vedere <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-147">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="f3a9b-148">Impostazione di configurazione dell'host del timeout di arresto quando si usa l'host Web.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-148">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="f3a9b-149">Per altre informazioni, vedere <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-149">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="f3a9b-150">Il servizio ospitato viene attivato una volta all'avvio dell'app e arrestato normalmente all'arresto dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-150">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="f3a9b-151">Se viene generato un errore durante l'esecuzione dell'attività in background, deve essere chiamato `Dispose` anche se `StopAsync` non viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-151">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="f3a9b-152">Classe di base BackgroundService</span><span class="sxs-lookup"><span data-stu-id="f3a9b-152">BackgroundService base class</span></span>

<span data-ttu-id="f3a9b-153"><xref:Microsoft.Extensions.Hosting.BackgroundService>è una classe di base per l'implementazione di un oggetto a esecuzione prolungata <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="f3a9b-153"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="f3a9b-154">[ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) viene chiamato per eseguire il servizio in background.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-154">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="f3a9b-155">L'implementazione restituisce un oggetto <xref:System.Threading.Tasks.Task> che rappresenta l'intera durata del servizio in background.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-155">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="f3a9b-156">Non vengono avviati altri servizi fino a quando [ExecuteAsync non diventa asincrono](https://github.com/dotnet/extensions/issues/2149), ad esempio chiamando `await` .</span><span class="sxs-lookup"><span data-stu-id="f3a9b-156">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="f3a9b-157">Evitare di eseguire operazioni di inizializzazione lunghe e bloccate in `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="f3a9b-157">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="f3a9b-158">Blocchi host in [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) in attesa del `ExecuteAsync` completamento di.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-158">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="f3a9b-159">Il token di annullamento viene attivato quando viene chiamato [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) .</span><span class="sxs-lookup"><span data-stu-id="f3a9b-159">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="f3a9b-160">L'implementazione di `ExecuteAsync` deve terminare tempestivamente quando viene generato il token di annullamento per arrestare correttamente il servizio.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-160">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="f3a9b-161">In caso contrario, il servizio si arresta normalmente in corrispondenza del timeout di arresto.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-161">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="f3a9b-162">Per ulteriori informazioni, vedere la sezione [interfaccia IHostedService](#ihostedservice-interface) .</span><span class="sxs-lookup"><span data-stu-id="f3a9b-162">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="f3a9b-163">Attività in background programmate</span><span class="sxs-lookup"><span data-stu-id="f3a9b-163">Timed background tasks</span></span>

<span data-ttu-id="f3a9b-164">Un'attività programmata in background utilizza la classe [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="f3a9b-164">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="f3a9b-165">Il timer attiva il metodo `DoWork` dell'attività.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-165">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="f3a9b-166">Il timer viene disabilitato con `StopAsync` ed eliminato quando il contenitore dei servizi è eliminato con `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-166">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="f3a9b-167">Il <xref:System.Threading.Timer> non attende il completamento delle esecuzioni precedenti di `DoWork` , pertanto l'approccio illustrato potrebbe non essere adatto per ogni scenario.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-167">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="f3a9b-168">[Interlocked. Increment](xref:System.Threading.Interlocked.Increment*) viene usato per incrementare il contatore di esecuzione come operazione atomica, in modo da garantire che più thread non vengano aggiornati `executionCount` contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-168">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="f3a9b-169">Il servizio è registrato in `IHostBuilder.ConfigureServices` (*Program.cs*) con il `AddHostedService` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-169">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="f3a9b-170">Utilizzo di un servizio con ambito in un'attività in background</span><span class="sxs-lookup"><span data-stu-id="f3a9b-170">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="f3a9b-171">Per usare i [servizi con ambito](xref:fundamentals/dependency-injection#service-lifetimes) all'interno di un [BackgroundService](#backgroundservice-base-class), creare un ambito.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-171">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="f3a9b-172">Non viene creato automaticamente alcun ambito per un servizio ospitato.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-172">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="f3a9b-173">Il servizio dell'attività in background con ambito contiene la logica dell'attività in background.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-173">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="f3a9b-174">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-174">In the following example:</span></span>

* <span data-ttu-id="f3a9b-175">Il servizio è asincrono.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-175">The service is asynchronous.</span></span> <span data-ttu-id="f3a9b-176">Il metodo `DoWork` restituisce un elemento `Task`.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-176">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="f3a9b-177">A scopo dimostrativo, nel metodo è atteso un ritardo di dieci secondi `DoWork` .</span><span class="sxs-lookup"><span data-stu-id="f3a9b-177">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="f3a9b-178">Un oggetto <xref:Microsoft.Extensions.Logging.ILogger> viene inserito nel servizio.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-178">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="f3a9b-179">Il servizio ospitato crea un ambito per risolvere il servizio attività in background con ambito per chiamare il relativo `DoWork` metodo.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-179">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="f3a9b-180">`DoWork`Restituisce un oggetto `Task` , che è atteso in `ExecuteAsync` :</span><span class="sxs-lookup"><span data-stu-id="f3a9b-180">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="f3a9b-181">I servizi sono registrati in `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="f3a9b-181">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="f3a9b-182">Il servizio ospitato viene registrato con il `AddHostedService` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-182">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="f3a9b-183">Attività in background in coda</span><span class="sxs-lookup"><span data-stu-id="f3a9b-183">Queued background tasks</span></span>

<span data-ttu-id="f3a9b-184">Una coda delle attività in background è basata su .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> :</span><span class="sxs-lookup"><span data-stu-id="f3a9b-184">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="f3a9b-185">Nell'esempio seguente `QueueHostedService` :</span><span class="sxs-lookup"><span data-stu-id="f3a9b-185">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="f3a9b-186">Il `BackgroundProcessing` metodo restituisce un oggetto `Task` , che è atteso in `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="f3a9b-186">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="f3a9b-187">Le attività in background nella coda vengono rimesse in coda ed eseguite in `BackgroundProcessing` .</span><span class="sxs-lookup"><span data-stu-id="f3a9b-187">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="f3a9b-188">Gli elementi di lavoro sono in attesa prima che il servizio si arresti in `StopAsync` .</span><span class="sxs-lookup"><span data-stu-id="f3a9b-188">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="f3a9b-189">Un `MonitorLoop` servizio gestisce le attività di Accodamento per il servizio ospitato ogni volta che la `w` chiave viene selezionata in un dispositivo di input:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-189">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="f3a9b-190">`IBackgroundTaskQueue`Viene inserito nel `MonitorLoop` servizio.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-190">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="f3a9b-191">`IBackgroundTaskQueue.QueueBackgroundWorkItem`viene chiamato per accodare un elemento di lavoro.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-191">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="f3a9b-192">L'elemento di lavoro simula un'attività in background con esecuzione prolungata:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-192">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="f3a9b-193">Vengono eseguiti tre ritardi di 5 secondi ( `Task.Delay` ).</span><span class="sxs-lookup"><span data-stu-id="f3a9b-193">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="f3a9b-194">`try-catch` <xref:System.OperationCanceledException> Se l'attività viene annullata, viene intercettata un'istruzione.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-194">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="f3a9b-195">I servizi sono registrati in `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="f3a9b-195">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="f3a9b-196">Il servizio ospitato viene registrato con il `AddHostedService` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-196">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="f3a9b-197">`MonitorLoop`viene avviato in `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="f3a9b-197">`MonitorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f3a9b-198">In ASP.NET Core le attività in background possono essere implementate come *servizi ospitati*.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-198">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="f3a9b-199">Un servizio ospitato è una classe con logica di attività in background che implementa l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-199">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="f3a9b-200">In questo argomento vengono forniti tre esempi di servizio ospitato:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-200">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="f3a9b-201">Attività in background eseguita su un timer.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-201">Background task that runs on a timer.</span></span>
* <span data-ttu-id="f3a9b-202">Servizio ospitato che attiva un [servizio con ambito](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="f3a9b-202">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="f3a9b-203">Il servizio con ambito può usare l' [inserimento di dipendenze](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="f3a9b-203">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="f3a9b-204">Attività in background in coda che vengono eseguite in sequenza.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-204">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="f3a9b-205">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f3a9b-205">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="f3a9b-206">Pacchetto</span><span class="sxs-lookup"><span data-stu-id="f3a9b-206">Package</span></span>

<span data-ttu-id="f3a9b-207">Fare riferimento al [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) oppure aggiungere un riferimento al pacchetto [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).</span><span class="sxs-lookup"><span data-stu-id="f3a9b-207">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="f3a9b-208">Interfaccia IHostedService</span><span class="sxs-lookup"><span data-stu-id="f3a9b-208">IHostedService interface</span></span>

<span data-ttu-id="f3a9b-209">I servizi ospitati implementano l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-209">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="f3a9b-210">L'interfaccia definisce due metodi riservati agli oggetti gestiti dall'host:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-210">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="f3a9b-211">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contiene la logica per avviare l'attività in background.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-211">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="f3a9b-212">Quando si usa l' [host Web](xref:fundamentals/host/web-host), `StartAsync` viene chiamato dopo che il server è stato avviato e viene attivato [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="f3a9b-212">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="f3a9b-213">Quando si usa l' [host generico](xref:fundamentals/host/generic-host), `StartAsync` viene chiamato prima di `ApplicationStarted` viene attivato.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-213">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="f3a9b-214">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): attivato quando l'host esegue un arresto normale.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-214">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="f3a9b-215">`StopAsync` contiene la logica per terminare l'attività in background.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-215">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="f3a9b-216">Implementare <xref:System.IDisposable> e i [finalizzatori (distruttori)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) per eliminare tutte le risorse non gestite.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-216">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="f3a9b-217">Il token di annullamento ha un timeout predefinito di cinque secondi che indica che il processo di arresto non è più normale.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-217">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="f3a9b-218">Quando viene richiesto l'annullamento sul token:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-218">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="f3a9b-219">Tutte le operazioni in background rimanenti che sta eseguendo l'app devono essere interrotte.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-219">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="f3a9b-220">Tutti i metodi eventuali chiamati in `StopAsync` devono essere completati rapidamente.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-220">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="f3a9b-221">Tuttavia, dopo la richiesta di annullamento le attività non vengono abbandonate: il chiamante attende il completamento di tutte le attività.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-221">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="f3a9b-222">Se l'app si arresta in modo imprevisto, ad esempio, il processo dell'app ha esito negativo, il metodo `StopAsync` potrebbe non essere chiamato.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-222">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="f3a9b-223">Pertanto è possibile che i metodi chiamati o le operazioni effettuate in `StopAsync` non vengano eseguiti.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-223">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="f3a9b-224">Per estendere il timeout di arresto predefinito di cinque secondi, impostare:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-224">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="f3a9b-225"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> quando si usa l'host generico.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-225"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="f3a9b-226">Per altre informazioni, vedere <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-226">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="f3a9b-227">Impostazione di configurazione dell'host del timeout di arresto quando si usa l'host Web.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-227">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="f3a9b-228">Per altre informazioni, vedere <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-228">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="f3a9b-229">Il servizio ospitato viene attivato una volta all'avvio dell'app e arrestato normalmente all'arresto dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-229">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="f3a9b-230">Se viene generato un errore durante l'esecuzione dell'attività in background, deve essere chiamato `Dispose` anche se `StopAsync` non viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-230">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="f3a9b-231">Attività in background programmate</span><span class="sxs-lookup"><span data-stu-id="f3a9b-231">Timed background tasks</span></span>

<span data-ttu-id="f3a9b-232">Un'attività programmata in background utilizza la classe [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="f3a9b-232">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="f3a9b-233">Il timer attiva il metodo `DoWork` dell'attività.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-233">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="f3a9b-234">Il timer viene disabilitato con `StopAsync` ed eliminato quando il contenitore dei servizi è eliminato con `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-234">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="f3a9b-235">Il <xref:System.Threading.Timer> non attende il completamento delle esecuzioni precedenti di `DoWork` , pertanto l'approccio illustrato potrebbe non essere adatto per ogni scenario.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-235">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="f3a9b-236">Il servizio registrato in `Startup.ConfigureServices` con il metodo di estensione `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-236">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="f3a9b-237">Utilizzo di un servizio con ambito in un'attività in background</span><span class="sxs-lookup"><span data-stu-id="f3a9b-237">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="f3a9b-238">Per usare i [servizi con ambito](xref:fundamentals/dependency-injection#service-lifetimes) all'interno di un `IHostedService` , creare un ambito.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-238">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="f3a9b-239">Non viene creato automaticamente alcun ambito per un servizio ospitato.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-239">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="f3a9b-240">Il servizio dell'attività in background con ambito contiene la logica dell'attività in background.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-240">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="f3a9b-241">Nell'esempio seguente, <xref:Microsoft.Extensions.Logging.ILogger> viene inserito nel servizio:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-241">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="f3a9b-242">Il servizio ospitato crea un ambito per risolvere il servizio dell'attività in background con ambito e chiamare il relativo metodo `DoWork`:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-242">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="f3a9b-243">I servizi vengono registrati in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-243">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f3a9b-244">L'implementazione di `IHostedService`viene registrata con il metodo di estensione `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-244">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="f3a9b-245">Attività in background in coda</span><span class="sxs-lookup"><span data-stu-id="f3a9b-245">Queued background tasks</span></span>

<span data-ttu-id="f3a9b-246">Una coda delle attività in background è basata sulla .NET Framework 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([pianificata provvisoriamente per essere incorporata per ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="f3a9b-246">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="f3a9b-247">In `QueueHostedService`, le attività in background in coda vengono rimosse dalla coda ed eseguite come [BackgroundService](#backgroundservice-base-class), ovvero una classe di base per l'implementazione di un `IHostedService` a esecuzione prolungata:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-247">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="f3a9b-248">I servizi vengono registrati in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-248">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f3a9b-249">L'implementazione di `IHostedService`viene registrata con il metodo di estensione `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-249">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="f3a9b-250">Nella classe modello della pagina di indice:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-250">In the Index page model class:</span></span>

* <span data-ttu-id="f3a9b-251">`IBackgroundTaskQueue` viene inserito nel costruttore e assegnato a `Queue`.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-251">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="f3a9b-252">Un'interfaccia <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> viene inserita e assegnata a `_serviceScopeFactory`.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-252">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="f3a9b-253">La factory viene usata per creare istanze di <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, che consente di creare servizi all'interno di un ambito.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-253">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="f3a9b-254">Viene creato un ambito per usare la classe `AppDbContext` dell'app (un [servizio con ambito](xref:fundamentals/dependency-injection#service-lifetimes)) per scrivere record di database in `IBackgroundTaskQueue` (un servizio singleton).</span><span class="sxs-lookup"><span data-stu-id="f3a9b-254">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="f3a9b-255">Quando si seleziona il pulsante **Aggiungi attività** nella pagina di indice, viene eseguito il metodo `OnPostAddTask`.</span><span class="sxs-lookup"><span data-stu-id="f3a9b-255">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="f3a9b-256">`QueueBackgroundWorkItem`viene chiamato per accodare un elemento di lavoro:</span><span class="sxs-lookup"><span data-stu-id="f3a9b-256">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f3a9b-257">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f3a9b-257">Additional resources</span></span>

* [<span data-ttu-id="f3a9b-258">Implementare attività in background in microservizi con IHostedService e la classe BackgroundService</span><span class="sxs-lookup"><span data-stu-id="f3a9b-258">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="f3a9b-259">Eseguire attività in background con processi Web nel servizio app Azure</span><span class="sxs-lookup"><span data-stu-id="f3a9b-259">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
