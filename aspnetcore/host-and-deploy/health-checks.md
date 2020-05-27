---
<span data-ttu-id="bb5b2-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb5b2-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb5b2-102">'Blazor'</span></span>
- <span data-ttu-id="bb5b2-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb5b2-103">'Identity'</span></span>
- <span data-ttu-id="bb5b2-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb5b2-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb5b2-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb5b2-105">'Razor'</span></span>
- <span data-ttu-id="bb5b2-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="bb5b2-106">'SignalR' uid:</span></span> 

---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="bb5b2-107">Controlli di integrità in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bb5b2-107">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="bb5b2-108">Di [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="bb5b2-108">By [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bb5b2-109">ASP.NET Core offre middleware e librerie di controlli di integrità per segnalare l'integrità dei componenti dell'infrastruttura di app.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-109">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="bb5b2-110">I controlli di integrità vengono esposti da un'app come endpoint HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-110">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="bb5b2-111">È possibile configurare endpoint dei controlli di integrità per svariati scenari di monitoraggio in tempo reale:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-111">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="bb5b2-112">I probe di integrità possono essere usati dagli agenti di orchestrazione e dai servizi di bilanciamento del carico per controllare lo stato di un'app.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-112">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="bb5b2-113">Ad esempio, un agente di orchestrazione potrebbe rispondere a controllo di integrità non superato arrestando una distribuzione in sequenza o riavviando un contenitore.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-113">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="bb5b2-114">Un servizio di bilanciamento del carico potrebbe reagire a un'app non integra trasferendo il traffico dall'istanza con errori a un'istanza integra.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-114">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="bb5b2-115">È possibile monitorare lo stato di integrità di memoria, disco e altre risorse fisiche del server.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-115">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="bb5b2-116">I controlli di integrità possono testare le dipendenze di un'app, ad esempio i database e gli endpoint di servizio esterni, per verificare la disponibilità e il normale funzionamento.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-116">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="bb5b2-117">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bb5b2-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="bb5b2-118">L'app di esempio include esempi degli scenari descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-118">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="bb5b2-119">Per eseguire l'app di esempio per un determinato scenario, usare il comando [dotnet run](/dotnet/core/tools/dotnet-run) dalla cartella del progetto in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-119">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="bb5b2-120">Per informazioni dettagliate su come usare l'app di esempio, vedere il file *README.md* dell'app di esempio e le descrizioni degli scenari in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-120">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bb5b2-121">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="bb5b2-121">Prerequisites</span></span>

