---
title: Creazione e Reindirizzamento dei log con il modulo ASP.NET Core
author: rick-anderson
description: Configurare IIS e il modulo ASP.NET Core per acquisire i log e le informazioni di diagnostica.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: host-and-deploy/iis/logging-and-diagnostics
ms.openlocfilehash: b866be130a93491bce7c5c7e08045de961ff91b2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057440"
---
# <a name="log-creation-and-redirection"></a><span data-ttu-id="f4633-103">Creazione e reindirizzamento dei log</span><span class="sxs-lookup"><span data-stu-id="f4633-103">Log creation and redirection</span></span>

<span data-ttu-id="f4633-104">Il modulo ASP.NET Core reindirizza su disco l'output della console stdout e stderr se sono impostati gli attributi `stdoutLogEnabled` e `stdoutLogFile` dell'elemento `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="f4633-104">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="f4633-105">Le eventuali le cartelle nel percorso `stdoutLogFile` vengono create dal modulo quando viene creato il file di log.</span><span class="sxs-lookup"><span data-stu-id="f4633-105">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="f4633-106">Il pool di applicazioni deve avere accesso in scrittura al percorso in cui vengono scritti i log (usare `IIS AppPool\{APP POOL NAME}` per fornire l'autorizzazione di scrittura, dove il segnaposto `{APP POOL NAME}` è il nome del pool di applicazioni).</span><span class="sxs-lookup"><span data-stu-id="f4633-106">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="f4633-107">I log non vengono ruotati, a meno che non si verifichi il riciclo/riavvio del processo.</span><span class="sxs-lookup"><span data-stu-id="f4633-107">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="f4633-108">È responsabilità del provider di servizi di hosting limitare lo spazio su disco usato dai log.</span><span class="sxs-lookup"><span data-stu-id="f4633-108">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="f4633-109">L'uso del log stdout è consigliato solo per la risoluzione dei problemi di avvio dell'app durante l'hosting in IIS o quando si usa il [supporto in fase di sviluppo per IIS con Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), non durante il debug in locale e l'esecuzione dell'app con IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f4633-109">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="f4633-110">Non usare il log stdout per scopi di registrazione generale delle app.</span><span class="sxs-lookup"><span data-stu-id="f4633-110">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="f4633-111">Per la registrazione di routine in un'app ASP.NET Core, usare una libreria di registrazione che limita le dimensioni dei file di log e ne esegue la rotazione.</span><span class="sxs-lookup"><span data-stu-id="f4633-111">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f4633-112">Per altre informazioni, vedere [Provider di registrazione di terze parti](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="f4633-112">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="f4633-113">Un timestamp e l'estensione del file vengono aggiunti automaticamente al momento della creazione del file di log.</span><span class="sxs-lookup"><span data-stu-id="f4633-113">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="f4633-114">Il nome del file di log è costituito dall'aggiunta del timestamp, dell'ID del processo e dell'estensione di file ( `.log` ) all'ultimo segmento del `stdoutLogFile` percorso, in genere `stdout` delimitato da caratteri di sottolineatura.</span><span class="sxs-lookup"><span data-stu-id="f4633-114">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="f4633-115">Se il `stdoutLogFile` percorso termina con `stdout` , il nome file di un log per un'app con PID 1934 creato in 2/5/2018 a 19:42:32 è `stdout_20180205194132_1934.log` .</span><span class="sxs-lookup"><span data-stu-id="f4633-115">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="f4633-116">Se `stdoutLogEnabled` è false, gli errori che si verificano all'avvio dell'app vengono acquisiti ed emessi nel log eventi fino a 30 KB.</span><span class="sxs-lookup"><span data-stu-id="f4633-116">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="f4633-117">Dopo l'avvio, tutti i log aggiuntivi vengono rimossi.</span><span class="sxs-lookup"><span data-stu-id="f4633-117">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="f4633-118">Nell'elemento di esempio seguente viene `aspNetCore` configurata la registrazione di stdout nel percorso relativo `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="f4633-118">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="f4633-119">Verificare che l'identità dell'utente AppPool disponga dell'autorizzazione di scrittura per il percorso specificato.</span><span class="sxs-lookup"><span data-stu-id="f4633-119">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="f4633-120">Quando si pubblica un'app per la distribuzione di app Azure Service, il valore viene impostato da Web SDK `stdoutLogFile` `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="f4633-120">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f4633-121">La `%home` variabile di ambiente è predefinita per le app ospitate dal servizio app Azure.</span><span class="sxs-lookup"><span data-stu-id="f4633-121">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="f4633-122">Per creare regole di filtro di registrazione, vedere le sezioni [configurazione](xref:fundamentals/logging/index#log-filtering) e [filtro dei log](xref:fundamentals/logging/index#log-filtering) della documentazione relativa alla registrazione del ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f4633-122">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="f4633-123">Per ulteriori informazioni sui formati di percorso, vedere [formati di percorso dei file nei sistemi Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="f4633-123">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="f4633-124">Log di diagnostica avanzati</span><span class="sxs-lookup"><span data-stu-id="f4633-124">Enhanced diagnostic logs</span></span>

<span data-ttu-id="f4633-125">Il modulo ASP.NET Core può essere configurato per restituire log di diagnostica avanzata.</span><span class="sxs-lookup"><span data-stu-id="f4633-125">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="f4633-126">Aggiungere l' `<handlerSettings>` elemento all' `<aspNetCore>` elemento in `web.config` .</span><span class="sxs-lookup"><span data-stu-id="f4633-126">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="f4633-127">L'impostazione di `debugLevel` su `TRACE` restituisce una maggior fedeltà dei dati diagnostici:</span><span class="sxs-lookup"><span data-stu-id="f4633-127">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="f4633-128">Tutte le cartelle nel percorso ( `logs` nell'esempio precedente) vengono create dal modulo quando viene creato il file di log.</span><span class="sxs-lookup"><span data-stu-id="f4633-128">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="f4633-129">Il pool di applicazioni deve avere accesso in scrittura al percorso in cui vengono scritti i log (usare `IIS AppPool\{APP POOL NAME}` per fornire l'autorizzazione di scrittura, dove il segnaposto `{APP POOL NAME}` è il nome del pool di applicazioni).</span><span class="sxs-lookup"><span data-stu-id="f4633-129">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="f4633-130">I valori di livello debug (`debugLevel`) possono includere sia il livello che il percorso.</span><span class="sxs-lookup"><span data-stu-id="f4633-130">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="f4633-131">Livelli (in ordine dal meno dettagliato al più dettagliato):</span><span class="sxs-lookup"><span data-stu-id="f4633-131">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="f4633-132">ERRORE</span><span class="sxs-lookup"><span data-stu-id="f4633-132">ERROR</span></span>
* <span data-ttu-id="f4633-133">WARNING</span><span class="sxs-lookup"><span data-stu-id="f4633-133">WARNING</span></span>
* <span data-ttu-id="f4633-134">INFO</span><span class="sxs-lookup"><span data-stu-id="f4633-134">INFO</span></span>
* <span data-ttu-id="f4633-135">TRACE</span><span class="sxs-lookup"><span data-stu-id="f4633-135">TRACE</span></span>

<span data-ttu-id="f4633-136">Posizioni (sono consentite più posizioni):</span><span class="sxs-lookup"><span data-stu-id="f4633-136">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="f4633-137">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="f4633-137">CONSOLE</span></span>
* <span data-ttu-id="f4633-138">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="f4633-138">EVENTLOG</span></span>
* <span data-ttu-id="f4633-139">FILE</span><span class="sxs-lookup"><span data-stu-id="f4633-139">FILE</span></span>

<span data-ttu-id="f4633-140">Le impostazioni del gestore possono essere specificate anche tramite le variabili di ambiente:</span><span class="sxs-lookup"><span data-stu-id="f4633-140">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="f4633-141">`ASPNETCORE_MODULE_DEBUG_FILE`: Percorso del file di log di debug.</span><span class="sxs-lookup"><span data-stu-id="f4633-141">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="f4633-142">(Impostazione predefinita: `aspnetcore-debug.log` )</span><span class="sxs-lookup"><span data-stu-id="f4633-142">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="f4633-143">`ASPNETCORE_MODULE_DEBUG`: Impostazione del livello di debug.</span><span class="sxs-lookup"><span data-stu-id="f4633-143">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="f4633-144">**Non** lasciare la registrazione del debug abilitata nella distribuzione per un tempo superiore a quello necessario alla risoluzione di problema.</span><span class="sxs-lookup"><span data-stu-id="f4633-144">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="f4633-145">Le dimensioni del log non sono limitate.</span><span class="sxs-lookup"><span data-stu-id="f4633-145">The size of the log isn't limited.</span></span> <span data-ttu-id="f4633-146">Se si lascia abilitato il log di debug, lo spazio disponibile su disco può esaurirsi e il server o il servizio app può registrare un arresto anomalo.</span><span class="sxs-lookup"><span data-stu-id="f4633-146">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="f4633-147">Per un esempio dell'elemento nel file, vedere [configurazione del modulo ASP.NET Core `web.config` con](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) `aspNetCore` `web.config` .</span><span class="sxs-lookup"><span data-stu-id="f4633-147">See [Configuration of ASP.NET Core Module with `web.config`](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>
