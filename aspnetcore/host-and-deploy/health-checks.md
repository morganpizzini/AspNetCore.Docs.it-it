---
title: Controlli di integrità in ASP.NET Core
author: rick-anderson
description: Informazioni su come configurare i controlli di integrità per l'infrastruttura ASP.NET Core, ad esempio database e app.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/health-checks
ms.openlocfilehash: cb3ee4f3bf9061d212c1fee85f3f4a22946be097
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84105779"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="2ec57-103">Controlli di integrità in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2ec57-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="2ec57-104">Di [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="2ec57-104">By [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2ec57-105">ASP.NET Core offre middleware e librerie di controlli di integrità per segnalare l'integrità dei componenti dell'infrastruttura di app.</span><span class="sxs-lookup"><span data-stu-id="2ec57-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="2ec57-106">I controlli di integrità vengono esposti da un'app come endpoint HTTP.</span><span class="sxs-lookup"><span data-stu-id="2ec57-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="2ec57-107">È possibile configurare endpoint dei controlli di integrità per svariati scenari di monitoraggio in tempo reale:</span><span class="sxs-lookup"><span data-stu-id="2ec57-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="2ec57-108">I probe di integrità possono essere usati dagli agenti di orchestrazione e dai servizi di bilanciamento del carico per controllare lo stato di un'app.</span><span class="sxs-lookup"><span data-stu-id="2ec57-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="2ec57-109">Ad esempio, un agente di orchestrazione potrebbe rispondere a controllo di integrità non superato arrestando una distribuzione in sequenza o riavviando un contenitore.</span><span class="sxs-lookup"><span data-stu-id="2ec57-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="2ec57-110">Un servizio di bilanciamento del carico potrebbe reagire a un'app non integra trasferendo il traffico dall'istanza con errori a un'istanza integra.</span><span class="sxs-lookup"><span data-stu-id="2ec57-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="2ec57-111">È possibile monitorare lo stato di integrità di memoria, disco e altre risorse fisiche del server.</span><span class="sxs-lookup"><span data-stu-id="2ec57-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="2ec57-112">I controlli di integrità possono testare le dipendenze di un'app, ad esempio i database e gli endpoint di servizio esterni, per verificare la disponibilità e il normale funzionamento.</span><span class="sxs-lookup"><span data-stu-id="2ec57-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="2ec57-113">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2ec57-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2ec57-114">L'app di esempio include esempi degli scenari descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="2ec57-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="2ec57-115">Per eseguire l'app di esempio per un determinato scenario, usare il comando [dotnet run](/dotnet/core/tools/dotnet-run) dalla cartella del progetto in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="2ec57-116">Per informazioni dettagliate su come usare l'app di esempio, vedere il file *README.md* dell'app di esempio e le descrizioni degli scenari in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="2ec57-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2ec57-117">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="2ec57-117">Prerequisites</span></span>