<span data-ttu-id="bb5b2-122">I controlli di integrità vengono in genere usati con un servizio di monitoraggio esterno o un agente di orchestrazione per controllare lo stato di un'app.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-122">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="bb5b2-123">Prima di aggiungere i controlli di integrità a un'app, decidere quale sistema di monitoraggio usare.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-123">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="bb5b2-124">Il sistema di monitoraggio determina quali tipi di controlli di integrità creare e come configurare i relativi endpoint.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-124">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="bb5b2-125">Si fa riferimento al pacchetto [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) in modo implicito per le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-125">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="bb5b2-126">Per eseguire i controlli di integrità usando Entity Framework Core, aggiungere un riferimento al pacchetto [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-126">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="bb5b2-127">L'app di esempio include il codice di avvio per illustrare i controlli di integrità per diversi scenari.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-127">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="bb5b2-128">Lo scenario di [probe del database](#database-probe) controlla l'integrità di una connessione di database usando [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-128">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="bb5b2-129">Lo scenario [Probe DbContext](#entity-framework-core-dbcontext-probe) verifica un database usando un elemento `DbContext` di EF Core.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-129">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="bb5b2-130">Per esplorare gli scenari relativi ai database, l'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-130">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="bb5b2-131">Crea un database e specifica la stringa di connessione nel file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-131">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="bb5b2-132">Dispone dei riferimenti al pacchetto seguenti nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-132">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="bb5b2-133">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="bb5b2-133">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="bb5b2-134">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="bb5b2-134">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="bb5b2-135">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) non è gestito né supportato da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-135">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="bb5b2-136">Un altro scenario relativo ai controlli di integrità illustra come filtrare i controlli di integrità per una porta di gestione.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-136">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="bb5b2-137">L'app di esempio richiede di creare un file *Properties/launchSettings.json* che include l'URL di gestione e la porta di gestione.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-137">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="bb5b2-138">Per altre informazioni, vedere la sezione [Filtrare in base alla porta](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-138">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="bb5b2-139">Probe di integrità di base</span><span class="sxs-lookup"><span data-stu-id="bb5b2-139">Basic health probe</span></span>

<span data-ttu-id="bb5b2-140">Per molte app, una configurazione del probe di integrità di base, che segnala la disponibilità dell'app per elaborare le richieste (*attività*), è sufficiente per individuare lo stato dell'app.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-140">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="bb5b2-141">La configurazione di base registra i servizi di controllo integrità e chiama il middleware dei controlli di integrità per rispondere a un endpoint URL con una risposta di integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-141">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="bb5b2-142">Per impostazione predefinita, non vengono registrati controlli di integrità specifici per testare particolari dipendenze o sottosistemi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-142">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="bb5b2-143">L'app viene considerata integra se riesce a rispondere all'URL dell'endpoint di integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-143">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="bb5b2-144">Il writer di risposta predefinito scrive lo stato (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) come risposta di testo non crittografato per il client, indicando uno stato [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) o [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-144">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="bb5b2-145">Registrare i servizi dei controlli di integrità con <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-145">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bb5b2-146">Creare un endpoint di controllo integrità chiamando `MapHealthChecks` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-146">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="bb5b2-147">Nell'app di esempio l'endpoint di controllo di integrità viene creato in `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-147">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

<span data-ttu-id="bb5b2-148">Per eseguire lo scenario di configurazione di base usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-148">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="bb5b2-149">Esempio Docker</span><span class="sxs-lookup"><span data-stu-id="bb5b2-149">Docker example</span></span>

<span data-ttu-id="bb5b2-150">[Docker](xref:host-and-deploy/docker/index) offre una direttiva `HEALTHCHECK` predefinita che può essere usata per controllare lo stato di un'app che usa la configurazione dei controlli di integrità di base:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-150">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="bb5b2-151">Creare controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="bb5b2-151">Create health checks</span></span>

<span data-ttu-id="bb5b2-152">I controlli di integrità vengono creati implementando l'interfaccia <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-152">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="bb5b2-153">Il metodo <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> restituisce <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> che indica l'integrità come `Healthy`, `Degraded` o `Unhealthy`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-153">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="bb5b2-154">Il risultato viene scritto come risposta di testo non crittografato con un codice di stato configurabile. La configurazione viene descritta nella sezione [Opzioni dei controlli di integrità](#health-check-options).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-154">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="bb5b2-155"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> può anche restituire coppie chiave-valore facoltative.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-155"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="bb5b2-156">Nella `ExampleHealthCheck` classe seguente viene illustrato il layout di un controllo di integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-156">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="bb5b2-157">La logica dei controlli di integrità viene inserita nel `CheckHealthAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-157">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="bb5b2-158">Nell'esempio seguente viene impostata una variabile fittizia, `healthCheckResultHealthy` , su `true` .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-158">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="bb5b2-159">Se il valore di `healthCheckResultHealthy` è impostato su `false` , viene restituito lo stato [HealthCheckResult. unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-159">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a><span data-ttu-id="bb5b2-160">Registrare i servizi di controllo dell'integrità</span><span class="sxs-lookup"><span data-stu-id="bb5b2-160">Register health check services</span></span>

<span data-ttu-id="bb5b2-161">Il `ExampleHealthCheck` tipo viene aggiunto ai servizi di controllo integrità con <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="bb5b2-161">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="bb5b2-162">L'overload <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> illustrato nell'esempio seguente imposta lo stato di errore (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) per segnalare quando il controllo di integrità indica un errore.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-162">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="bb5b2-163">Se lo stato di errore è impostato su `null` (impostazione predefinita), viene segnalato [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-163">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="bb5b2-164">Questo overload è uno scenario utile per gli autori di librerie, in cui lo stato di errore indicato dalla libreria viene applicato dall'app quando si verifica un errore di controllo dell'integrità se l'implementazione del controllo dell'integrità rispetta l'impostazione.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-164">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="bb5b2-165">Si possono usare *tag* per filtrare i controlli di integrità, come illustrato più avanti nella sezione [Filtrare i controlli di integrità](#filter-health-checks).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-165">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="bb5b2-166"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> può anche eseguire una funzione lambda.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-166"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="bb5b2-167">Nell'esempio seguente il nome del controllo di integrità viene specificato come `Example` e il controllo restituisce sempre uno stato integro:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-167">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="bb5b2-168">Chiamare <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> per passare argomenti a un'implementazione del controllo integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-168">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arguments to a health check implementation.</span></span> <span data-ttu-id="bb5b2-169">Nell'esempio seguente `TestHealthCheckWithArgs` accetta un numero intero e una stringa da usare quando <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> viene chiamato il metodo:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-169">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

<span data-ttu-id="bb5b2-170">`TestHealthCheckWithArgs`viene registrato chiamando `AddTypeActivatedCheck` con il valore integer e la stringa passati all'implementazione:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-170">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="bb5b2-171">Usare i controlli di integrità routing</span><span class="sxs-lookup"><span data-stu-id="bb5b2-171">Use Health Checks Routing</span></span>

<span data-ttu-id="bb5b2-172">In `Startup.Configure` , chiamare `MapHealthChecks` sul generatore di endpoint con l'URL dell'endpoint o il percorso relativo:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-172">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="bb5b2-173">Richiedi host</span><span class="sxs-lookup"><span data-stu-id="bb5b2-173">Require host</span></span>

<span data-ttu-id="bb5b2-174">Chiamare `RequireHost` per specificare uno o più host consentiti per l'endpoint di controllo integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-174">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="bb5b2-175">Gli host devono essere Unicode anziché Punycode e possono includere una porta.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-175">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="bb5b2-176">Se non viene specificata una raccolta, viene accettato qualsiasi host.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-176">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="bb5b2-177">Per altre informazioni, vedere la sezione [Filtrare in base alla porta](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-177">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="bb5b2-178">Richiedi autorizzazione</span><span class="sxs-lookup"><span data-stu-id="bb5b2-178">Require authorization</span></span>

<span data-ttu-id="bb5b2-179">Chiamare `RequireAuthorization` per eseguire il middleware di autorizzazione sull'endpoint della richiesta di controllo integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-179">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="bb5b2-180">Un `RequireAuthorization` Overload accetta uno o più criteri di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-180">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="bb5b2-181">Se un criterio non viene specificato, vengono usati i criteri di autorizzazione predefiniti.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-181">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="bb5b2-182">Abilitare richieste tra le origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="bb5b2-182">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="bb5b2-183">Sebbene i controlli di integrità eseguiti manualmente da un browser non siano uno scenario di utilizzo comune, il middleware CORS può essere abilitato chiamando `RequireCors` sugli endpoint di controllo dell'integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-183">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="bb5b2-184">Un `RequireCors` Overload accetta un delegato del generatore di criteri CORS ( `CorsPolicyBuilder` ) o un nome di criterio.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-184">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="bb5b2-185">Se non viene specificato alcun criterio, viene usato il criterio CORS predefinito.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-185">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="bb5b2-186">Per altre informazioni, vedere <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-186">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="bb5b2-187">Opzioni dei controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="bb5b2-187">Health check options</span></span>

<span data-ttu-id="bb5b2-188"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> consente di personalizzare il comportamento dei controlli di integrità:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-188"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="bb5b2-189">Filtrare i controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="bb5b2-189">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="bb5b2-190">Personalizzare il codice di stato HTTP</span><span class="sxs-lookup"><span data-stu-id="bb5b2-190">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="bb5b2-191">Eliminare le intestazioni della cache</span><span class="sxs-lookup"><span data-stu-id="bb5b2-191">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="bb5b2-192">Personalizzare l'output</span><span class="sxs-lookup"><span data-stu-id="bb5b2-192">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="bb5b2-193">Filtrare i controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="bb5b2-193">Filter health checks</span></span>

<span data-ttu-id="bb5b2-194">Per impostazione predefinita, il middleware controlli integrità esegue tutti i controlli di integrità registrati.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-194">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="bb5b2-195">Per eseguire un subset dei controlli di integrità, specificare una funzione che restituisce un valore booleano all'opzione <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate>.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-195">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="bb5b2-196">Nell'esempio seguente il controllo di integrità `Bar` viene filtrato in base al tag (`bar_tag`) nell'istruzione condizionale della funzione, dove `true` viene restituito solo se la proprietà <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> del controllo di integrità corrisponde a `foo_tag` o a `baz_tag`:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-196">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="bb5b2-197">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-197">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="bb5b2-198">In `Startup.Configure` il `Predicate` controllo integrità della barra è filtrato.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-198">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="bb5b2-199">Solo foo e Baz Execute.:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-199">Only Foo and Baz execute.:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="bb5b2-200">Personalizzare il codice di stato HTTP</span><span class="sxs-lookup"><span data-stu-id="bb5b2-200">Customize the HTTP status code</span></span>

<span data-ttu-id="bb5b2-201">Usare <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> per personalizzare il mapping dello stato di integrità dei codici di stato HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-201">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="bb5b2-202">Le assegnazioni <xref:Microsoft.AspNetCore.Http.StatusCodes> seguenti sono i valori predefiniti usati dal middleware.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-202">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="bb5b2-203">Modificare i valori dei codici di stato in base ai requisiti.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-203">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="bb5b2-204">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-204">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="bb5b2-205">Eliminare le intestazioni della cache</span><span class="sxs-lookup"><span data-stu-id="bb5b2-205">Suppress cache headers</span></span>

<span data-ttu-id="bb5b2-206"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Controlla se il middleware dei controlli di integrità aggiunge intestazioni HTTP a una risposta Probe per evitare la memorizzazione nella cache delle risposte.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-206"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="bb5b2-207">Se il valore è `false` (impostazione predefinita), il middleware imposta le intestazioni `Cache-Control`, `Expires` e `Pragma` o ne esegue l'override per impedire la memorizzazione della risposta nella cache.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-207">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="bb5b2-208">Se il valore è `true`, il middleware non modifica le intestazioni della risposta nella cache.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-208">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="bb5b2-209">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-209">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="bb5b2-210">Personalizzare l'output</span><span class="sxs-lookup"><span data-stu-id="bb5b2-210">Customize output</span></span>

<span data-ttu-id="bb5b2-211">In `Startup.Configure` impostare l'opzione [HealthCheckOptions. ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) su un delegato per la scrittura della risposta:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-211">In `Startup.Configure`, set the [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) option to a delegate for writing the response:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="bb5b2-212">Il delegato predefinito scrive una risposta in testo non crittografato minima con il valore di stringa [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-212">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="bb5b2-213">I seguenti delegati personalizzati restituiscono una risposta JSON personalizzata.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-213">The following custom delegates output a custom JSON response.</span></span>

<span data-ttu-id="bb5b2-214">Il primo esempio dell'app di esempio illustra come usare <xref:System.Text.Json?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="bb5b2-214">The first example from the sample app demonstrates how to use <xref:System.Text.Json?displayProperty=fullName>:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

<span data-ttu-id="bb5b2-215">Nel secondo esempio viene illustrato come usare [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-215">The second example demonstrates how to use [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

<span data-ttu-id="bb5b2-216">Nell'app di esempio impostare come commento la `SYSTEM_TEXT_JSON` [direttiva per il preprocessore](xref:index#preprocessor-directives-in-sample-code) in *CustomWriterStartup.cs* per abilitare la `Newtonsoft.Json` versione di `WriteResponse` .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-216">In the sample app, comment out the `SYSTEM_TEXT_JSON` [preprocessor directive](xref:index#preprocessor-directives-in-sample-code) in *CustomWriterStartup.cs* to enable the `Newtonsoft.Json` version of `WriteResponse`.</span></span>

<span data-ttu-id="bb5b2-217">L'API controlli di integrità non fornisce supporto incorporato per i formati restituiti JSON complessi, perché il formato è specifico del sistema di monitoraggio scelto.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-217">The health checks API doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="bb5b2-218">Personalizzare la risposta negli esempi precedenti in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-218">Customize the response in the preceding examples as needed.</span></span> <span data-ttu-id="bb5b2-219">Per altre informazioni sulla serializzazione JSON con `System.Text.Json` , vedere [come serializzare e deserializzare JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-219">For more information on JSON serialization with `System.Text.Json`, see [How to serialize and deserialize JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span></span>

## <a name="database-probe"></a><span data-ttu-id="bb5b2-220">Probe del database</span><span class="sxs-lookup"><span data-stu-id="bb5b2-220">Database probe</span></span>

<span data-ttu-id="bb5b2-221">Un controllo di integrità può specificare una query di database da eseguire come test booleano per indicare se il database sta rispondendo normalmente.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-221">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="bb5b2-222">L'app di esempio usa [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), una libreria di controlli di integrità per app ASP.NET Core, per eseguire un controllo di integrità su un database di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-222">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="bb5b2-223">`AspNetCore.Diagnostics.HealthChecks` esegue una query `SELECT 1` sul database per confermare che la connessione al database sia integra.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-223">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="bb5b2-224">Quando si controlla una connessione di database con una query, scegliere una query che viene restituita rapidamente.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-224">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="bb5b2-225">Dall'approccio alla query possono derivare il sovraccarico del database e il degrado delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-225">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="bb5b2-226">Nella maggior parte dei casi, l'esecuzione di una query di test non è necessaria.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-226">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="bb5b2-227">È sufficiente stabilire una connessione al database.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-227">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="bb5b2-228">Se invece è necessario eseguire una query, scegliere una semplice query SELECT, ad esempio `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-228">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="bb5b2-229">Includere un riferimento al pacchetto [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-229">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="bb5b2-230">Fornire una stringa di connessione di database valida nel file *appsettings.json* dell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-230">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="bb5b2-231">L'app usa una database di SQL Server denominato `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-231">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="bb5b2-232">Registrare i servizi dei controlli di integrità con <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-232">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bb5b2-233">L'app di esempio chiama il metodo `AddSqlServer` di con la stringa di connessione del database (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-233">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="bb5b2-234">Un endpoint di controllo dell'integrità viene creato chiamando `MapHealthChecks` in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="bb5b2-234">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="bb5b2-235">Per eseguire lo scenario di probe del database di base usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-235">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="bb5b2-236">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) non è gestito né supportato da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-236">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="bb5b2-237">Probe DbContext di Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="bb5b2-237">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="bb5b2-238">Il controllo `DbContext` verifica che l'app possa comunicare con il database configurato per un `DbContext` di EF Core.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-238">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="bb5b2-239">Il controllo `DbContext` è supportato nelle app che:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-239">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="bb5b2-240">Usano [Entity Framework (EF) Core](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-240">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="bb5b2-241">Includono un riferimento del pacchetto a [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-241">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="bb5b2-242">`AddDbContextCheck<TContext>` registra un controllo di integrità per un `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-242">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="bb5b2-243">`DbContext` viene reso disponibile come `TContext` al metodo.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-243">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="bb5b2-244">È disponibile un overload per configurare lo stato di errore, i tag e una query di test personalizzata.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-244">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="bb5b2-245">Per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-245">By default:</span></span>

* <span data-ttu-id="bb5b2-246">`DbContextHealthCheck` chiama il metodo `CanConnectAsync` di EF Core.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-246">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="bb5b2-247">È possibile determinare l'operazione da eseguire quando si controlla l'integrità usando gli overload del metodo `AddDbContextCheck`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-247">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="bb5b2-248">Il nome del controllo di integrità è il nome del tipo `TContext`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-248">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="bb5b2-249">Nell'app di esempio, `AppDbContext` viene fornito a `AddDbContextCheck` e registrato come servizio in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-249">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="bb5b2-250">Un endpoint di controllo dell'integrità viene creato chiamando `MapHealthChecks` in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="bb5b2-250">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="bb5b2-251">Per eseguire lo scenario di probe `DbContext` usando l'app di esempio, verificare che il database specificato dalla stringa di connessione non esista nell'istanza di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-251">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="bb5b2-252">Se il database esiste, eliminarlo.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-252">If the database exists, delete it.</span></span>

<span data-ttu-id="bb5b2-253">Eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-253">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="bb5b2-254">Dopo che l'app è in esecuzione, controllare lo stato di integrità effettuando una richiesta all'endpoint `/health` in un browser.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-254">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="bb5b2-255">Il database e `AppDbContext` non esistono, quindi l'app fornisce la risposta seguente:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-255">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="bb5b2-256">Attivare l'app di esempio per creare il database.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-256">Trigger the sample app to create the database.</span></span> <span data-ttu-id="bb5b2-257">Effettuare una richiesta a `/createdatabase`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-257">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="bb5b2-258">L'app risponde:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-258">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="bb5b2-259">Effettuare una richiesta all'endpoint `/health`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-259">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="bb5b2-260">Il database e il contesto esistono, quindi l'app risponde:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-260">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="bb5b2-261">Attivare l'app di esempio per eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-261">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="bb5b2-262">Effettuare una richiesta a `/deletedatabase`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-262">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="bb5b2-263">L'app risponde:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-263">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="bb5b2-264">Effettuare una richiesta all'endpoint `/health`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-264">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="bb5b2-265">L'app fornisce una risposta non integra:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-265">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="bb5b2-266">Separare i probe di idoneità e di attività</span><span class="sxs-lookup"><span data-stu-id="bb5b2-266">Separate readiness and liveness probes</span></span>

<span data-ttu-id="bb5b2-267">In alcuni scenari di hosting vengono usati due controlli di integrità che distinguono due stati dell'app:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-267">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="bb5b2-268">L'app è funzionante, ma non ancora pronta a ricevere le richieste.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-268">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="bb5b2-269">Questo stato è l'*idoneità* dell'app.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-269">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="bb5b2-270">L'app è funzionante e risponde alle richieste.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-270">The app is functioning and responding to requests.</span></span> <span data-ttu-id="bb5b2-271">Questo stato è l'*attività* dell'app.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-271">This state is the app's *liveness*.</span></span>

<span data-ttu-id="bb5b2-272">Il controllo di idoneità in genere esegue un set di controlli più completo e dispendioso in termini di tempo per determinare se tutti i sottosistemi e le risorse dell'app sono disponibili.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-272">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="bb5b2-273">Un controllo di attività esegue semplicemente un rapido controllo per determinare se l'app è disponibile per elaborare le richieste.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-273">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="bb5b2-274">Dopo che l'app ha superato il controllo di idoneità, non è necessario caricare ulteriormente l'app con il dispendioso set di controlli di idoneità. Sarà sufficiente controllare solo l'attività.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-274">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="bb5b2-275">L'app di esempio contiene un controllo di integrità per segnalare il completamento dell'attività di avvio con esecuzione prolungata in un [servizio ospitato](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-275">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="bb5b2-276">`StartupHostedServiceHealthCheck` espone una proprietà, `StartupTaskCompleted`, che il servizio ospitato può impostare su `true` al termine dell'attività con esecuzione prolungata (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-276">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="bb5b2-277">L'attività in background con esecuzione prolungata viene avviata da un [servizio ospitato](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-277">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="bb5b2-278">Al termine dell'attività, `StartupHostedServiceHealthCheck.StartupTaskCompleted` viene impostata su `true`:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-278">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="bb5b2-279">Il controllo di integrità viene registrato con <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` insieme al servizio ospitato.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-279">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="bb5b2-280">Poiché il servizio ospitato deve impostare la proprietà sul controllo di integrità, il controllo di integrità viene anche registrato nel contenitore del servizio (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-280">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="bb5b2-281">Un endpoint di controllo dell'integrità viene creato chiamando `MapHealthChecks` in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-281">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="bb5b2-282">Nell'app di esempio gli endpoint di controllo integrità vengono creati in:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-282">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="bb5b2-283">`/health/ready`per il controllo della conformità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-283">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="bb5b2-284">Il controllo di idoneità filtra i controlli di integrità per il controllo di integrità con il tag `ready`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-284">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="bb5b2-285">`/health/live`per il controllo dell'anima.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-285">`/health/live` for the liveness check.</span></span> <span data-ttu-id="bb5b2-286">Il controllo della durata filtra l'oggetto `StartupHostedServiceHealthCheck` restituendo `false` in [HealthCheckOptions. Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (per altre informazioni, vedere [filtrare i controlli di integrità](#filter-health-checks))</span><span class="sxs-lookup"><span data-stu-id="bb5b2-286">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="bb5b2-287">Nell'esempio di codice seguente:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-287">In the following example code:</span></span>

* <span data-ttu-id="bb5b2-288">Il controllo di conformità utilizza tutti i controlli registrati con il tag "Ready".</span><span class="sxs-lookup"><span data-stu-id="bb5b2-288">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="bb5b2-289">`Predicate`Esclude tutti i controlli e restituisce 200-OK.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-289">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

<span data-ttu-id="bb5b2-290">Per eseguire lo scenario di configurazione di idoneità/attività usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-290">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="bb5b2-291">In a browser visitare più volte `/health/ready` per 15 secondi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-291">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="bb5b2-292">Il controllo di integrità segnala *Unhealthy* per i primi 15 secondi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-292">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="bb5b2-293">Dopo 15 secondi, l'endpoint segnala *Healthy*, che indica il completamento dell'attività con esecuzione prolungata da parte del servizio ospitato.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-293">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="bb5b2-294">Questo esempio crea anche un server di pubblicazione dei controlli di integrità (implementazione di <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>) che esegue il primo controllo di conformità con un ritardo di due secondi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-294">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="bb5b2-295">Per altre informazioni, vedere la sezione [Server di pubblicazione dei controlli di integrità](#health-check-publisher).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-295">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="bb5b2-296">Esempio di Kubernetes</span><span class="sxs-lookup"><span data-stu-id="bb5b2-296">Kubernetes example</span></span>

<span data-ttu-id="bb5b2-297">L'uso di controlli di idoneità e attività separati è utile in un ambiente come [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-297">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="bb5b2-298">In Kubernetes un'app potrebbe dover eseguire un'operazione di avvio con esecuzione prolungata prima di accettare le richieste, ad esempio un test della disponibilità del database sottostante.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-298">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="bb5b2-299">L'uso di controlli separati consente all'agente di orchestrazione di distinguere se l'app è funzionante, ma non è ancora pronta o se l'app non è stata avviata.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-299">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="bb5b2-300">Per altre informazioni sui probe di idoneità e di attività in Kubernetes, vedere [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) (Configurare i probe di attività e di idoneità in Kubernetes) nella documentazione di Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-300">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="bb5b2-301">L'esempio seguente illustra la configurazione di un probe di idoneità di Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-301">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```yml
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="bb5b2-302">Probe basato sulle metriche con un writer di risposta personalizzata</span><span class="sxs-lookup"><span data-stu-id="bb5b2-302">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="bb5b2-303">L'app di esempio illustra un controllo di integrità della memoria con un writer di risposta personalizzata.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-303">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="bb5b2-304">`MemoryHealthCheck` segnala uno stato danneggiato se l'app usa più di una determinata soglia di memoria (1 GB nell'app di esempio).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-304">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="bb5b2-305"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> include informazioni sul Garbage Collector (GC) per l'app (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-305">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="bb5b2-306">Registrare i servizi dei controlli di integrità con <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-306">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bb5b2-307">Invece di abilitare il controllo di integrità passandolo ad <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, `MemoryHealthCheck` viene registrato come servizio.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-307">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="bb5b2-308">Tutti i servizi registrati da <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> sono disponibili per il middleware e i servizi di controllo dell'integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-308">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="bb5b2-309">È consigliabile registrare i servizi di controllo dell'integrità come servizi Singleton.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-309">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="bb5b2-310">In *CustomWriterStartup.cs* dell'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-310">In *CustomWriterStartup.cs* of the sample app:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="bb5b2-311">Un endpoint di controllo dell'integrità viene creato chiamando `MapHealthChecks` in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-311">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="bb5b2-312">Un `WriteResponse` delegato viene fornito alla proprietà <Microsoft. AspNetCore. Diagnostics. HealthChecks. HealthCheckOptions. ResponseWriter> per restituire una risposta JSON personalizzata quando viene eseguito il controllo integrità:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-312">A `WriteResponse` delegate is provided to the <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="bb5b2-313">Il `WriteResponse` delegato formatta `CompositeHealthCheckResult` in un oggetto JSON e restituisce l'output JSON per la risposta del controllo integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-313">The `WriteResponse` delegate formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response.</span></span> <span data-ttu-id="bb5b2-314">Per ulteriori informazioni, vedere la sezione [personalizzare l'output](#customize-output) .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-314">For more information, see the [Customize output](#customize-output) section.</span></span>

<span data-ttu-id="bb5b2-315">Per eseguire lo scenario di probe basato sulle metriche con il writer di risposta personalizzata usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-315">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="bb5b2-316">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) include scenari di controllo dell'integrità basati su metriche, tra cui i controlli dell'archiviazione su disco e dell'attività dei valori massimi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-316">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="bb5b2-317">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) non è gestito né supportato da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-317">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="bb5b2-318">Filtrare in base alla porta</span><span class="sxs-lookup"><span data-stu-id="bb5b2-318">Filter by port</span></span>

<span data-ttu-id="bb5b2-319">Chiamare `RequireHost` `MapHealthChecks` con un modello di URL che specifica una porta per limitare le richieste di controllo integrità alla porta specificata.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-319">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="bb5b2-320">Questa chiamata viene in genere usata nell'ambiente di un contenitore per esporre una porta per il monitoraggio dei servizi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-320">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="bb5b2-321">L'app di esempio configura la porta usando il [provider di configurazione della variabile di ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-321">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="bb5b2-322">La porta viene impostata nel file *launchSettings.json* e passata al provider di configurazione tramite una variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-322">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="bb5b2-323">È anche necessario configurare il server per l'ascolto di richieste sulla porta di gestione.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-323">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="bb5b2-324">Per usare l'app di esempio per illustrare la configurazione della porta di gestione, creare il file *launchSettings.json* in una cartella *Properties*.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-324">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="bb5b2-325">Il file *Properties/launchSettings. JSON* seguente nell'app di esempio non è incluso nei file di progetto dell'app di esempio e deve essere creato manualmente:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-325">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="bb5b2-326">Registrare i servizi dei controlli di integrità con <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-326">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bb5b2-327">Creare un endpoint di controllo integrità chiamando `MapHealthChecks` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-327">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="bb5b2-328">Nell'app di esempio, una chiamata a `RequireHost` sull'endpoint in `Startup.Configure` specifica la porta di gestione dalla configurazione:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-328">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="bb5b2-329">Gli endpoint vengono creati nell'app di esempio in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-329">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="bb5b2-330">Nell'esempio di codice seguente:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-330">In the following example code:</span></span>

* <span data-ttu-id="bb5b2-331">Il controllo di conformità utilizza tutti i controlli registrati con il tag "Ready".</span><span class="sxs-lookup"><span data-stu-id="bb5b2-331">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="bb5b2-332">`Predicate`Esclude tutti i controlli e restituisce 200-OK.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-332">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> <span data-ttu-id="bb5b2-333">È possibile evitare di creare il file *launchSettings. JSON* nell'app di esempio impostando la porta di gestione in modo esplicito nel codice.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-333">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="bb5b2-334">In *Program.cs* , in cui <xref:Microsoft.Extensions.Hosting.HostBuilder> viene creato l'oggetto, aggiungere una chiamata a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> e fornire l'endpoint della porta di gestione dell'app.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-334">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="bb5b2-335">In `Configure` di *ManagementPortStartup.cs*specificare la porta di gestione con `RequireHost` :</span><span class="sxs-lookup"><span data-stu-id="bb5b2-335">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="bb5b2-336">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-336">*Program.cs*:</span></span>
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> <span data-ttu-id="bb5b2-337">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-337">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="bb5b2-338">Per eseguire lo scenario di configurazione della porta di gestione usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-338">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="bb5b2-339">Distribuire una libreria di controllo di integrità</span><span class="sxs-lookup"><span data-stu-id="bb5b2-339">Distribute a health check library</span></span>

<span data-ttu-id="bb5b2-340">Per distribuire un controllo di integrità come libreria:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-340">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="bb5b2-341">Scrivere un controllo di integrità che implementa l'interfaccia <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> come classe autonoma.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-341">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="bb5b2-342">La classe può basarsi su [inserimento delle dipendenze](xref:fundamentals/dependency-injection), attivazione del tipo e [opzioni denominate](xref:fundamentals/configuration/options) per accedere ai dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-342">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="bb5b2-343">Nella logica dei controlli di integrità di `CheckHealthAsync` :</span><span class="sxs-lookup"><span data-stu-id="bb5b2-343">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="bb5b2-344">`data1`e `data2` vengono usati nel metodo per eseguire la logica del controllo di integrità del probe.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-344">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="bb5b2-345">`AccessViolationException`viene gestito.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-345">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="bb5b2-346">Quando <xref:System.AccessViolationException> si verifica un <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> errore, viene restituito con <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> per consentire agli utenti di configurare lo stato di errore dei controlli di integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-346">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. <span data-ttu-id="bb5b2-347">Scrivere un metodo di estensione con i parametri che l'app chiama nel metodo `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-347">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="bb5b2-348">Nell'esempio seguente si presuma la firma del metodo di controllo dell'integrità seguente:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-348">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="bb5b2-349">La firma precedente indica che `ExampleHealthCheck` richiede dati aggiuntivi per elaborare la logica del probe di controllo dell'integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-349">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="bb5b2-350">I dati vengono forniti al delegato usato per creare l'istanza del controllo di integrità quando il controllo di integrità viene registrato con un metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-350">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="bb5b2-351">Nell'esempio seguente il chiamante specifica facoltativamente:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-351">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="bb5b2-352">nome del controllo integrità (`name`).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-352">health check name (`name`).</span></span> <span data-ttu-id="bb5b2-353">Se `null`, viene usato `example_health_check`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-353">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="bb5b2-354">punto dati stringa per il controllo di integrità (`data1`).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-354">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="bb5b2-355">punto dati Integer per il controllo di integrità (`data2`).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-355">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="bb5b2-356">Se `null`, viene usato `1`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-356">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="bb5b2-357">stato dell'errore (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-357">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="bb5b2-358">Il valore predefinito è `null`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-358">The default is `null`.</span></span> <span data-ttu-id="bb5b2-359">Se `null`, viene segnalato [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) per uno stato di errore.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-359">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="bb5b2-360">tag (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-360">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="bb5b2-361">Server di pubblicazione dei controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="bb5b2-361">Health Check Publisher</span></span>

<span data-ttu-id="bb5b2-362">Quando <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> viene aggiunto al contenitore del servizio, il sistema di controllo dell'integrità esegue periodicamente i controlli di integrità e chiama `PublishAsync` con il risultato.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-362">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="bb5b2-363">Ciò è utile in uno scenario relativo a un sistema di monitoraggio dell'integrità basato su push, che prevede che ogni processo chiami periodicamente il sistema di monitoraggio per determinare l'integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-363">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="bb5b2-364">L'interfaccia <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> ha un solo metodo:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-364">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="bb5b2-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> consente di impostare:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="bb5b2-366"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash;Il ritardo iniziale applicato dopo l'avvio dell'app prima dell'esecuzione di <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> istanze.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-366"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="bb5b2-367">Il ritardo viene applicato una sola volta all'avvio e non si applica alle iterazioni successive.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-367">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="bb5b2-368">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-368">The default value is five seconds.</span></span>
* <span data-ttu-id="bb5b2-369"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash;Periodo di <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> esecuzione.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-369"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="bb5b2-370">Il valore predefinito è 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-370">The default value is 30 seconds.</span></span>
* <span data-ttu-id="bb5b2-371"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash;Se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> è `null` (impostazione predefinita), il servizio di pubblicazione controllo integrità esegue tutti i controlli di integrità registrati.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-371"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="bb5b2-372">Per eseguire un subset dei controlli di integrità, specificare una funzione che filtra il set di controlli.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-372">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="bb5b2-373">Il predicato viene valutato per ogni periodo.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-373">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="bb5b2-374"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash;Timeout per l'esecuzione dei controlli di integrità per tutte le <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> istanze.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-374"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="bb5b2-375">Usare <xref:System.Threading.Timeout.InfiniteTimeSpan> per l'esecuzione senza un timeout.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-375">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="bb5b2-376">Il valore predefinito è 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-376">The default value is 30 seconds.</span></span>

<span data-ttu-id="bb5b2-377">Nell'app di esempio, `ReadinessPublisher` è un'implementazione di <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-377">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="bb5b2-378">Lo stato del controllo di integrità viene registrato per ogni controllo a livello di log di:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-378">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="bb5b2-379">Information ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> ) se lo stato dei controlli di integrità è <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-379">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="bb5b2-380">Error ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*> ) se lo stato è <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> o <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-380">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="bb5b2-381">Nell'esempio di `LivenessProbeStartup` dell'app di esempio, il controllo di conformità `StartupHostedService` ha un ritardo di avvio di due secondi e il controllo viene eseguito ogni 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-381">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="bb5b2-382">Per attivare l'implementazione <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, l'esempio registra `ReadinessPublisher` come servizio singleton nel contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-382">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="bb5b2-383">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) include i server di pubblicazione per diversi sistemi, tra cui [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-383">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="bb5b2-384">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) non è gestito né supportato da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-384">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="bb5b2-385">Limitare i controlli integrità con MapWhen</span><span class="sxs-lookup"><span data-stu-id="bb5b2-385">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="bb5b2-386">Usare <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> per creare un ramo condizionale della pipeline delle richieste per gli endpoint di controllo integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-386">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="bb5b2-387">Nell'esempio seguente, `MapWhen` la pipeline della richiesta viene diramata per attivare il middleware dei controlli di integrità se viene ricevuta una richiesta GET per l' `api/HealthCheck` endpoint:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-387">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

<span data-ttu-id="bb5b2-388">Per altre informazioni, vedere <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-388">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bb5b2-389">ASP.NET Core offre middleware e librerie di controlli di integrità per segnalare l'integrità dei componenti dell'infrastruttura di app.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-389">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="bb5b2-390">I controlli di integrità vengono esposti da un'app come endpoint HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-390">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="bb5b2-391">È possibile configurare endpoint dei controlli di integrità per svariati scenari di monitoraggio in tempo reale:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-391">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="bb5b2-392">I probe di integrità possono essere usati dagli agenti di orchestrazione e dai servizi di bilanciamento del carico per controllare lo stato di un'app.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-392">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="bb5b2-393">Ad esempio, un agente di orchestrazione potrebbe rispondere a controllo di integrità non superato arrestando una distribuzione in sequenza o riavviando un contenitore.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-393">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="bb5b2-394">Un servizio di bilanciamento del carico potrebbe reagire a un'app non integra trasferendo il traffico dall'istanza con errori a un'istanza integra.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-394">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="bb5b2-395">È possibile monitorare lo stato di integrità di memoria, disco e altre risorse fisiche del server.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-395">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="bb5b2-396">I controlli di integrità possono testare le dipendenze di un'app, ad esempio i database e gli endpoint di servizio esterni, per verificare la disponibilità e il normale funzionamento.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-396">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="bb5b2-397">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bb5b2-397">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="bb5b2-398">L'app di esempio include esempi degli scenari descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-398">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="bb5b2-399">Per eseguire l'app di esempio per un determinato scenario, usare il comando [dotnet run](/dotnet/core/tools/dotnet-run) dalla cartella del progetto in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-399">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="bb5b2-400">Per informazioni dettagliate su come usare l'app di esempio, vedere il file *README.md* dell'app di esempio e le descrizioni degli scenari in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-400">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bb5b2-401">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="bb5b2-401">Prerequisites</span></span>

<span data-ttu-id="bb5b2-402">I controlli di integrità vengono in genere usati con un servizio di monitoraggio esterno o un agente di orchestrazione per controllare lo stato di un'app.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-402">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="bb5b2-403">Prima di aggiungere i controlli di integrità a un'app, decidere quale sistema di monitoraggio usare.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-403">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="bb5b2-404">Il sistema di monitoraggio determina quali tipi di controlli di integrità creare e come configurare i relativi endpoint.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-404">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="bb5b2-405">Fare riferimento al [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) oppure aggiungere un riferimento al pacchetto [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-405">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="bb5b2-406">L'app di esempio include il codice di avvio per illustrare i controlli di integrità per diversi scenari.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-406">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="bb5b2-407">Lo scenario di [probe del database](#database-probe) controlla l'integrità di una connessione di database usando [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-407">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="bb5b2-408">Lo scenario [Probe DbContext](#entity-framework-core-dbcontext-probe) verifica un database usando un elemento `DbContext` di EF Core.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-408">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="bb5b2-409">Per esplorare gli scenari relativi ai database, l'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-409">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="bb5b2-410">Crea un database e specifica la stringa di connessione nel file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-410">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="bb5b2-411">Dispone dei riferimenti al pacchetto seguenti nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-411">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="bb5b2-412">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="bb5b2-412">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="bb5b2-413">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="bb5b2-413">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="bb5b2-414">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) non è gestito né supportato da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-414">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="bb5b2-415">Un altro scenario relativo ai controlli di integrità illustra come filtrare i controlli di integrità per una porta di gestione.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-415">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="bb5b2-416">L'app di esempio richiede di creare un file *Properties/launchSettings.json* che include l'URL di gestione e la porta di gestione.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-416">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="bb5b2-417">Per altre informazioni, vedere la sezione [Filtrare in base alla porta](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-417">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="bb5b2-418">Probe di integrità di base</span><span class="sxs-lookup"><span data-stu-id="bb5b2-418">Basic health probe</span></span>

<span data-ttu-id="bb5b2-419">Per molte app, una configurazione del probe di integrità di base, che segnala la disponibilità dell'app per elaborare le richieste (*attività*), è sufficiente per individuare lo stato dell'app.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-419">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="bb5b2-420">La configurazione di base registra i servizi di controllo integrità e chiama il middleware dei controlli di integrità per rispondere a un endpoint URL con una risposta di integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-420">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="bb5b2-421">Per impostazione predefinita, non vengono registrati controlli di integrità specifici per testare particolari dipendenze o sottosistemi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-421">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="bb5b2-422">L'app viene considerata integra se riesce a rispondere all'URL dell'endpoint di integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-422">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="bb5b2-423">Il writer di risposta predefinito scrive lo stato (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) come risposta di testo non crittografato per il client, indicando uno stato [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) o [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-423">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="bb5b2-424">Registrare i servizi dei controlli di integrità con <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-424">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bb5b2-425">Aggiungere un endpoint per il middleware dei controlli di integrità con <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> nella pipeline di elaborazione della richiesta di `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-425">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="bb5b2-426">Nell'app di esempio l'endpoint di controllo di integrità viene creato in `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-426">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

<span data-ttu-id="bb5b2-427">Per eseguire lo scenario di configurazione di base usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-427">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="bb5b2-428">Esempio Docker</span><span class="sxs-lookup"><span data-stu-id="bb5b2-428">Docker example</span></span>

<span data-ttu-id="bb5b2-429">[Docker](xref:host-and-deploy/docker/index) offre una direttiva `HEALTHCHECK` predefinita che può essere usata per controllare lo stato di un'app che usa la configurazione dei controlli di integrità di base:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-429">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="bb5b2-430">Creare controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="bb5b2-430">Create health checks</span></span>

<span data-ttu-id="bb5b2-431">I controlli di integrità vengono creati implementando l'interfaccia <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-431">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="bb5b2-432">Il metodo <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> restituisce <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> che indica l'integrità come `Healthy`, `Degraded` o `Unhealthy`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-432">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="bb5b2-433">Il risultato viene scritto come risposta di testo non crittografato con un codice di stato configurabile. La configurazione viene descritta nella sezione [Opzioni dei controlli di integrità](#health-check-options).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-433">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="bb5b2-434"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> può anche restituire coppie chiave-valore facoltative.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-434"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="bb5b2-435">Controllo di integrità di esempio</span><span class="sxs-lookup"><span data-stu-id="bb5b2-435">Example health check</span></span>

<span data-ttu-id="bb5b2-436">Nella `ExampleHealthCheck` classe seguente viene illustrato il layout di un controllo di integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-436">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="bb5b2-437">La logica dei controlli di integrità viene inserita nel `CheckHealthAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-437">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="bb5b2-438">Nell'esempio seguente viene impostata una variabile fittizia, `healthCheckResultHealthy` , su `true` .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-438">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="bb5b2-439">Se il valore di `healthCheckResultHealthy` è impostato su `false` , viene restituito lo stato [HealthCheckResult. unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-439">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a><span data-ttu-id="bb5b2-440">Registrare i servizi di controllo dell'integrità</span><span class="sxs-lookup"><span data-stu-id="bb5b2-440">Register health check services</span></span>

<span data-ttu-id="bb5b2-441">Il `ExampleHealthCheck` tipo viene aggiunto ai servizi di controllo integrità in `Startup.ConfigureServices` con <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> :</span><span class="sxs-lookup"><span data-stu-id="bb5b2-441">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="bb5b2-442">L'overload <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> illustrato nell'esempio seguente imposta lo stato di errore (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) per segnalare quando il controllo di integrità indica un errore.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-442">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="bb5b2-443">Se lo stato di errore è impostato su `null` (impostazione predefinita), viene segnalato [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-443">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="bb5b2-444">Questo overload è uno scenario utile per gli autori di librerie, in cui lo stato di errore indicato dalla libreria viene applicato dall'app quando si verifica un errore di controllo dell'integrità se l'implementazione del controllo dell'integrità rispetta l'impostazione.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-444">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="bb5b2-445">Si possono usare *tag* per filtrare i controlli di integrità, come illustrato più avanti nella sezione [Filtrare i controlli di integrità](#filter-health-checks).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-445">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="bb5b2-446"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> può anche eseguire una funzione lambda.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-446"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="bb5b2-447">Nell'esempio seguente `Startup.ConfigureServices` il nome del controllo integrità viene specificato come `Example` e il controllo restituisce sempre uno stato integro:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-447">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="bb5b2-448">Usare il middleware per i controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="bb5b2-448">Use Health Checks Middleware</span></span>

<span data-ttu-id="bb5b2-449">In `Startup.Configure` chiamare <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> nella pipeline di elaborazione con l'URL dell'endpoint o il percorso relativo:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-449">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="bb5b2-450">Se i controlli di integrità devono essere in ascolto su una porta specifica, usare un overload di <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> per impostare la porta, come illustrato più avanti nella sezione [Filtrare in base alla porta](#filter-by-port):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-450">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="bb5b2-451">Opzioni dei controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="bb5b2-451">Health check options</span></span>

<span data-ttu-id="bb5b2-452"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> consente di personalizzare il comportamento dei controlli di integrità:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-452"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="bb5b2-453">Filtrare i controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="bb5b2-453">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="bb5b2-454">Personalizzare il codice di stato HTTP</span><span class="sxs-lookup"><span data-stu-id="bb5b2-454">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="bb5b2-455">Eliminare le intestazioni della cache</span><span class="sxs-lookup"><span data-stu-id="bb5b2-455">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="bb5b2-456">Personalizzare l'output</span><span class="sxs-lookup"><span data-stu-id="bb5b2-456">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="bb5b2-457">Filtrare i controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="bb5b2-457">Filter health checks</span></span>

<span data-ttu-id="bb5b2-458">Per impostazione predefinita, il middleware controlli integrità esegue tutti i controlli di integrità registrati.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-458">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="bb5b2-459">Per eseguire un subset dei controlli di integrità, specificare una funzione che restituisce un valore booleano all'opzione <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate>.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-459">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="bb5b2-460">Nell'esempio seguente il controllo di integrità `Bar` viene filtrato in base al tag (`bar_tag`) nell'istruzione condizionale della funzione, dove `true` viene restituito solo se la proprietà <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> del controllo di integrità corrisponde a `foo_tag` o a `baz_tag`:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-460">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="bb5b2-461">Personalizzare il codice di stato HTTP</span><span class="sxs-lookup"><span data-stu-id="bb5b2-461">Customize the HTTP status code</span></span>

<span data-ttu-id="bb5b2-462">Usare <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> per personalizzare il mapping dello stato di integrità dei codici di stato HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-462">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="bb5b2-463">Le assegnazioni <xref:Microsoft.AspNetCore.Http.StatusCodes> seguenti sono i valori predefiniti usati dal middleware.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-463">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="bb5b2-464">Modificare i valori dei codici di stato in base ai requisiti.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-464">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="bb5b2-465">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-465">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="bb5b2-466">Eliminare le intestazioni della cache</span><span class="sxs-lookup"><span data-stu-id="bb5b2-466">Suppress cache headers</span></span>

<span data-ttu-id="bb5b2-467"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Controlla se il middleware dei controlli di integrità aggiunge intestazioni HTTP a una risposta Probe per evitare la memorizzazione nella cache delle risposte.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-467"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="bb5b2-468">Se il valore è `false` (impostazione predefinita), il middleware imposta le intestazioni `Cache-Control`, `Expires` e `Pragma` o ne esegue l'override per impedire la memorizzazione della risposta nella cache.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-468">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="bb5b2-469">Se il valore è `true`, il middleware non modifica le intestazioni della risposta nella cache.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-469">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="bb5b2-470">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-470">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="bb5b2-471">Personalizzare l'output</span><span class="sxs-lookup"><span data-stu-id="bb5b2-471">Customize output</span></span>

<span data-ttu-id="bb5b2-472">L'opzione <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> ottiene o imposta un delegato usato per scrivere la risposta.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-472">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="bb5b2-473">Il delegato predefinito scrive una risposta in testo non crittografato minima con il valore di stringa [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-473">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="bb5b2-474">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-474">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="bb5b2-475">Il delegato predefinito scrive una risposta in testo non crittografato minima con il valore di stringa [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-475">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="bb5b2-476">Il delegato personalizzato seguente, `WriteResponse` , restituisce una risposta JSON personalizzata:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-476">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

<span data-ttu-id="bb5b2-477">Il sistema di controlli di integrità non fornisce il supporto incorporato per i formati restituiti JSON complessi, perché il formato è specifico del sistema di monitoraggio scelto.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-477">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="bb5b2-478">È possibile personalizzare nell' `JObject` esempio precedente, se necessario, per soddisfare le proprie esigenze.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-478">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="bb5b2-479">Probe del database</span><span class="sxs-lookup"><span data-stu-id="bb5b2-479">Database probe</span></span>

<span data-ttu-id="bb5b2-480">Un controllo di integrità può specificare una query di database da eseguire come test booleano per indicare se il database sta rispondendo normalmente.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-480">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="bb5b2-481">L'app di esempio usa [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), una libreria di controlli di integrità per app ASP.NET Core, per eseguire un controllo di integrità su un database di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-481">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="bb5b2-482">`AspNetCore.Diagnostics.HealthChecks` esegue una query `SELECT 1` sul database per confermare che la connessione al database sia integra.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-482">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="bb5b2-483">Quando si controlla una connessione di database con una query, scegliere una query che viene restituita rapidamente.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-483">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="bb5b2-484">Dall'approccio alla query possono derivare il sovraccarico del database e il degrado delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-484">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="bb5b2-485">Nella maggior parte dei casi, l'esecuzione di una query di test non è necessaria.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-485">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="bb5b2-486">È sufficiente stabilire una connessione al database.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-486">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="bb5b2-487">Se invece è necessario eseguire una query, scegliere una semplice query SELECT, ad esempio `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-487">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="bb5b2-488">Includere un riferimento al pacchetto [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-488">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="bb5b2-489">Fornire una stringa di connessione di database valida nel file *appsettings.json* dell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-489">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="bb5b2-490">L'app usa una database di SQL Server denominato `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-490">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="bb5b2-491">Registrare i servizi dei controlli di integrità con <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-491">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bb5b2-492">L'app di esempio chiama il metodo `AddSqlServer` di con la stringa di connessione del database (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-492">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="bb5b2-493">Chiamare il middleware verifica integrità nella pipeline di elaborazione delle app in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="bb5b2-493">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="bb5b2-494">Per eseguire lo scenario di probe del database di base usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-494">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="bb5b2-495">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) non è gestito né supportato da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-495">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="bb5b2-496">Probe DbContext di Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="bb5b2-496">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="bb5b2-497">Il controllo `DbContext` verifica che l'app possa comunicare con il database configurato per un `DbContext` di EF Core.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-497">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="bb5b2-498">Il controllo `DbContext` è supportato nelle app che:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-498">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="bb5b2-499">Usano [Entity Framework (EF) Core](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-499">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="bb5b2-500">Includono un riferimento del pacchetto a [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-500">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="bb5b2-501">`AddDbContextCheck<TContext>` registra un controllo di integrità per un `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-501">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="bb5b2-502">`DbContext` viene reso disponibile come `TContext` al metodo.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-502">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="bb5b2-503">È disponibile un overload per configurare lo stato di errore, i tag e una query di test personalizzata.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-503">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="bb5b2-504">Per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-504">By default:</span></span>

* <span data-ttu-id="bb5b2-505">`DbContextHealthCheck` chiama il metodo `CanConnectAsync` di EF Core.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-505">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="bb5b2-506">È possibile determinare l'operazione da eseguire quando si controlla l'integrità usando gli overload del metodo `AddDbContextCheck`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-506">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="bb5b2-507">Il nome del controllo di integrità è il nome del tipo `TContext`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-507">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="bb5b2-508">Nell'app di esempio, `AppDbContext` viene fornito a `AddDbContextCheck` e registrato come servizio in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-508">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="bb5b2-509">Nell'app di esempio, `UseHealthChecks` aggiunge il middleware dei controlli di integrità in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-509">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="bb5b2-510">Per eseguire lo scenario di probe `DbContext` usando l'app di esempio, verificare che il database specificato dalla stringa di connessione non esista nell'istanza di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-510">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="bb5b2-511">Se il database esiste, eliminarlo.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-511">If the database exists, delete it.</span></span>

<span data-ttu-id="bb5b2-512">Eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-512">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="bb5b2-513">Dopo che l'app è in esecuzione, controllare lo stato di integrità effettuando una richiesta all'endpoint `/health` in un browser.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-513">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="bb5b2-514">Il database e `AppDbContext` non esistono, quindi l'app fornisce la risposta seguente:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-514">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="bb5b2-515">Attivare l'app di esempio per creare il database.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-515">Trigger the sample app to create the database.</span></span> <span data-ttu-id="bb5b2-516">Effettuare una richiesta a `/createdatabase`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-516">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="bb5b2-517">L'app risponde:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-517">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="bb5b2-518">Effettuare una richiesta all'endpoint `/health`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-518">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="bb5b2-519">Il database e il contesto esistono, quindi l'app risponde:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-519">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="bb5b2-520">Attivare l'app di esempio per eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-520">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="bb5b2-521">Effettuare una richiesta a `/deletedatabase`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-521">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="bb5b2-522">L'app risponde:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-522">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="bb5b2-523">Effettuare una richiesta all'endpoint `/health`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-523">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="bb5b2-524">L'app fornisce una risposta non integra:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-524">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="bb5b2-525">Separare i probe di idoneità e di attività</span><span class="sxs-lookup"><span data-stu-id="bb5b2-525">Separate readiness and liveness probes</span></span>

<span data-ttu-id="bb5b2-526">In alcuni scenari di hosting vengono usati due controlli di integrità che distinguono due stati dell'app:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-526">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="bb5b2-527">L'app è funzionante, ma non ancora pronta a ricevere le richieste.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-527">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="bb5b2-528">Questo stato è l'*idoneità* dell'app.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-528">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="bb5b2-529">L'app è funzionante e risponde alle richieste.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-529">The app is functioning and responding to requests.</span></span> <span data-ttu-id="bb5b2-530">Questo stato è l'*attività* dell'app.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-530">This state is the app's *liveness*.</span></span>

<span data-ttu-id="bb5b2-531">Il controllo di idoneità in genere esegue un set di controlli più completo e dispendioso in termini di tempo per determinare se tutti i sottosistemi e le risorse dell'app sono disponibili.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-531">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="bb5b2-532">Un controllo di attività esegue semplicemente un rapido controllo per determinare se l'app è disponibile per elaborare le richieste.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-532">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="bb5b2-533">Dopo che l'app ha superato il controllo di idoneità, non è necessario caricare ulteriormente l'app con il dispendioso set di controlli di idoneità. Sarà sufficiente controllare solo l'attività.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-533">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="bb5b2-534">L'app di esempio contiene un controllo di integrità per segnalare il completamento dell'attività di avvio con esecuzione prolungata in un [servizio ospitato](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-534">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="bb5b2-535">`StartupHostedServiceHealthCheck` espone una proprietà, `StartupTaskCompleted`, che il servizio ospitato può impostare su `true` al termine dell'attività con esecuzione prolungata (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-535">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="bb5b2-536">L'attività in background con esecuzione prolungata viene avviata da un [servizio ospitato](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-536">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="bb5b2-537">Al termine dell'attività, `StartupHostedServiceHealthCheck.StartupTaskCompleted` viene impostata su `true`:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-537">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="bb5b2-538">Il controllo di integrità viene registrato con <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` insieme al servizio ospitato.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-538">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="bb5b2-539">Poiché il servizio ospitato deve impostare la proprietà sul controllo di integrità, il controllo di integrità viene anche registrato nel contenitore del servizio (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-539">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="bb5b2-540">Chiamare il middleware verifica integrità nella pipeline di elaborazione delle app in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-540">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="bb5b2-541">Nell'app di esempio gli endpoint dei controlli di integrità vengono creati in `/health/ready` per il controllo di idoneità e in `/health/live` per il controllo di attività.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-541">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="bb5b2-542">Il controllo di idoneità filtra i controlli di integrità per il controllo di integrità con il tag `ready`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-542">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="bb5b2-543">Il controllo di attività filtra `StartupHostedServiceHealthCheck` restituendo `false` in [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (per altre informazioni, vedere [Filtrare i controlli di integrità](#filter-health-checks)):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-543">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

<span data-ttu-id="bb5b2-544">Per eseguire lo scenario di configurazione di idoneità/attività usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-544">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="bb5b2-545">In a browser visitare più volte `/health/ready` per 15 secondi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-545">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="bb5b2-546">Il controllo di integrità segnala *Unhealthy* per i primi 15 secondi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-546">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="bb5b2-547">Dopo 15 secondi, l'endpoint segnala *Healthy*, che indica il completamento dell'attività con esecuzione prolungata da parte del servizio ospitato.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-547">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="bb5b2-548">Questo esempio crea anche un server di pubblicazione dei controlli di integrità (implementazione di <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>) che esegue il primo controllo di conformità con un ritardo di due secondi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-548">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="bb5b2-549">Per altre informazioni, vedere la sezione [Server di pubblicazione dei controlli di integrità](#health-check-publisher).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-549">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="bb5b2-550">Esempio di Kubernetes</span><span class="sxs-lookup"><span data-stu-id="bb5b2-550">Kubernetes example</span></span>

<span data-ttu-id="bb5b2-551">L'uso di controlli di idoneità e attività separati è utile in un ambiente come [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-551">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="bb5b2-552">In Kubernetes un'app potrebbe dover eseguire un'operazione di avvio con esecuzione prolungata prima di accettare le richieste, ad esempio un test della disponibilità del database sottostante.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-552">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="bb5b2-553">L'uso di controlli separati consente all'agente di orchestrazione di distinguere se l'app è funzionante, ma non è ancora pronta o se l'app non è stata avviata.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-553">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="bb5b2-554">Per altre informazioni sui probe di idoneità e di attività in Kubernetes, vedere [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) (Configurare i probe di attività e di idoneità in Kubernetes) nella documentazione di Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-554">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="bb5b2-555">L'esempio seguente illustra la configurazione di un probe di idoneità di Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-555">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="bb5b2-556">Probe basato sulle metriche con un writer di risposta personalizzata</span><span class="sxs-lookup"><span data-stu-id="bb5b2-556">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="bb5b2-557">L'app di esempio illustra un controllo di integrità della memoria con un writer di risposta personalizzata.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-557">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="bb5b2-558">`MemoryHealthCheck`segnala uno stato di tipo non integro se l'applicazione usa più di una determinata soglia di memoria (1 GB nell'app di esempio).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-558">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="bb5b2-559"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> include informazioni sul Garbage Collector (GC) per l'app (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-559">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="bb5b2-560">Registrare i servizi dei controlli di integrità con <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-560">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bb5b2-561">Invece di abilitare il controllo di integrità passandolo ad <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, `MemoryHealthCheck` viene registrato come servizio.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-561">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="bb5b2-562">Tutti i servizi registrati da <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> sono disponibili per il middleware e i servizi di controllo dell'integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-562">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="bb5b2-563">È consigliabile registrare i servizi di controllo dell'integrità come servizi Singleton.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-563">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="bb5b2-564">Nell'app di esempio (*CustomWriterStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-564">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="bb5b2-565">Chiamare il middleware verifica integrità nella pipeline di elaborazione delle app in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-565">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="bb5b2-566">Viene fornito un delegato `WriteResponse` alla proprietà `ResponseWriter` per visualizzare una risposta JSON personalizzata quando il controllo di integrità viene eseguito:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-566">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="bb5b2-567">Il metodo `WriteResponse` formatta `CompositeHealthCheckResult` in un oggetto JSON e genera l'output JSON per la risposta del controllo di integrità:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-567">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="bb5b2-568">Per eseguire lo scenario di probe basato sulle metriche con il writer di risposta personalizzata usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-568">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="bb5b2-569">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) include scenari di controllo dell'integrità basati su metriche, tra cui i controlli dell'archiviazione su disco e dell'attività dei valori massimi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-569">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="bb5b2-570">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) non è gestito né supportato da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-570">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="bb5b2-571">Filtrare in base alla porta</span><span class="sxs-lookup"><span data-stu-id="bb5b2-571">Filter by port</span></span>

<span data-ttu-id="bb5b2-572">La chiamata a <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> con una porta limita le richieste di controllo dell'integrità alla porta specificata.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-572">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="bb5b2-573">Questa chiamata viene in genere usata nell'ambiente di un contenitore per esporre una porta per il monitoraggio dei servizi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-573">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="bb5b2-574">L'app di esempio configura la porta usando il [provider di configurazione della variabile di ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-574">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="bb5b2-575">La porta viene impostata nel file *launchSettings.json* e passata al provider di configurazione tramite una variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-575">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="bb5b2-576">È anche necessario configurare il server per l'ascolto di richieste sulla porta di gestione.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-576">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="bb5b2-577">Per usare l'app di esempio per illustrare la configurazione della porta di gestione, creare il file *launchSettings.json* in una cartella *Properties*.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-577">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="bb5b2-578">Il file *Properties/launchSettings. JSON* seguente nell'app di esempio non è incluso nei file di progetto dell'app di esempio e deve essere creato manualmente:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-578">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="bb5b2-579">Registrare i servizi dei controlli di integrità con <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-579">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bb5b2-580">La chiamata a <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifica la porta di gestione (*ManagementPortStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-580">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="bb5b2-581">È possibile evitare di creare il file *launchSettings.json* nell'app di esempio impostando gli URL e la porta di gestione in modo esplicito nel codice.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-581">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="bb5b2-582">In *Program.cs*, dove viene creato <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>, aggiungere una chiamata a <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> e fornire l'endpoint della porta di gestione e l'endpoint di risposta normale dell'app.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-582">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="bb5b2-583">In *ManagementPortStartup.cs*, dove viene chiamato <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>, specificare la porta di gestione in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-583">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="bb5b2-584">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-584">*Program.cs*:</span></span>
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> <span data-ttu-id="bb5b2-585">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-585">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="bb5b2-586">Per eseguire lo scenario di configurazione della porta di gestione usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-586">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="bb5b2-587">Distribuire una libreria di controllo di integrità</span><span class="sxs-lookup"><span data-stu-id="bb5b2-587">Distribute a health check library</span></span>

<span data-ttu-id="bb5b2-588">Per distribuire un controllo di integrità come libreria:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-588">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="bb5b2-589">Scrivere un controllo di integrità che implementa l'interfaccia <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> come classe autonoma.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-589">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="bb5b2-590">La classe può basarsi su [inserimento delle dipendenze](xref:fundamentals/dependency-injection), attivazione del tipo e [opzioni denominate](xref:fundamentals/configuration/options) per accedere ai dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-590">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="bb5b2-591">Nella logica dei controlli di integrità di `CheckHealthAsync` :</span><span class="sxs-lookup"><span data-stu-id="bb5b2-591">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="bb5b2-592">`data1`e `data2` vengono usati nel metodo per eseguire la logica del controllo di integrità del probe.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-592">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="bb5b2-593">`AccessViolationException`viene gestito.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-593">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="bb5b2-594">Quando <xref:System.AccessViolationException> si verifica un <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> errore, viene restituito con <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> per consentire agli utenti di configurare lo stato di errore dei controlli di integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-594">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public class ExampleHealthCheck : IHealthCheck
   {
       private readonly string _data1;
       private readonly int? _data2;

       public ExampleHealthCheck(string data1, int? data2)
       {
           _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
           _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
       }

       public async Task<HealthCheckResult> CheckHealthAsync(
           HealthCheckContext context, CancellationToken cancellationToken)
       {
           try
           {
               return HealthCheckResult.Healthy();
           }
           catch (AccessViolationException ex)
           {
               return new HealthCheckResult(
                   context.Registration.FailureStatus,
                   description: "An access violation occurred during the check.",
                   exception: ex,
                   data: null);
           }
       }
   }
   ```

1. <span data-ttu-id="bb5b2-595">Scrivere un metodo di estensione con i parametri che l'app chiama nel metodo `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-595">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="bb5b2-596">Nell'esempio seguente si presuma la firma del metodo di controllo dell'integrità seguente:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-596">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="bb5b2-597">La firma precedente indica che `ExampleHealthCheck` richiede dati aggiuntivi per elaborare la logica del probe di controllo dell'integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-597">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="bb5b2-598">I dati vengono forniti al delegato usato per creare l'istanza del controllo di integrità quando il controllo di integrità viene registrato con un metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-598">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="bb5b2-599">Nell'esempio seguente il chiamante specifica facoltativamente:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-599">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="bb5b2-600">nome del controllo integrità (`name`).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-600">health check name (`name`).</span></span> <span data-ttu-id="bb5b2-601">Se `null`, viene usato `example_health_check`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-601">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="bb5b2-602">punto dati stringa per il controllo di integrità (`data1`).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-602">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="bb5b2-603">punto dati Integer per il controllo di integrità (`data2`).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-603">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="bb5b2-604">Se `null`, viene usato `1`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-604">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="bb5b2-605">stato dell'errore (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-605">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="bb5b2-606">Il valore predefinito è `null`.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-606">The default is `null`.</span></span> <span data-ttu-id="bb5b2-607">Se `null`, viene segnalato [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) per uno stato di errore.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-607">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="bb5b2-608">tag (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-608">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="bb5b2-609">Server di pubblicazione dei controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="bb5b2-609">Health Check Publisher</span></span>

<span data-ttu-id="bb5b2-610">Quando <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> viene aggiunto al contenitore del servizio, il sistema di controllo dell'integrità esegue periodicamente i controlli di integrità e chiama `PublishAsync` con il risultato.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-610">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="bb5b2-611">Ciò è utile in uno scenario relativo a un sistema di monitoraggio dell'integrità basato su push, che prevede che ogni processo chiami periodicamente il sistema di monitoraggio per determinare l'integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-611">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="bb5b2-612">L'interfaccia <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> ha un solo metodo:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-612">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="bb5b2-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> consente di impostare:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="bb5b2-614"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash;Il ritardo iniziale applicato dopo l'avvio dell'app prima dell'esecuzione di <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> istanze.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-614"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="bb5b2-615">Il ritardo viene applicato una sola volta all'avvio e non si applica alle iterazioni successive.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-615">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="bb5b2-616">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-616">The default value is five seconds.</span></span>
* <span data-ttu-id="bb5b2-617"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash;Periodo di <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> esecuzione.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-617"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="bb5b2-618">Il valore predefinito è 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-618">The default value is 30 seconds.</span></span>
* <span data-ttu-id="bb5b2-619"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash;Se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> è `null` (impostazione predefinita), il servizio di pubblicazione controllo integrità esegue tutti i controlli di integrità registrati.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-619"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="bb5b2-620">Per eseguire un subset dei controlli di integrità, specificare una funzione che filtra il set di controlli.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-620">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="bb5b2-621">Il predicato viene valutato per ogni periodo.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-621">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="bb5b2-622"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash;Timeout per l'esecuzione dei controlli di integrità per tutte le <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> istanze.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-622"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="bb5b2-623">Usare <xref:System.Threading.Timeout.InfiniteTimeSpan> per l'esecuzione senza un timeout.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-623">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="bb5b2-624">Il valore predefinito è 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-624">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="bb5b2-625">Nella versione 2.2 di ASP.NET Core, l'impostazione <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> non viene rispettata dall'implementazione <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Viene impostato il valore <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-625">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="bb5b2-626">Questo problema è stato risolto in ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-626">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="bb5b2-627">Nell'app di esempio, `ReadinessPublisher` è un'implementazione di <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-627">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="bb5b2-628">Lo stato di controllo integrità viene registrato per ogni controllo come:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-628">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="bb5b2-629">Information ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> ) se lo stato dei controlli di integrità è <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-629">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="bb5b2-630">Error ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*> ) se lo stato è <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> o <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> .</span><span class="sxs-lookup"><span data-stu-id="bb5b2-630">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="bb5b2-631">Nell'esempio di `LivenessProbeStartup` dell'app di esempio, il controllo di conformità `StartupHostedService` ha un ritardo di avvio di due secondi e il controllo viene eseguito ogni 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-631">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="bb5b2-632">Per attivare l'implementazione <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, l'esempio registra `ReadinessPublisher` come servizio singleton nel contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="bb5b2-632">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="bb5b2-633">La soluzione alternativa seguente consente di aggiungere un'istanza <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> al contenitore del servizio quando uno o più servizi ospitati sono già stati aggiunti all'app.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-633">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="bb5b2-634">Questa soluzione alternativa non è necessaria in ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-634">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

> [!NOTE]
> <span data-ttu-id="bb5b2-635">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) include i server di pubblicazione per diversi sistemi, tra cui [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="bb5b2-635">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="bb5b2-636">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) non è gestito né supportato da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-636">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="bb5b2-637">Limitare i controlli integrità con MapWhen</span><span class="sxs-lookup"><span data-stu-id="bb5b2-637">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="bb5b2-638">Usare <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> per creare un ramo condizionale della pipeline delle richieste per gli endpoint di controllo integrità.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-638">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="bb5b2-639">Nell'esempio seguente, `MapWhen` la pipeline della richiesta viene diramata per attivare il middleware dei controlli di integrità se viene ricevuta una richiesta GET per l' `api/HealthCheck` endpoint:</span><span class="sxs-lookup"><span data-stu-id="bb5b2-639">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="bb5b2-640">Per altre informazioni, vedere <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="bb5b2-640">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