<span data-ttu-id="2ec57-118">I controlli di integrità vengono in genere usati con un servizio di monitoraggio esterno o un agente di orchestrazione per controllare lo stato di un'app.</span><span class="sxs-lookup"><span data-stu-id="2ec57-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="2ec57-119">Prima di aggiungere i controlli di integrità a un'app, decidere quale sistema di monitoraggio usare.</span><span class="sxs-lookup"><span data-stu-id="2ec57-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="2ec57-120">Il sistema di monitoraggio determina quali tipi di controlli di integrità creare e come configurare i relativi endpoint.</span><span class="sxs-lookup"><span data-stu-id="2ec57-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="2ec57-121">Si fa riferimento al pacchetto [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) in modo implicito per le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2ec57-121">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="2ec57-122">Per eseguire i controlli di integrità usando Entity Framework Core, aggiungere un riferimento al pacchetto [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="2ec57-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="2ec57-123">L'app di esempio include il codice di avvio per illustrare i controlli di integrità per diversi scenari.</span><span class="sxs-lookup"><span data-stu-id="2ec57-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="2ec57-124">Lo scenario di [probe del database](#database-probe) controlla l'integrità di una connessione di database usando [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="2ec57-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="2ec57-125">Lo scenario [Probe DbContext](#entity-framework-core-dbcontext-probe) verifica un database usando un elemento `DbContext` di EF Core.</span><span class="sxs-lookup"><span data-stu-id="2ec57-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="2ec57-126">Per esplorare gli scenari relativi ai database, l'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="2ec57-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="2ec57-127">Crea un database e specifica la stringa di connessione nel file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2ec57-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="2ec57-128">Dispone dei riferimenti al pacchetto seguenti nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="2ec57-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="2ec57-129">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="2ec57-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="2ec57-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="2ec57-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="2ec57-131">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) non è gestito né supportato da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2ec57-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="2ec57-132">Un altro scenario relativo ai controlli di integrità illustra come filtrare i controlli di integrità per una porta di gestione.</span><span class="sxs-lookup"><span data-stu-id="2ec57-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="2ec57-133">L'app di esempio richiede di creare un file *Properties/launchSettings.json* che include l'URL di gestione e la porta di gestione.</span><span class="sxs-lookup"><span data-stu-id="2ec57-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="2ec57-134">Per altre informazioni, vedere la sezione [Filtrare in base alla porta](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="2ec57-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="2ec57-135">Probe di integrità di base</span><span class="sxs-lookup"><span data-stu-id="2ec57-135">Basic health probe</span></span>

<span data-ttu-id="2ec57-136">Per molte app, una configurazione del probe di integrità di base, che segnala la disponibilità dell'app per elaborare le richieste (*attività*), è sufficiente per individuare lo stato dell'app.</span><span class="sxs-lookup"><span data-stu-id="2ec57-136">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="2ec57-137">La configurazione di base registra i servizi di controllo integrità e chiama il middleware dei controlli di integrità per rispondere a un endpoint URL con una risposta di integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="2ec57-138">Per impostazione predefinita, non vengono registrati controlli di integrità specifici per testare particolari dipendenze o sottosistemi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="2ec57-139">L'app viene considerata integra se riesce a rispondere all'URL dell'endpoint di integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="2ec57-140">Il writer di risposta predefinito scrive lo stato (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) come risposta di testo non crittografato per il client, indicando uno stato [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) o [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).</span><span class="sxs-lookup"><span data-stu-id="2ec57-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="2ec57-141">Registrare i servizi dei controlli di integrità con <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2ec57-142">Creare un endpoint di controllo integrità chiamando `MapHealthChecks` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2ec57-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="2ec57-143">Nell'app di esempio l'endpoint di controllo di integrità viene creato in `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="2ec57-143">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="2ec57-144">Per eseguire lo scenario di configurazione di base usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="2ec57-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="2ec57-145">Esempio Docker</span><span class="sxs-lookup"><span data-stu-id="2ec57-145">Docker example</span></span>

<span data-ttu-id="2ec57-146">[Docker](xref:host-and-deploy/docker/index) offre una direttiva `HEALTHCHECK` predefinita che può essere usata per controllare lo stato di un'app che usa la configurazione dei controlli di integrità di base:</span><span class="sxs-lookup"><span data-stu-id="2ec57-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="2ec57-147">Creare controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="2ec57-147">Create health checks</span></span>

<span data-ttu-id="2ec57-148">I controlli di integrità vengono creati implementando l'interfaccia <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>.</span><span class="sxs-lookup"><span data-stu-id="2ec57-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="2ec57-149">Il metodo <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> restituisce <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> che indica l'integrità come `Healthy`, `Degraded` o `Unhealthy`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="2ec57-150">Il risultato viene scritto come risposta di testo non crittografato con un codice di stato configurabile. La configurazione viene descritta nella sezione [Opzioni dei controlli di integrità](#health-check-options).</span><span class="sxs-lookup"><span data-stu-id="2ec57-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="2ec57-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> può anche restituire coppie chiave-valore facoltative.</span><span class="sxs-lookup"><span data-stu-id="2ec57-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="2ec57-152">Nella `ExampleHealthCheck` classe seguente viene illustrato il layout di un controllo di integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="2ec57-153">La logica dei controlli di integrità viene inserita nel `CheckHealthAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="2ec57-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="2ec57-154">Nell'esempio seguente viene impostata una variabile fittizia, `healthCheckResultHealthy` , su `true` .</span><span class="sxs-lookup"><span data-stu-id="2ec57-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="2ec57-155">Se il valore di `healthCheckResultHealthy` è impostato su `false` , viene restituito lo stato [HealthCheckResult. unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) .</span><span class="sxs-lookup"><span data-stu-id="2ec57-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

## <a name="register-health-check-services"></a><span data-ttu-id="2ec57-156">Registrare i servizi di controllo dell'integrità</span><span class="sxs-lookup"><span data-stu-id="2ec57-156">Register health check services</span></span>

<span data-ttu-id="2ec57-157">Il `ExampleHealthCheck` tipo viene aggiunto ai servizi di controllo integrità con <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2ec57-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="2ec57-158">L'overload <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> illustrato nell'esempio seguente imposta lo stato di errore (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) per segnalare quando il controllo di integrità indica un errore.</span><span class="sxs-lookup"><span data-stu-id="2ec57-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="2ec57-159">Se lo stato di errore è impostato su `null` (impostazione predefinita), viene segnalato [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).</span><span class="sxs-lookup"><span data-stu-id="2ec57-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="2ec57-160">Questo overload è uno scenario utile per gli autori di librerie, in cui lo stato di errore indicato dalla libreria viene applicato dall'app quando si verifica un errore di controllo dell'integrità se l'implementazione del controllo dell'integrità rispetta l'impostazione.</span><span class="sxs-lookup"><span data-stu-id="2ec57-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="2ec57-161">Si possono usare *tag* per filtrare i controlli di integrità, come illustrato più avanti nella sezione [Filtrare i controlli di integrità](#filter-health-checks).</span><span class="sxs-lookup"><span data-stu-id="2ec57-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="2ec57-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> può anche eseguire una funzione lambda.</span><span class="sxs-lookup"><span data-stu-id="2ec57-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="2ec57-163">Nell'esempio seguente il nome del controllo di integrità viene specificato come `Example` e il controllo restituisce sempre uno stato integro:</span><span class="sxs-lookup"><span data-stu-id="2ec57-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="2ec57-164">Chiamare <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> per passare argomenti a un'implementazione del controllo integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-164">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arguments to a health check implementation.</span></span> <span data-ttu-id="2ec57-165">Nell'esempio seguente `TestHealthCheckWithArgs` accetta un numero intero e una stringa da usare quando <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> viene chiamato il metodo:</span><span class="sxs-lookup"><span data-stu-id="2ec57-165">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

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

<span data-ttu-id="2ec57-166">`TestHealthCheckWithArgs`viene registrato chiamando `AddTypeActivatedCheck` con il valore integer e la stringa passati all'implementazione:</span><span class="sxs-lookup"><span data-stu-id="2ec57-166">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="2ec57-167">Usare i controlli di integrità routing</span><span class="sxs-lookup"><span data-stu-id="2ec57-167">Use Health Checks Routing</span></span>

<span data-ttu-id="2ec57-168">In `Startup.Configure` , chiamare `MapHealthChecks` sul generatore di endpoint con l'URL dell'endpoint o il percorso relativo:</span><span class="sxs-lookup"><span data-stu-id="2ec57-168">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="2ec57-169">Richiedi host</span><span class="sxs-lookup"><span data-stu-id="2ec57-169">Require host</span></span>

<span data-ttu-id="2ec57-170">Chiamare `RequireHost` per specificare uno o più host consentiti per l'endpoint di controllo integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-170">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="2ec57-171">Gli host devono essere Unicode anziché Punycode e possono includere una porta.</span><span class="sxs-lookup"><span data-stu-id="2ec57-171">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="2ec57-172">Se non viene specificata una raccolta, viene accettato qualsiasi host.</span><span class="sxs-lookup"><span data-stu-id="2ec57-172">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="2ec57-173">Per altre informazioni, vedere la sezione [Filtrare in base alla porta](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="2ec57-173">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="2ec57-174">Richiedi autorizzazione</span><span class="sxs-lookup"><span data-stu-id="2ec57-174">Require authorization</span></span>

<span data-ttu-id="2ec57-175">Chiamare `RequireAuthorization` per eseguire il middleware di autorizzazione sull'endpoint della richiesta di controllo integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-175">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="2ec57-176">Un `RequireAuthorization` Overload accetta uno o più criteri di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="2ec57-176">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="2ec57-177">Se un criterio non viene specificato, vengono usati i criteri di autorizzazione predefiniti.</span><span class="sxs-lookup"><span data-stu-id="2ec57-177">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="2ec57-178">Abilitare richieste tra le origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="2ec57-178">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="2ec57-179">Sebbene i controlli di integrità eseguiti manualmente da un browser non siano uno scenario di utilizzo comune, il middleware CORS può essere abilitato chiamando `RequireCors` sugli endpoint di controllo dell'integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-179">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="2ec57-180">Un `RequireCors` Overload accetta un delegato del generatore di criteri CORS ( `CorsPolicyBuilder` ) o un nome di criterio.</span><span class="sxs-lookup"><span data-stu-id="2ec57-180">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="2ec57-181">Se non viene specificato alcun criterio, viene usato il criterio CORS predefinito.</span><span class="sxs-lookup"><span data-stu-id="2ec57-181">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="2ec57-182">Per altre informazioni, vedere <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="2ec57-182">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="2ec57-183">Opzioni dei controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="2ec57-183">Health check options</span></span>

<span data-ttu-id="2ec57-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> consente di personalizzare il comportamento dei controlli di integrità:</span><span class="sxs-lookup"><span data-stu-id="2ec57-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="2ec57-185">Filtrare i controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="2ec57-185">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="2ec57-186">Personalizzare il codice di stato HTTP</span><span class="sxs-lookup"><span data-stu-id="2ec57-186">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="2ec57-187">Eliminare le intestazioni della cache</span><span class="sxs-lookup"><span data-stu-id="2ec57-187">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="2ec57-188">Personalizzare l'output</span><span class="sxs-lookup"><span data-stu-id="2ec57-188">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="2ec57-189">Filtrare i controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="2ec57-189">Filter health checks</span></span>

<span data-ttu-id="2ec57-190">Per impostazione predefinita, il middleware controlli integrità esegue tutti i controlli di integrità registrati.</span><span class="sxs-lookup"><span data-stu-id="2ec57-190">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="2ec57-191">Per eseguire un subset dei controlli di integrità, specificare una funzione che restituisce un valore booleano all'opzione <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate>.</span><span class="sxs-lookup"><span data-stu-id="2ec57-191">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="2ec57-192">Nell'esempio seguente il controllo di integrità `Bar` viene filtrato in base al tag (`bar_tag`) nell'istruzione condizionale della funzione, dove `true` viene restituito solo se la proprietà <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> del controllo di integrità corrisponde a `foo_tag` o a `baz_tag`:</span><span class="sxs-lookup"><span data-stu-id="2ec57-192">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="2ec57-193">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2ec57-193">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="2ec57-194">In `Startup.Configure` il `Predicate` controllo integrità della barra è filtrato.</span><span class="sxs-lookup"><span data-stu-id="2ec57-194">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="2ec57-195">Solo foo e Baz Execute.:</span><span class="sxs-lookup"><span data-stu-id="2ec57-195">Only Foo and Baz execute.:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="2ec57-196">Personalizzare il codice di stato HTTP</span><span class="sxs-lookup"><span data-stu-id="2ec57-196">Customize the HTTP status code</span></span>

<span data-ttu-id="2ec57-197">Usare <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> per personalizzare il mapping dello stato di integrità dei codici di stato HTTP.</span><span class="sxs-lookup"><span data-stu-id="2ec57-197">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="2ec57-198">Le assegnazioni <xref:Microsoft.AspNetCore.Http.StatusCodes> seguenti sono i valori predefiniti usati dal middleware.</span><span class="sxs-lookup"><span data-stu-id="2ec57-198">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="2ec57-199">Modificare i valori dei codici di stato in base ai requisiti.</span><span class="sxs-lookup"><span data-stu-id="2ec57-199">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="2ec57-200">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2ec57-200">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="2ec57-201">Eliminare le intestazioni della cache</span><span class="sxs-lookup"><span data-stu-id="2ec57-201">Suppress cache headers</span></span>

<span data-ttu-id="2ec57-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Controlla se il middleware dei controlli di integrità aggiunge intestazioni HTTP a una risposta Probe per evitare la memorizzazione nella cache delle risposte.</span><span class="sxs-lookup"><span data-stu-id="2ec57-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="2ec57-203">Se il valore è `false` (impostazione predefinita), il middleware imposta le intestazioni `Cache-Control`, `Expires` e `Pragma` o ne esegue l'override per impedire la memorizzazione della risposta nella cache.</span><span class="sxs-lookup"><span data-stu-id="2ec57-203">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="2ec57-204">Se il valore è `true`, il middleware non modifica le intestazioni della risposta nella cache.</span><span class="sxs-lookup"><span data-stu-id="2ec57-204">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="2ec57-205">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2ec57-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="2ec57-206">Personalizzare l'output</span><span class="sxs-lookup"><span data-stu-id="2ec57-206">Customize output</span></span>

<span data-ttu-id="2ec57-207">In `Startup.Configure` impostare l'opzione [HealthCheckOptions. ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) su un delegato per la scrittura della risposta:</span><span class="sxs-lookup"><span data-stu-id="2ec57-207">In `Startup.Configure`, set the [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) option to a delegate for writing the response:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="2ec57-208">Il delegato predefinito scrive una risposta in testo non crittografato minima con il valore di stringa [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="2ec57-208">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="2ec57-209">I seguenti delegati personalizzati restituiscono una risposta JSON personalizzata.</span><span class="sxs-lookup"><span data-stu-id="2ec57-209">The following custom delegates output a custom JSON response.</span></span>

<span data-ttu-id="2ec57-210">Il primo esempio dell'app di esempio illustra come usare <xref:System.Text.Json?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="2ec57-210">The first example from the sample app demonstrates how to use <xref:System.Text.Json?displayProperty=fullName>:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

<span data-ttu-id="2ec57-211">Nel secondo esempio viene illustrato come usare [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/):</span><span class="sxs-lookup"><span data-stu-id="2ec57-211">The second example demonstrates how to use [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

<span data-ttu-id="2ec57-212">Nell'app di esempio impostare come commento la `SYSTEM_TEXT_JSON` [direttiva per il preprocessore](xref:index#preprocessor-directives-in-sample-code) in *CustomWriterStartup.cs* per abilitare la `Newtonsoft.Json` versione di `WriteResponse` .</span><span class="sxs-lookup"><span data-stu-id="2ec57-212">In the sample app, comment out the `SYSTEM_TEXT_JSON` [preprocessor directive](xref:index#preprocessor-directives-in-sample-code) in *CustomWriterStartup.cs* to enable the `Newtonsoft.Json` version of `WriteResponse`.</span></span>

<span data-ttu-id="2ec57-213">L'API controlli di integrità non fornisce supporto incorporato per i formati restituiti JSON complessi, perché il formato è specifico del sistema di monitoraggio scelto.</span><span class="sxs-lookup"><span data-stu-id="2ec57-213">The health checks API doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="2ec57-214">Personalizzare la risposta negli esempi precedenti in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="2ec57-214">Customize the response in the preceding examples as needed.</span></span> <span data-ttu-id="2ec57-215">Per altre informazioni sulla serializzazione JSON con `System.Text.Json` , vedere [come serializzare e deserializzare JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span><span class="sxs-lookup"><span data-stu-id="2ec57-215">For more information on JSON serialization with `System.Text.Json`, see [How to serialize and deserialize JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span></span>

## <a name="database-probe"></a><span data-ttu-id="2ec57-216">Probe del database</span><span class="sxs-lookup"><span data-stu-id="2ec57-216">Database probe</span></span>

<span data-ttu-id="2ec57-217">Un controllo di integrità può specificare una query di database da eseguire come test booleano per indicare se il database sta rispondendo normalmente.</span><span class="sxs-lookup"><span data-stu-id="2ec57-217">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="2ec57-218">L'app di esempio usa [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), una libreria di controlli di integrità per app ASP.NET Core, per eseguire un controllo di integrità su un database di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="2ec57-218">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="2ec57-219">`AspNetCore.Diagnostics.HealthChecks` esegue una query `SELECT 1` sul database per confermare che la connessione al database sia integra.</span><span class="sxs-lookup"><span data-stu-id="2ec57-219">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="2ec57-220">Quando si controlla una connessione di database con una query, scegliere una query che viene restituita rapidamente.</span><span class="sxs-lookup"><span data-stu-id="2ec57-220">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="2ec57-221">Dall'approccio alla query possono derivare il sovraccarico del database e il degrado delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="2ec57-221">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="2ec57-222">Nella maggior parte dei casi, l'esecuzione di una query di test non è necessaria.</span><span class="sxs-lookup"><span data-stu-id="2ec57-222">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="2ec57-223">È sufficiente stabilire una connessione al database.</span><span class="sxs-lookup"><span data-stu-id="2ec57-223">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="2ec57-224">Se invece è necessario eseguire una query, scegliere una semplice query SELECT, ad esempio `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-224">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="2ec57-225">Includere un riferimento al pacchetto [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="2ec57-225">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="2ec57-226">Fornire una stringa di connessione di database valida nel file *appsettings.json* dell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="2ec57-226">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="2ec57-227">L'app usa una database di SQL Server denominato `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="2ec57-227">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="2ec57-228">Registrare i servizi dei controlli di integrità con <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-228">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2ec57-229">L'app di esempio chiama il metodo `AddSqlServer` di con la stringa di connessione del database (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="2ec57-229">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="2ec57-230">Un endpoint di controllo dell'integrità viene creato chiamando `MapHealthChecks` in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="2ec57-230">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="2ec57-231">Per eseguire lo scenario di probe del database di base usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="2ec57-231">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="2ec57-232">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) non è gestito né supportato da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2ec57-232">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="2ec57-233">Probe DbContext di Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="2ec57-233">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="2ec57-234">Il controllo `DbContext` verifica che l'app possa comunicare con il database configurato per un `DbContext` di EF Core.</span><span class="sxs-lookup"><span data-stu-id="2ec57-234">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="2ec57-235">Il controllo `DbContext` è supportato nelle app che:</span><span class="sxs-lookup"><span data-stu-id="2ec57-235">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="2ec57-236">Usano [Entity Framework (EF) Core](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="2ec57-236">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="2ec57-237">Includono un riferimento del pacchetto a [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="2ec57-237">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="2ec57-238">`AddDbContextCheck<TContext>` registra un controllo di integrità per un `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-238">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="2ec57-239">`DbContext` viene reso disponibile come `TContext` al metodo.</span><span class="sxs-lookup"><span data-stu-id="2ec57-239">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="2ec57-240">È disponibile un overload per configurare lo stato di errore, i tag e una query di test personalizzata.</span><span class="sxs-lookup"><span data-stu-id="2ec57-240">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="2ec57-241">Per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="2ec57-241">By default:</span></span>

* <span data-ttu-id="2ec57-242">`DbContextHealthCheck` chiama il metodo `CanConnectAsync` di EF Core.</span><span class="sxs-lookup"><span data-stu-id="2ec57-242">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="2ec57-243">È possibile determinare l'operazione da eseguire quando si controlla l'integrità usando gli overload del metodo `AddDbContextCheck`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-243">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="2ec57-244">Il nome del controllo di integrità è il nome del tipo `TContext`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-244">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="2ec57-245">Nell'app di esempio, `AppDbContext` viene fornito a `AddDbContextCheck` e registrato come servizio in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="2ec57-245">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="2ec57-246">Un endpoint di controllo dell'integrità viene creato chiamando `MapHealthChecks` in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="2ec57-246">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="2ec57-247">Per eseguire lo scenario di probe `DbContext` usando l'app di esempio, verificare che il database specificato dalla stringa di connessione non esista nell'istanza di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="2ec57-247">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="2ec57-248">Se il database esiste, eliminarlo.</span><span class="sxs-lookup"><span data-stu-id="2ec57-248">If the database exists, delete it.</span></span>

<span data-ttu-id="2ec57-249">Eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="2ec57-249">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="2ec57-250">Dopo che l'app è in esecuzione, controllare lo stato di integrità effettuando una richiesta all'endpoint `/health` in un browser.</span><span class="sxs-lookup"><span data-stu-id="2ec57-250">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="2ec57-251">Il database e `AppDbContext` non esistono, quindi l'app fornisce la risposta seguente:</span><span class="sxs-lookup"><span data-stu-id="2ec57-251">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="2ec57-252">Attivare l'app di esempio per creare il database.</span><span class="sxs-lookup"><span data-stu-id="2ec57-252">Trigger the sample app to create the database.</span></span> <span data-ttu-id="2ec57-253">Effettuare una richiesta a `/createdatabase`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-253">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="2ec57-254">L'app risponde:</span><span class="sxs-lookup"><span data-stu-id="2ec57-254">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="2ec57-255">Effettuare una richiesta all'endpoint `/health`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-255">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="2ec57-256">Il database e il contesto esistono, quindi l'app risponde:</span><span class="sxs-lookup"><span data-stu-id="2ec57-256">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="2ec57-257">Attivare l'app di esempio per eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="2ec57-257">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="2ec57-258">Effettuare una richiesta a `/deletedatabase`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-258">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="2ec57-259">L'app risponde:</span><span class="sxs-lookup"><span data-stu-id="2ec57-259">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="2ec57-260">Effettuare una richiesta all'endpoint `/health`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-260">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="2ec57-261">L'app fornisce una risposta non integra:</span><span class="sxs-lookup"><span data-stu-id="2ec57-261">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="2ec57-262">Separare i probe di idoneità e di attività</span><span class="sxs-lookup"><span data-stu-id="2ec57-262">Separate readiness and liveness probes</span></span>

<span data-ttu-id="2ec57-263">In alcuni scenari di hosting vengono usati due controlli di integrità che distinguono due stati dell'app:</span><span class="sxs-lookup"><span data-stu-id="2ec57-263">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="2ec57-264">L'app è funzionante, ma non ancora pronta a ricevere le richieste.</span><span class="sxs-lookup"><span data-stu-id="2ec57-264">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="2ec57-265">Questo stato è l'*idoneità* dell'app.</span><span class="sxs-lookup"><span data-stu-id="2ec57-265">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="2ec57-266">L'app è funzionante e risponde alle richieste.</span><span class="sxs-lookup"><span data-stu-id="2ec57-266">The app is functioning and responding to requests.</span></span> <span data-ttu-id="2ec57-267">Questo stato è l'*attività* dell'app.</span><span class="sxs-lookup"><span data-stu-id="2ec57-267">This state is the app's *liveness*.</span></span>

<span data-ttu-id="2ec57-268">Il controllo di idoneità in genere esegue un set di controlli più completo e dispendioso in termini di tempo per determinare se tutti i sottosistemi e le risorse dell'app sono disponibili.</span><span class="sxs-lookup"><span data-stu-id="2ec57-268">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="2ec57-269">Un controllo di attività esegue semplicemente un rapido controllo per determinare se l'app è disponibile per elaborare le richieste.</span><span class="sxs-lookup"><span data-stu-id="2ec57-269">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="2ec57-270">Dopo che l'app ha superato il controllo di idoneità, non è necessario caricare ulteriormente l'app con il dispendioso set di controlli di idoneità. Sarà sufficiente controllare solo l'attività.</span><span class="sxs-lookup"><span data-stu-id="2ec57-270">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="2ec57-271">L'app di esempio contiene un controllo di integrità per segnalare il completamento dell'attività di avvio con esecuzione prolungata in un [servizio ospitato](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="2ec57-271">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="2ec57-272">`StartupHostedServiceHealthCheck` espone una proprietà, `StartupTaskCompleted`, che il servizio ospitato può impostare su `true` al termine dell'attività con esecuzione prolungata (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="2ec57-272">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="2ec57-273">L'attività in background con esecuzione prolungata viene avviata da un [servizio ospitato](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="2ec57-273">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="2ec57-274">Al termine dell'attività, `StartupHostedServiceHealthCheck.StartupTaskCompleted` viene impostata su `true`:</span><span class="sxs-lookup"><span data-stu-id="2ec57-274">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="2ec57-275">Il controllo di integrità viene registrato con <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` insieme al servizio ospitato.</span><span class="sxs-lookup"><span data-stu-id="2ec57-275">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="2ec57-276">Poiché il servizio ospitato deve impostare la proprietà sul controllo di integrità, il controllo di integrità viene anche registrato nel contenitore del servizio (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="2ec57-276">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="2ec57-277">Un endpoint di controllo dell'integrità viene creato chiamando `MapHealthChecks` in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2ec57-277">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="2ec57-278">Nell'app di esempio gli endpoint di controllo integrità vengono creati in:</span><span class="sxs-lookup"><span data-stu-id="2ec57-278">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="2ec57-279">`/health/ready`per il controllo della conformità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-279">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="2ec57-280">Il controllo di idoneità filtra i controlli di integrità per il controllo di integrità con il tag `ready`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-280">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="2ec57-281">`/health/live`per il controllo dell'anima.</span><span class="sxs-lookup"><span data-stu-id="2ec57-281">`/health/live` for the liveness check.</span></span> <span data-ttu-id="2ec57-282">Il controllo della durata filtra l'oggetto `StartupHostedServiceHealthCheck` restituendo `false` in [HealthCheckOptions. Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (per altre informazioni, vedere [filtrare i controlli di integrità](#filter-health-checks))</span><span class="sxs-lookup"><span data-stu-id="2ec57-282">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="2ec57-283">Nell'esempio di codice seguente:</span><span class="sxs-lookup"><span data-stu-id="2ec57-283">In the following example code:</span></span>

* <span data-ttu-id="2ec57-284">Il controllo di conformità utilizza tutti i controlli registrati con il tag "Ready".</span><span class="sxs-lookup"><span data-stu-id="2ec57-284">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="2ec57-285">`Predicate`Esclude tutti i controlli e restituisce 200-OK.</span><span class="sxs-lookup"><span data-stu-id="2ec57-285">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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

<span data-ttu-id="2ec57-286">Per eseguire lo scenario di configurazione di idoneità/attività usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="2ec57-286">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="2ec57-287">In a browser visitare più volte `/health/ready` per 15 secondi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-287">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="2ec57-288">Il controllo di integrità segnala *Unhealthy* per i primi 15 secondi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-288">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="2ec57-289">Dopo 15 secondi, l'endpoint segnala *Healthy*, che indica il completamento dell'attività con esecuzione prolungata da parte del servizio ospitato.</span><span class="sxs-lookup"><span data-stu-id="2ec57-289">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="2ec57-290">Questo esempio crea anche un server di pubblicazione dei controlli di integrità (implementazione di <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>) che esegue il primo controllo di conformità con un ritardo di due secondi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-290">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="2ec57-291">Per altre informazioni, vedere la sezione [Server di pubblicazione dei controlli di integrità](#health-check-publisher).</span><span class="sxs-lookup"><span data-stu-id="2ec57-291">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="2ec57-292">Esempio di Kubernetes</span><span class="sxs-lookup"><span data-stu-id="2ec57-292">Kubernetes example</span></span>

<span data-ttu-id="2ec57-293">L'uso di controlli di idoneità e attività separati è utile in un ambiente come [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="2ec57-293">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="2ec57-294">In Kubernetes un'app potrebbe dover eseguire un'operazione di avvio con esecuzione prolungata prima di accettare le richieste, ad esempio un test della disponibilità del database sottostante.</span><span class="sxs-lookup"><span data-stu-id="2ec57-294">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="2ec57-295">L'uso di controlli separati consente all'agente di orchestrazione di distinguere se l'app è funzionante, ma non è ancora pronta o se l'app non è stata avviata.</span><span class="sxs-lookup"><span data-stu-id="2ec57-295">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="2ec57-296">Per altre informazioni sui probe di idoneità e di attività in Kubernetes, vedere [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) (Configurare i probe di attività e di idoneità in Kubernetes) nella documentazione di Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="2ec57-296">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="2ec57-297">L'esempio seguente illustra la configurazione di un probe di idoneità di Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="2ec57-297">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="2ec57-298">Probe basato sulle metriche con un writer di risposta personalizzata</span><span class="sxs-lookup"><span data-stu-id="2ec57-298">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="2ec57-299">L'app di esempio illustra un controllo di integrità della memoria con un writer di risposta personalizzata.</span><span class="sxs-lookup"><span data-stu-id="2ec57-299">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="2ec57-300">`MemoryHealthCheck` segnala uno stato danneggiato se l'app usa più di una determinata soglia di memoria (1 GB nell'app di esempio).</span><span class="sxs-lookup"><span data-stu-id="2ec57-300">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="2ec57-301"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> include informazioni sul Garbage Collector (GC) per l'app (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="2ec57-301">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="2ec57-302">Registrare i servizi dei controlli di integrità con <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-302">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2ec57-303">Invece di abilitare il controllo di integrità passandolo ad <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, `MemoryHealthCheck` viene registrato come servizio.</span><span class="sxs-lookup"><span data-stu-id="2ec57-303">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="2ec57-304">Tutti i servizi registrati da <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> sono disponibili per il middleware e i servizi di controllo dell'integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-304">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="2ec57-305">È consigliabile registrare i servizi di controllo dell'integrità come servizi Singleton.</span><span class="sxs-lookup"><span data-stu-id="2ec57-305">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="2ec57-306">In *CustomWriterStartup.cs* dell'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="2ec57-306">In *CustomWriterStartup.cs* of the sample app:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="2ec57-307">Un endpoint di controllo dell'integrità viene creato chiamando `MapHealthChecks` in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2ec57-307">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="2ec57-308">Un `WriteResponse` delegato viene fornito alla proprietà <Microsoft. AspNetCore. Diagnostics. HealthChecks. HealthCheckOptions. ResponseWriter> per restituire una risposta JSON personalizzata quando viene eseguito il controllo integrità:</span><span class="sxs-lookup"><span data-stu-id="2ec57-308">A `WriteResponse` delegate is provided to the <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="2ec57-309">Il `WriteResponse` delegato formatta `CompositeHealthCheckResult` in un oggetto JSON e restituisce l'output JSON per la risposta del controllo integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-309">The `WriteResponse` delegate formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response.</span></span> <span data-ttu-id="2ec57-310">Per ulteriori informazioni, vedere la sezione [personalizzare l'output](#customize-output) .</span><span class="sxs-lookup"><span data-stu-id="2ec57-310">For more information, see the [Customize output](#customize-output) section.</span></span>

<span data-ttu-id="2ec57-311">Per eseguire lo scenario di probe basato sulle metriche con il writer di risposta personalizzata usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="2ec57-311">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="2ec57-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) include scenari di controllo dell'integrità basati su metriche, tra cui i controlli dell'archiviazione su disco e dell'attività dei valori massimi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="2ec57-313">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) non è gestito né supportato da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2ec57-313">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="2ec57-314">Filtrare in base alla porta</span><span class="sxs-lookup"><span data-stu-id="2ec57-314">Filter by port</span></span>

<span data-ttu-id="2ec57-315">Chiamare `RequireHost` `MapHealthChecks` con un modello di URL che specifica una porta per limitare le richieste di controllo integrità alla porta specificata.</span><span class="sxs-lookup"><span data-stu-id="2ec57-315">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="2ec57-316">Questa chiamata viene in genere usata nell'ambiente di un contenitore per esporre una porta per il monitoraggio dei servizi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-316">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="2ec57-317">L'app di esempio configura la porta usando il [provider di configurazione della variabile di ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2ec57-317">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="2ec57-318">La porta viene impostata nel file *launchSettings.json* e passata al provider di configurazione tramite una variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="2ec57-318">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="2ec57-319">È anche necessario configurare il server per l'ascolto di richieste sulla porta di gestione.</span><span class="sxs-lookup"><span data-stu-id="2ec57-319">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="2ec57-320">Per usare l'app di esempio per illustrare la configurazione della porta di gestione, creare il file *launchSettings.json* in una cartella *Properties*.</span><span class="sxs-lookup"><span data-stu-id="2ec57-320">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="2ec57-321">Il file *Properties/launchSettings. JSON* seguente nell'app di esempio non è incluso nei file di progetto dell'app di esempio e deve essere creato manualmente:</span><span class="sxs-lookup"><span data-stu-id="2ec57-321">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="2ec57-322">Registrare i servizi dei controlli di integrità con <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-322">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2ec57-323">Creare un endpoint di controllo integrità chiamando `MapHealthChecks` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2ec57-323">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="2ec57-324">Nell'app di esempio, una chiamata a `RequireHost` sull'endpoint in `Startup.Configure` specifica la porta di gestione dalla configurazione:</span><span class="sxs-lookup"><span data-stu-id="2ec57-324">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="2ec57-325">Gli endpoint vengono creati nell'app di esempio in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2ec57-325">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="2ec57-326">Nell'esempio di codice seguente:</span><span class="sxs-lookup"><span data-stu-id="2ec57-326">In the following example code:</span></span>

* <span data-ttu-id="2ec57-327">Il controllo di conformità utilizza tutti i controlli registrati con il tag "Ready".</span><span class="sxs-lookup"><span data-stu-id="2ec57-327">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="2ec57-328">`Predicate`Esclude tutti i controlli e restituisce 200-OK.</span><span class="sxs-lookup"><span data-stu-id="2ec57-328">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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
> <span data-ttu-id="2ec57-329">È possibile evitare di creare il file *launchSettings. JSON* nell'app di esempio impostando la porta di gestione in modo esplicito nel codice.</span><span class="sxs-lookup"><span data-stu-id="2ec57-329">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="2ec57-330">In *Program.cs* , in cui <xref:Microsoft.Extensions.Hosting.HostBuilder> viene creato l'oggetto, aggiungere una chiamata a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> e fornire l'endpoint della porta di gestione dell'app.</span><span class="sxs-lookup"><span data-stu-id="2ec57-330">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="2ec57-331">In `Configure` di *ManagementPortStartup.cs*specificare la porta di gestione con `RequireHost` :</span><span class="sxs-lookup"><span data-stu-id="2ec57-331">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="2ec57-332">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2ec57-332">*Program.cs*:</span></span>
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
> <span data-ttu-id="2ec57-333">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2ec57-333">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="2ec57-334">Per eseguire lo scenario di configurazione della porta di gestione usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="2ec57-334">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="2ec57-335">Distribuire una libreria di controllo di integrità</span><span class="sxs-lookup"><span data-stu-id="2ec57-335">Distribute a health check library</span></span>

<span data-ttu-id="2ec57-336">Per distribuire un controllo di integrità come libreria:</span><span class="sxs-lookup"><span data-stu-id="2ec57-336">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="2ec57-337">Scrivere un controllo di integrità che implementa l'interfaccia <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> come classe autonoma.</span><span class="sxs-lookup"><span data-stu-id="2ec57-337">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="2ec57-338">La classe può basarsi su [inserimento delle dipendenze](xref:fundamentals/dependency-injection), attivazione del tipo e [opzioni denominate](xref:fundamentals/configuration/options) per accedere ai dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="2ec57-338">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="2ec57-339">Nella logica dei controlli di integrità di `CheckHealthAsync` :</span><span class="sxs-lookup"><span data-stu-id="2ec57-339">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="2ec57-340">`data1`e `data2` vengono usati nel metodo per eseguire la logica del controllo di integrità del probe.</span><span class="sxs-lookup"><span data-stu-id="2ec57-340">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="2ec57-341">`AccessViolationException`viene gestito.</span><span class="sxs-lookup"><span data-stu-id="2ec57-341">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="2ec57-342">Quando <xref:System.AccessViolationException> si verifica un <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> errore, viene restituito con <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> per consentire agli utenti di configurare lo stato di errore dei controlli di integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-342">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="2ec57-343">Scrivere un metodo di estensione con i parametri che l'app chiama nel metodo `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-343">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="2ec57-344">Nell'esempio seguente si presuma la firma del metodo di controllo dell'integrità seguente:</span><span class="sxs-lookup"><span data-stu-id="2ec57-344">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="2ec57-345">La firma precedente indica che `ExampleHealthCheck` richiede dati aggiuntivi per elaborare la logica del probe di controllo dell'integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-345">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="2ec57-346">I dati vengono forniti al delegato usato per creare l'istanza del controllo di integrità quando il controllo di integrità viene registrato con un metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="2ec57-346">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="2ec57-347">Nell'esempio seguente il chiamante specifica facoltativamente:</span><span class="sxs-lookup"><span data-stu-id="2ec57-347">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="2ec57-348">nome del controllo integrità (`name`).</span><span class="sxs-lookup"><span data-stu-id="2ec57-348">health check name (`name`).</span></span> <span data-ttu-id="2ec57-349">Se `null`, viene usato `example_health_check`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-349">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="2ec57-350">punto dati stringa per il controllo di integrità (`data1`).</span><span class="sxs-lookup"><span data-stu-id="2ec57-350">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="2ec57-351">punto dati Integer per il controllo di integrità (`data2`).</span><span class="sxs-lookup"><span data-stu-id="2ec57-351">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="2ec57-352">Se `null`, viene usato `1`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-352">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="2ec57-353">stato dell'errore (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="2ec57-353">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="2ec57-354">Il valore predefinito è `null`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-354">The default is `null`.</span></span> <span data-ttu-id="2ec57-355">Se `null`, viene segnalato [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) per uno stato di errore.</span><span class="sxs-lookup"><span data-stu-id="2ec57-355">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="2ec57-356">tag (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="2ec57-356">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="2ec57-357">Server di pubblicazione dei controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="2ec57-357">Health Check Publisher</span></span>

<span data-ttu-id="2ec57-358">Quando <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> viene aggiunto al contenitore del servizio, il sistema di controllo dell'integrità esegue periodicamente i controlli di integrità e chiama `PublishAsync` con il risultato.</span><span class="sxs-lookup"><span data-stu-id="2ec57-358">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="2ec57-359">Ciò è utile in uno scenario relativo a un sistema di monitoraggio dell'integrità basato su push, che prevede che ogni processo chiami periodicamente il sistema di monitoraggio per determinare l'integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-359">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="2ec57-360">L'interfaccia <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> ha un solo metodo:</span><span class="sxs-lookup"><span data-stu-id="2ec57-360">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="2ec57-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> consente di impostare:</span><span class="sxs-lookup"><span data-stu-id="2ec57-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="2ec57-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Ritardo iniziale applicato dopo l'avvio dell'app prima dell'esecuzione di <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> istanze.</span><span class="sxs-lookup"><span data-stu-id="2ec57-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="2ec57-363">Il ritardo viene applicato una sola volta all'avvio e non si applica alle iterazioni successive.</span><span class="sxs-lookup"><span data-stu-id="2ec57-363">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="2ec57-364">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-364">The default value is five seconds.</span></span>
* <span data-ttu-id="2ec57-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: Periodo di <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> esecuzione.</span><span class="sxs-lookup"><span data-stu-id="2ec57-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="2ec57-366">Il valore predefinito è 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-366">The default value is 30 seconds.</span></span>
* <span data-ttu-id="2ec57-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: Se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> è `null` (impostazione predefinita), il servizio Publisher verifica integrità esegue tutti i controlli di integrità registrati.</span><span class="sxs-lookup"><span data-stu-id="2ec57-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="2ec57-368">Per eseguire un subset dei controlli di integrità, specificare una funzione che filtra il set di controlli.</span><span class="sxs-lookup"><span data-stu-id="2ec57-368">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="2ec57-369">Il predicato viene valutato per ogni periodo.</span><span class="sxs-lookup"><span data-stu-id="2ec57-369">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="2ec57-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Timeout per l'esecuzione dei controlli di integrità per tutte le <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> istanze.</span><span class="sxs-lookup"><span data-stu-id="2ec57-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="2ec57-371">Usare <xref:System.Threading.Timeout.InfiniteTimeSpan> per l'esecuzione senza un timeout.</span><span class="sxs-lookup"><span data-stu-id="2ec57-371">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="2ec57-372">Il valore predefinito è 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-372">The default value is 30 seconds.</span></span>

<span data-ttu-id="2ec57-373">Nell'app di esempio, `ReadinessPublisher` è un'implementazione di <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="2ec57-373">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="2ec57-374">Lo stato del controllo di integrità viene registrato per ogni controllo a livello di log di:</span><span class="sxs-lookup"><span data-stu-id="2ec57-374">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="2ec57-375">Information ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> ) se lo stato dei controlli di integrità è <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> .</span><span class="sxs-lookup"><span data-stu-id="2ec57-375">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="2ec57-376">Error ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*> ) se lo stato è <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> o <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> .</span><span class="sxs-lookup"><span data-stu-id="2ec57-376">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="2ec57-377">Nell'esempio di `LivenessProbeStartup` dell'app di esempio, il controllo di conformità `StartupHostedService` ha un ritardo di avvio di due secondi e il controllo viene eseguito ogni 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-377">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="2ec57-378">Per attivare l'implementazione <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, l'esempio registra `ReadinessPublisher` come servizio singleton nel contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="2ec57-378">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="2ec57-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) include i server di pubblicazione per diversi sistemi, tra cui [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="2ec57-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="2ec57-380">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) non è gestito né supportato da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2ec57-380">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="2ec57-381">Limitare i controlli integrità con MapWhen</span><span class="sxs-lookup"><span data-stu-id="2ec57-381">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="2ec57-382">Usare <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> per creare un ramo condizionale della pipeline delle richieste per gli endpoint di controllo integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-382">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="2ec57-383">Nell'esempio seguente, `MapWhen` la pipeline della richiesta viene diramata per attivare il middleware dei controlli di integrità se viene ricevuta una richiesta GET per l' `api/HealthCheck` endpoint:</span><span class="sxs-lookup"><span data-stu-id="2ec57-383">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

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

<span data-ttu-id="2ec57-384">Per altre informazioni, vedere <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="2ec57-384">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2ec57-385">ASP.NET Core offre middleware e librerie di controlli di integrità per segnalare l'integrità dei componenti dell'infrastruttura di app.</span><span class="sxs-lookup"><span data-stu-id="2ec57-385">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="2ec57-386">I controlli di integrità vengono esposti da un'app come endpoint HTTP.</span><span class="sxs-lookup"><span data-stu-id="2ec57-386">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="2ec57-387">È possibile configurare endpoint dei controlli di integrità per svariati scenari di monitoraggio in tempo reale:</span><span class="sxs-lookup"><span data-stu-id="2ec57-387">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="2ec57-388">I probe di integrità possono essere usati dagli agenti di orchestrazione e dai servizi di bilanciamento del carico per controllare lo stato di un'app.</span><span class="sxs-lookup"><span data-stu-id="2ec57-388">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="2ec57-389">Ad esempio, un agente di orchestrazione potrebbe rispondere a controllo di integrità non superato arrestando una distribuzione in sequenza o riavviando un contenitore.</span><span class="sxs-lookup"><span data-stu-id="2ec57-389">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="2ec57-390">Un servizio di bilanciamento del carico potrebbe reagire a un'app non integra trasferendo il traffico dall'istanza con errori a un'istanza integra.</span><span class="sxs-lookup"><span data-stu-id="2ec57-390">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="2ec57-391">È possibile monitorare lo stato di integrità di memoria, disco e altre risorse fisiche del server.</span><span class="sxs-lookup"><span data-stu-id="2ec57-391">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="2ec57-392">I controlli di integrità possono testare le dipendenze di un'app, ad esempio i database e gli endpoint di servizio esterni, per verificare la disponibilità e il normale funzionamento.</span><span class="sxs-lookup"><span data-stu-id="2ec57-392">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="2ec57-393">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2ec57-393">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2ec57-394">L'app di esempio include esempi degli scenari descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="2ec57-394">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="2ec57-395">Per eseguire l'app di esempio per un determinato scenario, usare il comando [dotnet run](/dotnet/core/tools/dotnet-run) dalla cartella del progetto in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-395">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="2ec57-396">Per informazioni dettagliate su come usare l'app di esempio, vedere il file *README.md* dell'app di esempio e le descrizioni degli scenari in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="2ec57-396">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2ec57-397">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="2ec57-397">Prerequisites</span></span>

<span data-ttu-id="2ec57-398">I controlli di integrità vengono in genere usati con un servizio di monitoraggio esterno o un agente di orchestrazione per controllare lo stato di un'app.</span><span class="sxs-lookup"><span data-stu-id="2ec57-398">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="2ec57-399">Prima di aggiungere i controlli di integrità a un'app, decidere quale sistema di monitoraggio usare.</span><span class="sxs-lookup"><span data-stu-id="2ec57-399">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="2ec57-400">Il sistema di monitoraggio determina quali tipi di controlli di integrità creare e come configurare i relativi endpoint.</span><span class="sxs-lookup"><span data-stu-id="2ec57-400">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="2ec57-401">Fare riferimento al [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) oppure aggiungere un riferimento al pacchetto [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="2ec57-401">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="2ec57-402">L'app di esempio include il codice di avvio per illustrare i controlli di integrità per diversi scenari.</span><span class="sxs-lookup"><span data-stu-id="2ec57-402">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="2ec57-403">Lo scenario di [probe del database](#database-probe) controlla l'integrità di una connessione di database usando [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="2ec57-403">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="2ec57-404">Lo scenario [Probe DbContext](#entity-framework-core-dbcontext-probe) verifica un database usando un elemento `DbContext` di EF Core.</span><span class="sxs-lookup"><span data-stu-id="2ec57-404">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="2ec57-405">Per esplorare gli scenari relativi ai database, l'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="2ec57-405">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="2ec57-406">Crea un database e specifica la stringa di connessione nel file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2ec57-406">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="2ec57-407">Dispone dei riferimenti al pacchetto seguenti nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="2ec57-407">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="2ec57-408">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="2ec57-408">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="2ec57-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="2ec57-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="2ec57-410">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) non è gestito né supportato da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2ec57-410">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="2ec57-411">Un altro scenario relativo ai controlli di integrità illustra come filtrare i controlli di integrità per una porta di gestione.</span><span class="sxs-lookup"><span data-stu-id="2ec57-411">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="2ec57-412">L'app di esempio richiede di creare un file *Properties/launchSettings.json* che include l'URL di gestione e la porta di gestione.</span><span class="sxs-lookup"><span data-stu-id="2ec57-412">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="2ec57-413">Per altre informazioni, vedere la sezione [Filtrare in base alla porta](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="2ec57-413">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="2ec57-414">Probe di integrità di base</span><span class="sxs-lookup"><span data-stu-id="2ec57-414">Basic health probe</span></span>

<span data-ttu-id="2ec57-415">Per molte app, una configurazione del probe di integrità di base, che segnala la disponibilità dell'app per elaborare le richieste (*attività*), è sufficiente per individuare lo stato dell'app.</span><span class="sxs-lookup"><span data-stu-id="2ec57-415">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="2ec57-416">La configurazione di base registra i servizi di controllo integrità e chiama il middleware dei controlli di integrità per rispondere a un endpoint URL con una risposta di integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-416">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="2ec57-417">Per impostazione predefinita, non vengono registrati controlli di integrità specifici per testare particolari dipendenze o sottosistemi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-417">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="2ec57-418">L'app viene considerata integra se riesce a rispondere all'URL dell'endpoint di integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-418">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="2ec57-419">Il writer di risposta predefinito scrive lo stato (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) come risposta di testo non crittografato per il client, indicando uno stato [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) o [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).</span><span class="sxs-lookup"><span data-stu-id="2ec57-419">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="2ec57-420">Registrare i servizi dei controlli di integrità con <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-420">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2ec57-421">Aggiungere un endpoint per il middleware dei controlli di integrità con <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> nella pipeline di elaborazione della richiesta di `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2ec57-421">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="2ec57-422">Nell'app di esempio l'endpoint di controllo di integrità viene creato in `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="2ec57-422">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="2ec57-423">Per eseguire lo scenario di configurazione di base usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="2ec57-423">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="2ec57-424">Esempio Docker</span><span class="sxs-lookup"><span data-stu-id="2ec57-424">Docker example</span></span>

<span data-ttu-id="2ec57-425">[Docker](xref:host-and-deploy/docker/index) offre una direttiva `HEALTHCHECK` predefinita che può essere usata per controllare lo stato di un'app che usa la configurazione dei controlli di integrità di base:</span><span class="sxs-lookup"><span data-stu-id="2ec57-425">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="2ec57-426">Creare controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="2ec57-426">Create health checks</span></span>

<span data-ttu-id="2ec57-427">I controlli di integrità vengono creati implementando l'interfaccia <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>.</span><span class="sxs-lookup"><span data-stu-id="2ec57-427">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="2ec57-428">Il metodo <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> restituisce <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> che indica l'integrità come `Healthy`, `Degraded` o `Unhealthy`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-428">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="2ec57-429">Il risultato viene scritto come risposta di testo non crittografato con un codice di stato configurabile. La configurazione viene descritta nella sezione [Opzioni dei controlli di integrità](#health-check-options).</span><span class="sxs-lookup"><span data-stu-id="2ec57-429">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="2ec57-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> può anche restituire coppie chiave-valore facoltative.</span><span class="sxs-lookup"><span data-stu-id="2ec57-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="2ec57-431">Controllo di integrità di esempio</span><span class="sxs-lookup"><span data-stu-id="2ec57-431">Example health check</span></span>

<span data-ttu-id="2ec57-432">Nella `ExampleHealthCheck` classe seguente viene illustrato il layout di un controllo di integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-432">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="2ec57-433">La logica dei controlli di integrità viene inserita nel `CheckHealthAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="2ec57-433">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="2ec57-434">Nell'esempio seguente viene impostata una variabile fittizia, `healthCheckResultHealthy` , su `true` .</span><span class="sxs-lookup"><span data-stu-id="2ec57-434">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="2ec57-435">Se il valore di `healthCheckResultHealthy` è impostato su `false` , viene restituito lo stato [HealthCheckResult. unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) .</span><span class="sxs-lookup"><span data-stu-id="2ec57-435">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

### <a name="register-health-check-services"></a><span data-ttu-id="2ec57-436">Registrare i servizi di controllo dell'integrità</span><span class="sxs-lookup"><span data-stu-id="2ec57-436">Register health check services</span></span>

<span data-ttu-id="2ec57-437">Il `ExampleHealthCheck` tipo viene aggiunto ai servizi di controllo integrità in `Startup.ConfigureServices` con <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> :</span><span class="sxs-lookup"><span data-stu-id="2ec57-437">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="2ec57-438">L'overload <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> illustrato nell'esempio seguente imposta lo stato di errore (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) per segnalare quando il controllo di integrità indica un errore.</span><span class="sxs-lookup"><span data-stu-id="2ec57-438">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="2ec57-439">Se lo stato di errore è impostato su `null` (impostazione predefinita), viene segnalato [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).</span><span class="sxs-lookup"><span data-stu-id="2ec57-439">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="2ec57-440">Questo overload è uno scenario utile per gli autori di librerie, in cui lo stato di errore indicato dalla libreria viene applicato dall'app quando si verifica un errore di controllo dell'integrità se l'implementazione del controllo dell'integrità rispetta l'impostazione.</span><span class="sxs-lookup"><span data-stu-id="2ec57-440">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="2ec57-441">Si possono usare *tag* per filtrare i controlli di integrità, come illustrato più avanti nella sezione [Filtrare i controlli di integrità](#filter-health-checks).</span><span class="sxs-lookup"><span data-stu-id="2ec57-441">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="2ec57-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> può anche eseguire una funzione lambda.</span><span class="sxs-lookup"><span data-stu-id="2ec57-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="2ec57-443">Nell'esempio seguente `Startup.ConfigureServices` il nome del controllo integrità viene specificato come `Example` e il controllo restituisce sempre uno stato integro:</span><span class="sxs-lookup"><span data-stu-id="2ec57-443">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="2ec57-444">Usare il middleware per i controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="2ec57-444">Use Health Checks Middleware</span></span>

<span data-ttu-id="2ec57-445">In `Startup.Configure` chiamare <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> nella pipeline di elaborazione con l'URL dell'endpoint o il percorso relativo:</span><span class="sxs-lookup"><span data-stu-id="2ec57-445">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="2ec57-446">Se i controlli di integrità devono essere in ascolto su una porta specifica, usare un overload di <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> per impostare la porta, come illustrato più avanti nella sezione [Filtrare in base alla porta](#filter-by-port):</span><span class="sxs-lookup"><span data-stu-id="2ec57-446">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="2ec57-447">Opzioni dei controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="2ec57-447">Health check options</span></span>

<span data-ttu-id="2ec57-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> consente di personalizzare il comportamento dei controlli di integrità:</span><span class="sxs-lookup"><span data-stu-id="2ec57-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="2ec57-449">Filtrare i controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="2ec57-449">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="2ec57-450">Personalizzare il codice di stato HTTP</span><span class="sxs-lookup"><span data-stu-id="2ec57-450">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="2ec57-451">Eliminare le intestazioni della cache</span><span class="sxs-lookup"><span data-stu-id="2ec57-451">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="2ec57-452">Personalizzare l'output</span><span class="sxs-lookup"><span data-stu-id="2ec57-452">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="2ec57-453">Filtrare i controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="2ec57-453">Filter health checks</span></span>

<span data-ttu-id="2ec57-454">Per impostazione predefinita, il middleware controlli integrità esegue tutti i controlli di integrità registrati.</span><span class="sxs-lookup"><span data-stu-id="2ec57-454">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="2ec57-455">Per eseguire un subset dei controlli di integrità, specificare una funzione che restituisce un valore booleano all'opzione <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate>.</span><span class="sxs-lookup"><span data-stu-id="2ec57-455">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="2ec57-456">Nell'esempio seguente il controllo di integrità `Bar` viene filtrato in base al tag (`bar_tag`) nell'istruzione condizionale della funzione, dove `true` viene restituito solo se la proprietà <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> del controllo di integrità corrisponde a `foo_tag` o a `baz_tag`:</span><span class="sxs-lookup"><span data-stu-id="2ec57-456">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="2ec57-457">Personalizzare il codice di stato HTTP</span><span class="sxs-lookup"><span data-stu-id="2ec57-457">Customize the HTTP status code</span></span>

<span data-ttu-id="2ec57-458">Usare <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> per personalizzare il mapping dello stato di integrità dei codici di stato HTTP.</span><span class="sxs-lookup"><span data-stu-id="2ec57-458">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="2ec57-459">Le assegnazioni <xref:Microsoft.AspNetCore.Http.StatusCodes> seguenti sono i valori predefiniti usati dal middleware.</span><span class="sxs-lookup"><span data-stu-id="2ec57-459">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="2ec57-460">Modificare i valori dei codici di stato in base ai requisiti.</span><span class="sxs-lookup"><span data-stu-id="2ec57-460">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="2ec57-461">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2ec57-461">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="2ec57-462">Eliminare le intestazioni della cache</span><span class="sxs-lookup"><span data-stu-id="2ec57-462">Suppress cache headers</span></span>

<span data-ttu-id="2ec57-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Controlla se il middleware dei controlli di integrità aggiunge intestazioni HTTP a una risposta Probe per evitare la memorizzazione nella cache delle risposte.</span><span class="sxs-lookup"><span data-stu-id="2ec57-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="2ec57-464">Se il valore è `false` (impostazione predefinita), il middleware imposta le intestazioni `Cache-Control`, `Expires` e `Pragma` o ne esegue l'override per impedire la memorizzazione della risposta nella cache.</span><span class="sxs-lookup"><span data-stu-id="2ec57-464">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="2ec57-465">Se il valore è `true`, il middleware non modifica le intestazioni della risposta nella cache.</span><span class="sxs-lookup"><span data-stu-id="2ec57-465">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="2ec57-466">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2ec57-466">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="2ec57-467">Personalizzare l'output</span><span class="sxs-lookup"><span data-stu-id="2ec57-467">Customize output</span></span>

<span data-ttu-id="2ec57-468">L'opzione <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> ottiene o imposta un delegato usato per scrivere la risposta.</span><span class="sxs-lookup"><span data-stu-id="2ec57-468">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="2ec57-469">Il delegato predefinito scrive una risposta in testo non crittografato minima con il valore di stringa [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="2ec57-469">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="2ec57-470">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2ec57-470">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="2ec57-471">Il delegato predefinito scrive una risposta in testo non crittografato minima con il valore di stringa [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="2ec57-471">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="2ec57-472">Il delegato personalizzato seguente, `WriteResponse` , restituisce una risposta JSON personalizzata:</span><span class="sxs-lookup"><span data-stu-id="2ec57-472">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

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

<span data-ttu-id="2ec57-473">Il sistema di controlli di integrità non fornisce il supporto incorporato per i formati restituiti JSON complessi, perché il formato è specifico del sistema di monitoraggio scelto.</span><span class="sxs-lookup"><span data-stu-id="2ec57-473">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="2ec57-474">È possibile personalizzare nell' `JObject` esempio precedente, se necessario, per soddisfare le proprie esigenze.</span><span class="sxs-lookup"><span data-stu-id="2ec57-474">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="2ec57-475">Probe del database</span><span class="sxs-lookup"><span data-stu-id="2ec57-475">Database probe</span></span>

<span data-ttu-id="2ec57-476">Un controllo di integrità può specificare una query di database da eseguire come test booleano per indicare se il database sta rispondendo normalmente.</span><span class="sxs-lookup"><span data-stu-id="2ec57-476">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="2ec57-477">L'app di esempio usa [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), una libreria di controlli di integrità per app ASP.NET Core, per eseguire un controllo di integrità su un database di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="2ec57-477">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="2ec57-478">`AspNetCore.Diagnostics.HealthChecks` esegue una query `SELECT 1` sul database per confermare che la connessione al database sia integra.</span><span class="sxs-lookup"><span data-stu-id="2ec57-478">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="2ec57-479">Quando si controlla una connessione di database con una query, scegliere una query che viene restituita rapidamente.</span><span class="sxs-lookup"><span data-stu-id="2ec57-479">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="2ec57-480">Dall'approccio alla query possono derivare il sovraccarico del database e il degrado delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="2ec57-480">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="2ec57-481">Nella maggior parte dei casi, l'esecuzione di una query di test non è necessaria.</span><span class="sxs-lookup"><span data-stu-id="2ec57-481">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="2ec57-482">È sufficiente stabilire una connessione al database.</span><span class="sxs-lookup"><span data-stu-id="2ec57-482">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="2ec57-483">Se invece è necessario eseguire una query, scegliere una semplice query SELECT, ad esempio `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-483">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="2ec57-484">Includere un riferimento al pacchetto [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="2ec57-484">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="2ec57-485">Fornire una stringa di connessione di database valida nel file *appsettings.json* dell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="2ec57-485">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="2ec57-486">L'app usa una database di SQL Server denominato `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="2ec57-486">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="2ec57-487">Registrare i servizi dei controlli di integrità con <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-487">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2ec57-488">L'app di esempio chiama il metodo `AddSqlServer` di con la stringa di connessione del database (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="2ec57-488">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="2ec57-489">Chiamare il middleware verifica integrità nella pipeline di elaborazione delle app in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="2ec57-489">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="2ec57-490">Per eseguire lo scenario di probe del database di base usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="2ec57-490">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="2ec57-491">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) non è gestito né supportato da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2ec57-491">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="2ec57-492">Probe DbContext di Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="2ec57-492">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="2ec57-493">Il controllo `DbContext` verifica che l'app possa comunicare con il database configurato per un `DbContext` di EF Core.</span><span class="sxs-lookup"><span data-stu-id="2ec57-493">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="2ec57-494">Il controllo `DbContext` è supportato nelle app che:</span><span class="sxs-lookup"><span data-stu-id="2ec57-494">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="2ec57-495">Usano [Entity Framework (EF) Core](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="2ec57-495">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="2ec57-496">Includono un riferimento del pacchetto a [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="2ec57-496">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="2ec57-497">`AddDbContextCheck<TContext>` registra un controllo di integrità per un `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-497">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="2ec57-498">`DbContext` viene reso disponibile come `TContext` al metodo.</span><span class="sxs-lookup"><span data-stu-id="2ec57-498">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="2ec57-499">È disponibile un overload per configurare lo stato di errore, i tag e una query di test personalizzata.</span><span class="sxs-lookup"><span data-stu-id="2ec57-499">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="2ec57-500">Per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="2ec57-500">By default:</span></span>

* <span data-ttu-id="2ec57-501">`DbContextHealthCheck` chiama il metodo `CanConnectAsync` di EF Core.</span><span class="sxs-lookup"><span data-stu-id="2ec57-501">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="2ec57-502">È possibile determinare l'operazione da eseguire quando si controlla l'integrità usando gli overload del metodo `AddDbContextCheck`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-502">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="2ec57-503">Il nome del controllo di integrità è il nome del tipo `TContext`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-503">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="2ec57-504">Nell'app di esempio, `AppDbContext` viene fornito a `AddDbContextCheck` e registrato come servizio in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="2ec57-504">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="2ec57-505">Nell'app di esempio, `UseHealthChecks` aggiunge il middleware dei controlli di integrità in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2ec57-505">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="2ec57-506">Per eseguire lo scenario di probe `DbContext` usando l'app di esempio, verificare che il database specificato dalla stringa di connessione non esista nell'istanza di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="2ec57-506">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="2ec57-507">Se il database esiste, eliminarlo.</span><span class="sxs-lookup"><span data-stu-id="2ec57-507">If the database exists, delete it.</span></span>

<span data-ttu-id="2ec57-508">Eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="2ec57-508">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="2ec57-509">Dopo che l'app è in esecuzione, controllare lo stato di integrità effettuando una richiesta all'endpoint `/health` in un browser.</span><span class="sxs-lookup"><span data-stu-id="2ec57-509">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="2ec57-510">Il database e `AppDbContext` non esistono, quindi l'app fornisce la risposta seguente:</span><span class="sxs-lookup"><span data-stu-id="2ec57-510">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="2ec57-511">Attivare l'app di esempio per creare il database.</span><span class="sxs-lookup"><span data-stu-id="2ec57-511">Trigger the sample app to create the database.</span></span> <span data-ttu-id="2ec57-512">Effettuare una richiesta a `/createdatabase`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-512">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="2ec57-513">L'app risponde:</span><span class="sxs-lookup"><span data-stu-id="2ec57-513">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="2ec57-514">Effettuare una richiesta all'endpoint `/health`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-514">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="2ec57-515">Il database e il contesto esistono, quindi l'app risponde:</span><span class="sxs-lookup"><span data-stu-id="2ec57-515">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="2ec57-516">Attivare l'app di esempio per eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="2ec57-516">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="2ec57-517">Effettuare una richiesta a `/deletedatabase`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-517">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="2ec57-518">L'app risponde:</span><span class="sxs-lookup"><span data-stu-id="2ec57-518">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="2ec57-519">Effettuare una richiesta all'endpoint `/health`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-519">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="2ec57-520">L'app fornisce una risposta non integra:</span><span class="sxs-lookup"><span data-stu-id="2ec57-520">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="2ec57-521">Separare i probe di idoneità e di attività</span><span class="sxs-lookup"><span data-stu-id="2ec57-521">Separate readiness and liveness probes</span></span>

<span data-ttu-id="2ec57-522">In alcuni scenari di hosting vengono usati due controlli di integrità che distinguono due stati dell'app:</span><span class="sxs-lookup"><span data-stu-id="2ec57-522">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="2ec57-523">L'app è funzionante, ma non ancora pronta a ricevere le richieste.</span><span class="sxs-lookup"><span data-stu-id="2ec57-523">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="2ec57-524">Questo stato è l'*idoneità* dell'app.</span><span class="sxs-lookup"><span data-stu-id="2ec57-524">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="2ec57-525">L'app è funzionante e risponde alle richieste.</span><span class="sxs-lookup"><span data-stu-id="2ec57-525">The app is functioning and responding to requests.</span></span> <span data-ttu-id="2ec57-526">Questo stato è l'*attività* dell'app.</span><span class="sxs-lookup"><span data-stu-id="2ec57-526">This state is the app's *liveness*.</span></span>

<span data-ttu-id="2ec57-527">Il controllo di idoneità in genere esegue un set di controlli più completo e dispendioso in termini di tempo per determinare se tutti i sottosistemi e le risorse dell'app sono disponibili.</span><span class="sxs-lookup"><span data-stu-id="2ec57-527">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="2ec57-528">Un controllo di attività esegue semplicemente un rapido controllo per determinare se l'app è disponibile per elaborare le richieste.</span><span class="sxs-lookup"><span data-stu-id="2ec57-528">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="2ec57-529">Dopo che l'app ha superato il controllo di idoneità, non è necessario caricare ulteriormente l'app con il dispendioso set di controlli di idoneità. Sarà sufficiente controllare solo l'attività.</span><span class="sxs-lookup"><span data-stu-id="2ec57-529">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="2ec57-530">L'app di esempio contiene un controllo di integrità per segnalare il completamento dell'attività di avvio con esecuzione prolungata in un [servizio ospitato](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="2ec57-530">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="2ec57-531">`StartupHostedServiceHealthCheck` espone una proprietà, `StartupTaskCompleted`, che il servizio ospitato può impostare su `true` al termine dell'attività con esecuzione prolungata (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="2ec57-531">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="2ec57-532">L'attività in background con esecuzione prolungata viene avviata da un [servizio ospitato](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="2ec57-532">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="2ec57-533">Al termine dell'attività, `StartupHostedServiceHealthCheck.StartupTaskCompleted` viene impostata su `true`:</span><span class="sxs-lookup"><span data-stu-id="2ec57-533">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="2ec57-534">Il controllo di integrità viene registrato con <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` insieme al servizio ospitato.</span><span class="sxs-lookup"><span data-stu-id="2ec57-534">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="2ec57-535">Poiché il servizio ospitato deve impostare la proprietà sul controllo di integrità, il controllo di integrità viene anche registrato nel contenitore del servizio (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="2ec57-535">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="2ec57-536">Chiamare il middleware verifica integrità nella pipeline di elaborazione delle app in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2ec57-536">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="2ec57-537">Nell'app di esempio gli endpoint dei controlli di integrità vengono creati in `/health/ready` per il controllo di idoneità e in `/health/live` per il controllo di attività.</span><span class="sxs-lookup"><span data-stu-id="2ec57-537">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="2ec57-538">Il controllo di idoneità filtra i controlli di integrità per il controllo di integrità con il tag `ready`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-538">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="2ec57-539">Il controllo di attività filtra `StartupHostedServiceHealthCheck` restituendo `false` in [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (per altre informazioni, vedere [Filtrare i controlli di integrità](#filter-health-checks)):</span><span class="sxs-lookup"><span data-stu-id="2ec57-539">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

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

<span data-ttu-id="2ec57-540">Per eseguire lo scenario di configurazione di idoneità/attività usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="2ec57-540">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="2ec57-541">In a browser visitare più volte `/health/ready` per 15 secondi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-541">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="2ec57-542">Il controllo di integrità segnala *Unhealthy* per i primi 15 secondi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-542">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="2ec57-543">Dopo 15 secondi, l'endpoint segnala *Healthy*, che indica il completamento dell'attività con esecuzione prolungata da parte del servizio ospitato.</span><span class="sxs-lookup"><span data-stu-id="2ec57-543">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="2ec57-544">Questo esempio crea anche un server di pubblicazione dei controlli di integrità (implementazione di <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>) che esegue il primo controllo di conformità con un ritardo di due secondi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-544">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="2ec57-545">Per altre informazioni, vedere la sezione [Server di pubblicazione dei controlli di integrità](#health-check-publisher).</span><span class="sxs-lookup"><span data-stu-id="2ec57-545">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="2ec57-546">Esempio di Kubernetes</span><span class="sxs-lookup"><span data-stu-id="2ec57-546">Kubernetes example</span></span>

<span data-ttu-id="2ec57-547">L'uso di controlli di idoneità e attività separati è utile in un ambiente come [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="2ec57-547">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="2ec57-548">In Kubernetes un'app potrebbe dover eseguire un'operazione di avvio con esecuzione prolungata prima di accettare le richieste, ad esempio un test della disponibilità del database sottostante.</span><span class="sxs-lookup"><span data-stu-id="2ec57-548">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="2ec57-549">L'uso di controlli separati consente all'agente di orchestrazione di distinguere se l'app è funzionante, ma non è ancora pronta o se l'app non è stata avviata.</span><span class="sxs-lookup"><span data-stu-id="2ec57-549">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="2ec57-550">Per altre informazioni sui probe di idoneità e di attività in Kubernetes, vedere [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) (Configurare i probe di attività e di idoneità in Kubernetes) nella documentazione di Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="2ec57-550">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="2ec57-551">L'esempio seguente illustra la configurazione di un probe di idoneità di Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="2ec57-551">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="2ec57-552">Probe basato sulle metriche con un writer di risposta personalizzata</span><span class="sxs-lookup"><span data-stu-id="2ec57-552">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="2ec57-553">L'app di esempio illustra un controllo di integrità della memoria con un writer di risposta personalizzata.</span><span class="sxs-lookup"><span data-stu-id="2ec57-553">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="2ec57-554">`MemoryHealthCheck`segnala uno stato di tipo non integro se l'applicazione usa più di una determinata soglia di memoria (1 GB nell'app di esempio).</span><span class="sxs-lookup"><span data-stu-id="2ec57-554">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="2ec57-555"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> include informazioni sul Garbage Collector (GC) per l'app (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="2ec57-555">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="2ec57-556">Registrare i servizi dei controlli di integrità con <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-556">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2ec57-557">Invece di abilitare il controllo di integrità passandolo ad <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, `MemoryHealthCheck` viene registrato come servizio.</span><span class="sxs-lookup"><span data-stu-id="2ec57-557">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="2ec57-558">Tutti i servizi registrati da <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> sono disponibili per il middleware e i servizi di controllo dell'integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-558">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="2ec57-559">È consigliabile registrare i servizi di controllo dell'integrità come servizi Singleton.</span><span class="sxs-lookup"><span data-stu-id="2ec57-559">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="2ec57-560">Nell'app di esempio (*CustomWriterStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="2ec57-560">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="2ec57-561">Chiamare il middleware verifica integrità nella pipeline di elaborazione delle app in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2ec57-561">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="2ec57-562">Viene fornito un delegato `WriteResponse` alla proprietà `ResponseWriter` per visualizzare una risposta JSON personalizzata quando il controllo di integrità viene eseguito:</span><span class="sxs-lookup"><span data-stu-id="2ec57-562">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

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

<span data-ttu-id="2ec57-563">Il metodo `WriteResponse` formatta `CompositeHealthCheckResult` in un oggetto JSON e genera l'output JSON per la risposta del controllo di integrità:</span><span class="sxs-lookup"><span data-stu-id="2ec57-563">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="2ec57-564">Per eseguire lo scenario di probe basato sulle metriche con il writer di risposta personalizzata usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="2ec57-564">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="2ec57-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) include scenari di controllo dell'integrità basati su metriche, tra cui i controlli dell'archiviazione su disco e dell'attività dei valori massimi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="2ec57-566">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) non è gestito né supportato da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2ec57-566">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="2ec57-567">Filtrare in base alla porta</span><span class="sxs-lookup"><span data-stu-id="2ec57-567">Filter by port</span></span>

<span data-ttu-id="2ec57-568">La chiamata a <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> con una porta limita le richieste di controllo dell'integrità alla porta specificata.</span><span class="sxs-lookup"><span data-stu-id="2ec57-568">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="2ec57-569">Questa chiamata viene in genere usata nell'ambiente di un contenitore per esporre una porta per il monitoraggio dei servizi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-569">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="2ec57-570">L'app di esempio configura la porta usando il [provider di configurazione della variabile di ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2ec57-570">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="2ec57-571">La porta viene impostata nel file *launchSettings.json* e passata al provider di configurazione tramite una variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="2ec57-571">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="2ec57-572">È anche necessario configurare il server per l'ascolto di richieste sulla porta di gestione.</span><span class="sxs-lookup"><span data-stu-id="2ec57-572">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="2ec57-573">Per usare l'app di esempio per illustrare la configurazione della porta di gestione, creare il file *launchSettings.json* in una cartella *Properties*.</span><span class="sxs-lookup"><span data-stu-id="2ec57-573">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="2ec57-574">Il file *Properties/launchSettings. JSON* seguente nell'app di esempio non è incluso nei file di progetto dell'app di esempio e deve essere creato manualmente:</span><span class="sxs-lookup"><span data-stu-id="2ec57-574">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="2ec57-575">Registrare i servizi dei controlli di integrità con <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-575">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2ec57-576">La chiamata a <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifica la porta di gestione (*ManagementPortStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="2ec57-576">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="2ec57-577">È possibile evitare di creare il file *launchSettings.json* nell'app di esempio impostando gli URL e la porta di gestione in modo esplicito nel codice.</span><span class="sxs-lookup"><span data-stu-id="2ec57-577">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="2ec57-578">In *Program.cs*, dove viene creato <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>, aggiungere una chiamata a <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> e fornire l'endpoint della porta di gestione e l'endpoint di risposta normale dell'app.</span><span class="sxs-lookup"><span data-stu-id="2ec57-578">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="2ec57-579">In *ManagementPortStartup.cs*, dove viene chiamato <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>, specificare la porta di gestione in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="2ec57-579">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="2ec57-580">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2ec57-580">*Program.cs*:</span></span>
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
> <span data-ttu-id="2ec57-581">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2ec57-581">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="2ec57-582">Per eseguire lo scenario di configurazione della porta di gestione usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="2ec57-582">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="2ec57-583">Distribuire una libreria di controllo di integrità</span><span class="sxs-lookup"><span data-stu-id="2ec57-583">Distribute a health check library</span></span>

<span data-ttu-id="2ec57-584">Per distribuire un controllo di integrità come libreria:</span><span class="sxs-lookup"><span data-stu-id="2ec57-584">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="2ec57-585">Scrivere un controllo di integrità che implementa l'interfaccia <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> come classe autonoma.</span><span class="sxs-lookup"><span data-stu-id="2ec57-585">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="2ec57-586">La classe può basarsi su [inserimento delle dipendenze](xref:fundamentals/dependency-injection), attivazione del tipo e [opzioni denominate](xref:fundamentals/configuration/options) per accedere ai dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="2ec57-586">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="2ec57-587">Nella logica dei controlli di integrità di `CheckHealthAsync` :</span><span class="sxs-lookup"><span data-stu-id="2ec57-587">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="2ec57-588">`data1`e `data2` vengono usati nel metodo per eseguire la logica del controllo di integrità del probe.</span><span class="sxs-lookup"><span data-stu-id="2ec57-588">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="2ec57-589">`AccessViolationException`viene gestito.</span><span class="sxs-lookup"><span data-stu-id="2ec57-589">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="2ec57-590">Quando <xref:System.AccessViolationException> si verifica un <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> errore, viene restituito con <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> per consentire agli utenti di configurare lo stato di errore dei controlli di integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-590">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="2ec57-591">Scrivere un metodo di estensione con i parametri che l'app chiama nel metodo `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-591">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="2ec57-592">Nell'esempio seguente si presuma la firma del metodo di controllo dell'integrità seguente:</span><span class="sxs-lookup"><span data-stu-id="2ec57-592">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="2ec57-593">La firma precedente indica che `ExampleHealthCheck` richiede dati aggiuntivi per elaborare la logica del probe di controllo dell'integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-593">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="2ec57-594">I dati vengono forniti al delegato usato per creare l'istanza del controllo di integrità quando il controllo di integrità viene registrato con un metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="2ec57-594">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="2ec57-595">Nell'esempio seguente il chiamante specifica facoltativamente:</span><span class="sxs-lookup"><span data-stu-id="2ec57-595">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="2ec57-596">nome del controllo integrità (`name`).</span><span class="sxs-lookup"><span data-stu-id="2ec57-596">health check name (`name`).</span></span> <span data-ttu-id="2ec57-597">Se `null`, viene usato `example_health_check`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-597">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="2ec57-598">punto dati stringa per il controllo di integrità (`data1`).</span><span class="sxs-lookup"><span data-stu-id="2ec57-598">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="2ec57-599">punto dati Integer per il controllo di integrità (`data2`).</span><span class="sxs-lookup"><span data-stu-id="2ec57-599">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="2ec57-600">Se `null`, viene usato `1`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-600">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="2ec57-601">stato dell'errore (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="2ec57-601">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="2ec57-602">Il valore predefinito è `null`.</span><span class="sxs-lookup"><span data-stu-id="2ec57-602">The default is `null`.</span></span> <span data-ttu-id="2ec57-603">Se `null`, viene segnalato [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) per uno stato di errore.</span><span class="sxs-lookup"><span data-stu-id="2ec57-603">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="2ec57-604">tag (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="2ec57-604">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="2ec57-605">Server di pubblicazione dei controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="2ec57-605">Health Check Publisher</span></span>

<span data-ttu-id="2ec57-606">Quando <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> viene aggiunto al contenitore del servizio, il sistema di controllo dell'integrità esegue periodicamente i controlli di integrità e chiama `PublishAsync` con il risultato.</span><span class="sxs-lookup"><span data-stu-id="2ec57-606">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="2ec57-607">Ciò è utile in uno scenario relativo a un sistema di monitoraggio dell'integrità basato su push, che prevede che ogni processo chiami periodicamente il sistema di monitoraggio per determinare l'integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-607">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="2ec57-608">L'interfaccia <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> ha un solo metodo:</span><span class="sxs-lookup"><span data-stu-id="2ec57-608">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="2ec57-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> consente di impostare:</span><span class="sxs-lookup"><span data-stu-id="2ec57-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="2ec57-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Ritardo iniziale applicato dopo l'avvio dell'app prima dell'esecuzione di <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> istanze.</span><span class="sxs-lookup"><span data-stu-id="2ec57-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="2ec57-611">Il ritardo viene applicato una sola volta all'avvio e non si applica alle iterazioni successive.</span><span class="sxs-lookup"><span data-stu-id="2ec57-611">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="2ec57-612">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-612">The default value is five seconds.</span></span>
* <span data-ttu-id="2ec57-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: Periodo di <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> esecuzione.</span><span class="sxs-lookup"><span data-stu-id="2ec57-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="2ec57-614">Il valore predefinito è 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-614">The default value is 30 seconds.</span></span>
* <span data-ttu-id="2ec57-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: Se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> è `null` (impostazione predefinita), il servizio Publisher verifica integrità esegue tutti i controlli di integrità registrati.</span><span class="sxs-lookup"><span data-stu-id="2ec57-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="2ec57-616">Per eseguire un subset dei controlli di integrità, specificare una funzione che filtra il set di controlli.</span><span class="sxs-lookup"><span data-stu-id="2ec57-616">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="2ec57-617">Il predicato viene valutato per ogni periodo.</span><span class="sxs-lookup"><span data-stu-id="2ec57-617">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="2ec57-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Timeout per l'esecuzione dei controlli di integrità per tutte le <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> istanze.</span><span class="sxs-lookup"><span data-stu-id="2ec57-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="2ec57-619">Usare <xref:System.Threading.Timeout.InfiniteTimeSpan> per l'esecuzione senza un timeout.</span><span class="sxs-lookup"><span data-stu-id="2ec57-619">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="2ec57-620">Il valore predefinito è 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-620">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="2ec57-621">Nella versione 2.2 di ASP.NET Core, l'impostazione <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> non viene rispettata dall'implementazione <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Viene impostato il valore <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span><span class="sxs-lookup"><span data-stu-id="2ec57-621">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="2ec57-622">Questo problema è stato risolto in ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="2ec57-622">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="2ec57-623">Nell'app di esempio, `ReadinessPublisher` è un'implementazione di <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="2ec57-623">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="2ec57-624">Lo stato di controllo integrità viene registrato per ogni controllo come:</span><span class="sxs-lookup"><span data-stu-id="2ec57-624">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="2ec57-625">Information ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> ) se lo stato dei controlli di integrità è <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> .</span><span class="sxs-lookup"><span data-stu-id="2ec57-625">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="2ec57-626">Error ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*> ) se lo stato è <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> o <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> .</span><span class="sxs-lookup"><span data-stu-id="2ec57-626">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="2ec57-627">Nell'esempio di `LivenessProbeStartup` dell'app di esempio, il controllo di conformità `StartupHostedService` ha un ritardo di avvio di due secondi e il controllo viene eseguito ogni 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="2ec57-627">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="2ec57-628">Per attivare l'implementazione <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, l'esempio registra `ReadinessPublisher` come servizio singleton nel contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="2ec57-628">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="2ec57-629">La soluzione alternativa seguente consente di aggiungere un'istanza <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> al contenitore del servizio quando uno o più servizi ospitati sono già stati aggiunti all'app.</span><span class="sxs-lookup"><span data-stu-id="2ec57-629">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="2ec57-630">Questa soluzione alternativa non è necessaria in ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="2ec57-630">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
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
> <span data-ttu-id="2ec57-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) include i server di pubblicazione per diversi sistemi, tra cui [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="2ec57-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="2ec57-632">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) non è gestito né supportato da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2ec57-632">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="2ec57-633">Limitare i controlli integrità con MapWhen</span><span class="sxs-lookup"><span data-stu-id="2ec57-633">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="2ec57-634">Usare <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> per creare un ramo condizionale della pipeline delle richieste per gli endpoint di controllo integrità.</span><span class="sxs-lookup"><span data-stu-id="2ec57-634">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="2ec57-635">Nell'esempio seguente, `MapWhen` la pipeline della richiesta viene diramata per attivare il middleware dei controlli di integrità se viene ricevuta una richiesta GET per l' `api/HealthCheck` endpoint:</span><span class="sxs-lookup"><span data-stu-id="2ec57-635">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="2ec57-636">Per altre informazioni, vedere <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="2ec57-636">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
