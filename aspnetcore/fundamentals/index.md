---
title: Nozioni fondamentali su ASP.NET Core
author: rick-anderson
description: Informazioni sui concetti fondamentali per la compilazione di app ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/index
ms.openlocfilehash: c797ce8bcb22aec2b56df2f3b108da4cbfde263d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403300"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="7a09b-103">Nozioni fondamentali su ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a09b-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a09b-104">Questo articolo fornisce una panoramica degli argomenti chiave per comprendere come sviluppare app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7a09b-104">This article provides an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="7a09b-105">Classe Startup</span><span class="sxs-lookup"><span data-stu-id="7a09b-105">The Startup class</span></span>

<span data-ttu-id="7a09b-106">All'interno della classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="7a09b-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="7a09b-107">Vengono configurati i servizi necessari per l'app.</span><span class="sxs-lookup"><span data-stu-id="7a09b-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="7a09b-108">La pipeline di gestione delle richieste dell'app è definita come una serie di componenti middleware.</span><span class="sxs-lookup"><span data-stu-id="7a09b-108">The app's request handling pipeline is defined, as a series of middleware components.</span></span>

<span data-ttu-id="7a09b-109">Ecco un esempio di classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="7a09b-109">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="7a09b-110">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-110">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="7a09b-111">Iniezione di dipendenze (servizi)</span><span class="sxs-lookup"><span data-stu-id="7a09b-111">Dependency injection (services)</span></span>

<span data-ttu-id="7a09b-112">ASP.NET Core include un framework DI inserimento delle dipendenze incorporato che rende disponibili i servizi configurati in un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="7a09b-112">ASP.NET Core includes a built-in dependency injection (DI) framework that makes configured services available throughout an app.</span></span> <span data-ttu-id="7a09b-113">Ad esempio, un componente di registrazione è un servizio.</span><span class="sxs-lookup"><span data-stu-id="7a09b-113">For example, a logging component is a service.</span></span>

<span data-ttu-id="7a09b-114">Il codice per configurare o *registrare* i servizi viene aggiunto al metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7a09b-114">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7a09b-115">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a09b-115">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

<span data-ttu-id="7a09b-116">I servizi vengono in genere risolti da DI usando l'inserimento del costruttore.</span><span class="sxs-lookup"><span data-stu-id="7a09b-116">Services are typically resolved from DI using constructor injection.</span></span> <span data-ttu-id="7a09b-117">Con l'inserimento del costruttore, una classe dichiara un parametro del costruttore del tipo richiesto o di un'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="7a09b-117">With constructor injection, a class declares a constructor parameter of either the required type or an interface.</span></span> <span data-ttu-id="7a09b-118">Il framework DI fornisce un'istanza di questo servizio in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="7a09b-118">The DI framework provides an instance of this service at runtime.</span></span>

<span data-ttu-id="7a09b-119">Nell'esempio seguente viene usato il costruttore Injection per risolvere un `RazorPagesMovieContext` da di:</span><span class="sxs-lookup"><span data-stu-id="7a09b-119">The following example uses constructor injection to resolve a `RazorPagesMovieContext` from DI:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="7a09b-120">Se il contenitore Inversion of Control (IoC) incorporato non soddisfa tutte le esigenze di un'app, è possibile usare un contenitore IoC di terze parti.</span><span class="sxs-lookup"><span data-stu-id="7a09b-120">If the built-in Inversion of Control (IoC) container doesn't meet all of an app's needs, a third-party IoC container can be used instead.</span></span>

<span data-ttu-id="7a09b-121">Per altre informazioni, vedere <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-121">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="7a09b-122">Middleware</span><span class="sxs-lookup"><span data-stu-id="7a09b-122">Middleware</span></span>

<span data-ttu-id="7a09b-123">La pipeline di gestione delle richieste è strutturata come una serie di componenti middleware.</span><span class="sxs-lookup"><span data-stu-id="7a09b-123">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="7a09b-124">Ogni componente esegue operazioni su un oggetto `HttpContext` e richiama il middleware successivo nella pipeline o termina la richiesta.</span><span class="sxs-lookup"><span data-stu-id="7a09b-124">Each component performs operations on an `HttpContext` and either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="7a09b-125">Per convenzione, un componente middleware viene aggiunto alla pipeline richiamando un `Use...` metodo di estensione nel `Startup.Configure` metodo.</span><span class="sxs-lookup"><span data-stu-id="7a09b-125">By convention, a middleware component is added to the pipeline by invoking a `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="7a09b-126">Per abilitare il rendering dei file statici, ad esempio, chiamare `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="7a09b-126">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="7a09b-127">Nell'esempio seguente viene configurata una pipeline di gestione delle richieste:</span><span class="sxs-lookup"><span data-stu-id="7a09b-127">The following example configures a request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

<span data-ttu-id="7a09b-128">ASP.NET Core include un set completo di middleware incorporato.</span><span class="sxs-lookup"><span data-stu-id="7a09b-128">ASP.NET Core includes a rich set of built-in middleware.</span></span> <span data-ttu-id="7a09b-129">È anche possibile scrivere componenti middleware personalizzati.</span><span class="sxs-lookup"><span data-stu-id="7a09b-129">Custom middleware components can also be written.</span></span>

<span data-ttu-id="7a09b-130">Per altre informazioni, vedere <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-130">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="7a09b-131">Host</span><span class="sxs-lookup"><span data-stu-id="7a09b-131">Host</span></span>

<span data-ttu-id="7a09b-132">All'avvio, un'app ASP.NET Core compila un *host*.</span><span class="sxs-lookup"><span data-stu-id="7a09b-132">On startup, an ASP.NET Core app builds a *host*.</span></span> <span data-ttu-id="7a09b-133">L'host incapsula tutte le risorse dell'app, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a09b-133">The host encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="7a09b-134">Un'implementazione del server HTTP</span><span class="sxs-lookup"><span data-stu-id="7a09b-134">An HTTP server implementation</span></span>
* <span data-ttu-id="7a09b-135">I componenti middleware</span><span class="sxs-lookup"><span data-stu-id="7a09b-135">Middleware components</span></span>
* <span data-ttu-id="7a09b-136">Registrazione</span><span class="sxs-lookup"><span data-stu-id="7a09b-136">Logging</span></span>
* <span data-ttu-id="7a09b-137">Servizi DI INSERIMENTO DI dipendenze</span><span class="sxs-lookup"><span data-stu-id="7a09b-137">Dependency injection (DI) services</span></span>
* <span data-ttu-id="7a09b-138">Configurazione</span><span class="sxs-lookup"><span data-stu-id="7a09b-138">Configuration</span></span>

<span data-ttu-id="7a09b-139">Sono presenti due host diversi:</span><span class="sxs-lookup"><span data-stu-id="7a09b-139">There are two different hosts:</span></span> 

* <span data-ttu-id="7a09b-140">Host generico .NET</span><span class="sxs-lookup"><span data-stu-id="7a09b-140">.NET Generic Host</span></span>
* <span data-ttu-id="7a09b-141">Host Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a09b-141">ASP.NET Core Web Host</span></span>

<span data-ttu-id="7a09b-142">L'host generico .NET è consigliato.</span><span class="sxs-lookup"><span data-stu-id="7a09b-142">The .NET Generic Host is recommended.</span></span> <span data-ttu-id="7a09b-143">Il ASP.NET Core host Web è disponibile solo per la compatibilità con le versioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="7a09b-143">The ASP.NET Core Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="7a09b-144">Nell'esempio seguente viene creato un host generico .NET:</span><span class="sxs-lookup"><span data-stu-id="7a09b-144">The following example creates a .NET Generic Host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

<span data-ttu-id="7a09b-145">I `CreateDefaultBuilder` `ConfigureWebHostDefaults` metodi e configurano un host con un set di opzioni predefinite, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a09b-145">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with a set of default options, such as:</span></span>

* <span data-ttu-id="7a09b-146">Usare [Kestrel](#servers) come server Web e abilitare l'integrazione IIS.</span><span class="sxs-lookup"><span data-stu-id="7a09b-146">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="7a09b-147">Caricare la configurazione da *appsettings.json*, *appsettings.[EnvironmentName].json*, variabili di ambiente, argomenti della riga di comando e altri origini di configurazione.</span><span class="sxs-lookup"><span data-stu-id="7a09b-147">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="7a09b-148">Inviare l'output di registrazione alla console e ai provider di debug.</span><span class="sxs-lookup"><span data-stu-id="7a09b-148">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="7a09b-149">Per altre informazioni, vedere <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-149">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="7a09b-150">Scenari non Web</span><span class="sxs-lookup"><span data-stu-id="7a09b-150">Non-web scenarios</span></span>

<span data-ttu-id="7a09b-151">L'host generico consente ad altri tipi di app di usare estensioni del framework trasversali quali la registrazione, l'inserimento delle dipendenze, la configurazione e la gestione del ciclo di vita delle app.</span><span class="sxs-lookup"><span data-stu-id="7a09b-151">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="7a09b-152">Per altre informazioni, vedere <xref:fundamentals/host/generic-host> e <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-152">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="7a09b-153">Server</span><span class="sxs-lookup"><span data-stu-id="7a09b-153">Servers</span></span>

<span data-ttu-id="7a09b-154">Un'app ASP.NET Core usa un'implementazione del server HTTP per l'ascolto delle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="7a09b-154">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="7a09b-155">Il server espone le richieste all'app sotto forma di insieme di [funzionalità di richiesta](xref:fundamentals/request-features) strutturate in un `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="7a09b-155">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="7a09b-156">Windows</span><span class="sxs-lookup"><span data-stu-id="7a09b-156">Windows</span></span>](#tab/windows)

<span data-ttu-id="7a09b-157">ASP.NET Core include le implementazioni server seguenti:</span><span class="sxs-lookup"><span data-stu-id="7a09b-157">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="7a09b-158">*Kestrel* è un server Web multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="7a09b-158">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="7a09b-159">Kestrel viene spesso eseguito in una configurazione con proxy inverso tramite [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="7a09b-159">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="7a09b-160">In ASP.NET Core 2.0 o versione successiva Kestrel può essere eseguito come server perimetrale pubblico esposto direttamente a Internet.</span><span class="sxs-lookup"><span data-stu-id="7a09b-160">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="7a09b-161">Il *Server http IIS* è un server per Windows che usa IIS.</span><span class="sxs-lookup"><span data-stu-id="7a09b-161">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="7a09b-162">Con questo server l'app ASP.NET Core e IIS sono eseguiti nello stesso processo.</span><span class="sxs-lookup"><span data-stu-id="7a09b-162">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="7a09b-163">*HTTP.sys* è un server per Windows che non viene usato con IIS.</span><span class="sxs-lookup"><span data-stu-id="7a09b-163">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="7a09b-164">macOS</span><span class="sxs-lookup"><span data-stu-id="7a09b-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="7a09b-165">ASP.NET Core fornisce l'implementazione del server multipiattaforma *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="7a09b-165">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="7a09b-166">In ASP.NET Core 2,0 o versioni successive, il gheppio può essere eseguito come server perimetrale pubblico esposto direttamente a Internet.</span><span class="sxs-lookup"><span data-stu-id="7a09b-166">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="7a09b-167">Kestrel viene spesso eseguito in una configurazione con proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="7a09b-167">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="7a09b-168">Linux</span><span class="sxs-lookup"><span data-stu-id="7a09b-168">Linux</span></span>](#tab/linux)

<span data-ttu-id="7a09b-169">ASP.NET Core fornisce l'implementazione del server multipiattaforma *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="7a09b-169">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="7a09b-170">In ASP.NET Core 2,0 o versioni successive, il gheppio può essere eseguito come server perimetrale pubblico esposto direttamente a Internet.</span><span class="sxs-lookup"><span data-stu-id="7a09b-170">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="7a09b-171">Kestrel viene spesso eseguito in una configurazione con proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="7a09b-171">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="7a09b-172">Per altre informazioni, vedere <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-172">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="7a09b-173">Configurazione</span><span class="sxs-lookup"><span data-stu-id="7a09b-173">Configuration</span></span>

<span data-ttu-id="7a09b-174">ASP.NET Core offre un framework di configurazione che ottiene le impostazioni, ad esempio coppie nome-valore, da un set ordinato di provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="7a09b-174">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="7a09b-175">I provider di configurazione incorporati sono disponibili per un'ampia gamma di origini, ad esempio file *JSON* , file *XML* , variabili di ambiente e argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="7a09b-175">Built-in configuration providers are available for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="7a09b-176">Scrivere provider di configurazione personalizzati per supportare altre origini.</span><span class="sxs-lookup"><span data-stu-id="7a09b-176">Write custom configuration providers to support other sources.</span></span>

<span data-ttu-id="7a09b-177">Per [impostazione predefinita](xref:fundamentals/configuration/index#default), le app ASP.NET Core sono configurate per la lettura da *appsettings.js*, le variabili di ambiente, la riga di comando e altro ancora.</span><span class="sxs-lookup"><span data-stu-id="7a09b-177">By [default](xref:fundamentals/configuration/index#default), ASP.NET Core apps are configured to read from *appsettings.json*, environment variables, the command line, and more.</span></span> <span data-ttu-id="7a09b-178">Quando viene caricata la configurazione dell'app, i valori delle variabili di ambiente eseguono l'override dei valori di *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="7a09b-178">When the app's configuration is loaded, values from environment variables override values from *appsettings.json*.</span></span>

<span data-ttu-id="7a09b-179">Il modo migliore per leggere i valori di configurazione correlati consiste nell'usare il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="7a09b-179">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="7a09b-180">Per altre informazioni, vedere [associare dati di configurazione gerarchici usando il modello di opzioni](xref:fundamentals/configuration/index#optpat).</span><span class="sxs-lookup"><span data-stu-id="7a09b-180">For more information, see [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat).</span></span>

<span data-ttu-id="7a09b-181">Per la gestione dei dati di configurazione riservati, ad esempio le password, ASP.NET Core fornisce la [gestione dei segreti](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="7a09b-181">For managing confidential configuration data such as passwords, ASP.NET Core provides the [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="7a09b-182">Per i segreti di produzione, si consiglia di usare [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="7a09b-182">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="7a09b-183">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-183">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="environments"></a><span data-ttu-id="7a09b-184">Ambienti</span><span class="sxs-lookup"><span data-stu-id="7a09b-184">Environments</span></span>

<span data-ttu-id="7a09b-185">Gli ambienti di esecuzione, ad esempio `Development` , `Staging` e `Production` , sono una nozione di primo livello in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7a09b-185">Execution environments, such as `Development`, `Staging`, and `Production`, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="7a09b-186">Specificare l'ambiente in cui è in esecuzione un'app impostando la `ASPNETCORE_ENVIRONMENT` variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="7a09b-186">Specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="7a09b-187">ASP.NET Core legge tale variabile di ambiente all'avvio dell'app e ne archivia il valore in un'implementazione `IWebHostEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="7a09b-187">ASP.NET Core reads that environment variable at app startup and stores the value in an `IWebHostEnvironment` implementation.</span></span> <span data-ttu-id="7a09b-188">Questa implementazione è disponibile ovunque in un'app tramite l'inserimento DI dipendenze (DI).</span><span class="sxs-lookup"><span data-stu-id="7a09b-188">This implementation is available anywhere in an app via dependency injection (DI).</span></span>

<span data-ttu-id="7a09b-189">Nell'esempio seguente l'app viene configurata in modo da fornire informazioni dettagliate sull'errore durante l'esecuzione nell' `Development` ambiente:</span><span class="sxs-lookup"><span data-stu-id="7a09b-189">The following example configures the app to provide detailed error information when running in the `Development` environment:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="7a09b-190">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-190">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="7a09b-191">Registrazione</span><span class="sxs-lookup"><span data-stu-id="7a09b-191">Logging</span></span>

<span data-ttu-id="7a09b-192">ASP.NET Core supporta un'API di registrazione che funziona con un'ampia gamma di provider di registrazione predefiniti e di terze parti.</span><span class="sxs-lookup"><span data-stu-id="7a09b-192">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="7a09b-193">I provider disponibili includono:</span><span class="sxs-lookup"><span data-stu-id="7a09b-193">Available providers include:</span></span>

* <span data-ttu-id="7a09b-194">Console</span><span class="sxs-lookup"><span data-stu-id="7a09b-194">Console</span></span>
* <span data-ttu-id="7a09b-195">Debug</span><span class="sxs-lookup"><span data-stu-id="7a09b-195">Debug</span></span>
* <span data-ttu-id="7a09b-196">Event Tracing for Windows</span><span class="sxs-lookup"><span data-stu-id="7a09b-196">Event Tracing on Windows</span></span>
* <span data-ttu-id="7a09b-197">Registro eventi di Windows</span><span class="sxs-lookup"><span data-stu-id="7a09b-197">Windows Event Log</span></span>
* <span data-ttu-id="7a09b-198">TraceSource</span><span class="sxs-lookup"><span data-stu-id="7a09b-198">TraceSource</span></span>
* <span data-ttu-id="7a09b-199">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="7a09b-199">Azure App Service</span></span>
* <span data-ttu-id="7a09b-200">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="7a09b-200">Azure Application Insights</span></span>

<span data-ttu-id="7a09b-201">Per creare i log, risolvere un <xref:Microsoft.Extensions.Logging.ILogger%601> servizio da inserimento delle dipendenze (di) e metodi di registrazione delle chiamate, ad esempio <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> .</span><span class="sxs-lookup"><span data-stu-id="7a09b-201">To create logs, resolve an <xref:Microsoft.Extensions.Logging.ILogger%601> service from dependency injection (DI) and call logging methods such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span></span> <span data-ttu-id="7a09b-202">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a09b-202">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

<span data-ttu-id="7a09b-203">I metodi di registrazione, ad esempio, `LogInformation` supportano un numero qualsiasi di campi.</span><span class="sxs-lookup"><span data-stu-id="7a09b-203">Logging methods such as `LogInformation` support any number of fields.</span></span> <span data-ttu-id="7a09b-204">Questi campi vengono in genere usati per creare un messaggio `string` , ma alcuni provider di registrazione li inviano a un archivio dati come campi separati.</span><span class="sxs-lookup"><span data-stu-id="7a09b-204">These fields are commonly used to construct a message `string`, but some logging providers send these to a data store as separate fields.</span></span> <span data-ttu-id="7a09b-205">Questa funzionalità consente ai provider di registrazione di implementare la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="7a09b-205">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="7a09b-206">Per altre informazioni, vedere <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-206">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="7a09b-207">Routing.</span><span class="sxs-lookup"><span data-stu-id="7a09b-207">Routing</span></span>

<span data-ttu-id="7a09b-208">Una *route* è un modello URL di cui è stato eseguito il mapping su un gestore.</span><span class="sxs-lookup"><span data-stu-id="7a09b-208">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="7a09b-209">Il gestore è in genere una Razor pagina, un metodo di azione in un controller MVC o un middleware.</span><span class="sxs-lookup"><span data-stu-id="7a09b-209">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="7a09b-210">Il routing di ASP.NET Core consente di controllare gli URL usati dall'app.</span><span class="sxs-lookup"><span data-stu-id="7a09b-210">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="7a09b-211">Per altre informazioni, vedere <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-211">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="7a09b-212">Gestione degli errori</span><span class="sxs-lookup"><span data-stu-id="7a09b-212">Error handling</span></span>

<span data-ttu-id="7a09b-213">ASP.NET Core dispone di funzionalità predefinite per la gestione degli errori, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a09b-213">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="7a09b-214">Una pagina delle eccezioni per gli sviluppatori</span><span class="sxs-lookup"><span data-stu-id="7a09b-214">A developer exception page</span></span>
* <span data-ttu-id="7a09b-215">Pagine di errore personalizzate</span><span class="sxs-lookup"><span data-stu-id="7a09b-215">Custom error pages</span></span>
* <span data-ttu-id="7a09b-216">Pagine dei codici di stato statiche</span><span class="sxs-lookup"><span data-stu-id="7a09b-216">Static status code pages</span></span>
* <span data-ttu-id="7a09b-217">Gestione delle eccezioni durante l'avvio</span><span class="sxs-lookup"><span data-stu-id="7a09b-217">Startup exception handling</span></span>

<span data-ttu-id="7a09b-218">Per altre informazioni, vedere <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-218">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="7a09b-219">Creare richieste HTTP</span><span class="sxs-lookup"><span data-stu-id="7a09b-219">Make HTTP requests</span></span>

<span data-ttu-id="7a09b-220">Un'implementazione di `IHttpClientFactory` è disponibile per la creazione di istanze `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="7a09b-220">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="7a09b-221">Il factory:</span><span class="sxs-lookup"><span data-stu-id="7a09b-221">The factory:</span></span>

* <span data-ttu-id="7a09b-222">Offre una posizione centrale per la denominazione e la configurazione di istanze di `HttpClient` logiche.</span><span class="sxs-lookup"><span data-stu-id="7a09b-222">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="7a09b-223">Ad esempio, registrare e configurare un client *GitHub* per l'accesso a github.</span><span class="sxs-lookup"><span data-stu-id="7a09b-223">For example, register and configure a *github* client for accessing GitHub.</span></span> <span data-ttu-id="7a09b-224">Registrare e configurare un client predefinito per altri scopi.</span><span class="sxs-lookup"><span data-stu-id="7a09b-224">Register and configure a default client for other purposes.</span></span>
* <span data-ttu-id="7a09b-225">Supporta la registrazione e il concatenamento di più gestori di delega per creare una pipeline di middleware per le richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="7a09b-225">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="7a09b-226">Questo modello è simile alla pipeline middleware in ingresso ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7a09b-226">This pattern is similar to ASP.NET Core's inbound middleware pipeline.</span></span> <span data-ttu-id="7a09b-227">Il modello fornisce un meccanismo per gestire le problematiche trasversali per le richieste HTTP, ad esempio la memorizzazione nella cache, la gestione degli errori, la serializzazione e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="7a09b-227">The pattern provides a mechanism to manage cross-cutting concerns for HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="7a09b-228">Si integra con *Polly*, una famosa libreria di terze parti per la gestione degli errori temporanei.</span><span class="sxs-lookup"><span data-stu-id="7a09b-228">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="7a09b-229">Gestisce il pool e la durata delle istanze sottostanti `HttpClientHandler` per evitare problemi DNS comuni che si verificano quando si gestiscono le `HttpClient` durate manualmente.</span><span class="sxs-lookup"><span data-stu-id="7a09b-229">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when managing `HttpClient` lifetimes manually.</span></span>
* <span data-ttu-id="7a09b-230">Aggiunge un'esperienza di registrazione configurabile tramite <xref:Microsoft.Extensions.Logging.ILogger> per tutte le richieste inviate tramite client creati dalla factory.</span><span class="sxs-lookup"><span data-stu-id="7a09b-230">Adds a configurable logging experience via <xref:Microsoft.Extensions.Logging.ILogger> for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="7a09b-231">Per altre informazioni, vedere <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-231">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="7a09b-232">Radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="7a09b-232">Content root</span></span>

<span data-ttu-id="7a09b-233">La radice del contenuto è il percorso di base per:</span><span class="sxs-lookup"><span data-stu-id="7a09b-233">The content root is the base path for:</span></span>

* <span data-ttu-id="7a09b-234">Eseguibile che ospita l'app (*exe*).</span><span class="sxs-lookup"><span data-stu-id="7a09b-234">The executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="7a09b-235">Assembly compilati che costituiscono l'app (*. dll*).</span><span class="sxs-lookup"><span data-stu-id="7a09b-235">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="7a09b-236">File di contenuto usati dall'app, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a09b-236">Content files used by the app, such as:</span></span>
  * Razor<span data-ttu-id="7a09b-237">file (*. cshtml*, *. Razor*)</span><span class="sxs-lookup"><span data-stu-id="7a09b-237"> files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="7a09b-238">File di configurazione (con*estensione JSON*, *XML*)</span><span class="sxs-lookup"><span data-stu-id="7a09b-238">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="7a09b-239">File di dati (*. DB*)</span><span class="sxs-lookup"><span data-stu-id="7a09b-239">Data files (*.db*)</span></span>
* <span data-ttu-id="7a09b-240">La [radice Web](#web-root), in genere la cartella *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="7a09b-240">The [Web root](#web-root), typically the *wwwroot* folder.</span></span>

<span data-ttu-id="7a09b-241">Durante lo sviluppo, il valore predefinito della radice del contenuto è la directory radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="7a09b-241">During development, the content root defaults to the project's root directory.</span></span> <span data-ttu-id="7a09b-242">Questa directory è anche il percorso di base per i file di contenuto dell'app e per la [radice Web](#web-root).</span><span class="sxs-lookup"><span data-stu-id="7a09b-242">This directory is also the base path for both the app's content files and the [Web root](#web-root).</span></span> <span data-ttu-id="7a09b-243">Specificare una radice del contenuto diversa impostando il relativo percorso durante [la compilazione dell'host](#host).</span><span class="sxs-lookup"><span data-stu-id="7a09b-243">Specify a different content root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="7a09b-244">Per altre informazioni, vedere [Radice del contenuto](xref:fundamentals/host/generic-host#contentroot).</span><span class="sxs-lookup"><span data-stu-id="7a09b-244">For more information, see [Content root](xref:fundamentals/host/generic-host#contentroot).</span></span>

## <a name="web-root"></a><span data-ttu-id="7a09b-245">Radice Web</span><span class="sxs-lookup"><span data-stu-id="7a09b-245">Web root</span></span>

<span data-ttu-id="7a09b-246">La radice Web è il percorso di base per i file di risorse statici pubblici, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a09b-246">The web root is the base path for public, static resource files, such as:</span></span>

* <span data-ttu-id="7a09b-247">Fogli di stile (*. CSS*)</span><span class="sxs-lookup"><span data-stu-id="7a09b-247">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="7a09b-248">JavaScript (*. js*)</span><span class="sxs-lookup"><span data-stu-id="7a09b-248">JavaScript (*.js*)</span></span>
* <span data-ttu-id="7a09b-249">Immagini (*. png*, *. jpg*)</span><span class="sxs-lookup"><span data-stu-id="7a09b-249">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="7a09b-250">Per impostazione predefinita, i file statici vengono serviti solo dalla directory radice Web e dalle relative sottodirectory.</span><span class="sxs-lookup"><span data-stu-id="7a09b-250">By default, static files are served only from the web root directory and its sub-directories.</span></span> <span data-ttu-id="7a09b-251">Per impostazione predefinita, il percorso radice Web è *{Content root}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="7a09b-251">The web root path defaults to *{content root}/wwwroot*.</span></span> <span data-ttu-id="7a09b-252">Specificare un'altra radice Web impostando il relativo percorso durante [la compilazione dell'host](#host).</span><span class="sxs-lookup"><span data-stu-id="7a09b-252">Specify a different web root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="7a09b-253">Per altre informazioni, vedere [Web root](xref:fundamentals/host/generic-host#webroot) (Radice Web).</span><span class="sxs-lookup"><span data-stu-id="7a09b-253">For more information, see [Web root](xref:fundamentals/host/generic-host#webroot).</span></span>

<span data-ttu-id="7a09b-254">Impedire la pubblicazione di file in *wwwroot* con l' [ \<Content> elemento del progetto](/visualstudio/msbuild/common-msbuild-project-items#content) nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="7a09b-254">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="7a09b-255">Nell'esempio seguente viene impedita la pubblicazione di contenuto in *wwwroot/local* e nelle relative sottodirectory:</span><span class="sxs-lookup"><span data-stu-id="7a09b-255">The following example prevents publishing content in *wwwroot/local* and its sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="7a09b-256">Nei Razor file con *estensione cshtml* , tilde-Slash ( `~/` ) punta alla radice Web.</span><span class="sxs-lookup"><span data-stu-id="7a09b-256">In Razor *.cshtml* files, tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="7a09b-257">Un percorso che inizia con `~/` viene definito *percorso virtuale*.</span><span class="sxs-lookup"><span data-stu-id="7a09b-257">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="7a09b-258">Per altre informazioni, vedere <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-258">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7a09b-259">Questo articolo contiene una panoramica degli argomenti chiave necessari per comprendere come sviluppare app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7a09b-259">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="7a09b-260">Classe Startup</span><span class="sxs-lookup"><span data-stu-id="7a09b-260">The Startup class</span></span>

<span data-ttu-id="7a09b-261">All'interno della classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="7a09b-261">The `Startup` class is where:</span></span>

* <span data-ttu-id="7a09b-262">Vengono configurati i servizi necessari per l'app.</span><span class="sxs-lookup"><span data-stu-id="7a09b-262">Services required by the app are configured.</span></span>
* <span data-ttu-id="7a09b-263">Viene definita la pipeline di gestione delle richieste.</span><span class="sxs-lookup"><span data-stu-id="7a09b-263">The request handling pipeline is defined.</span></span>

<span data-ttu-id="7a09b-264">I *servizi* sono componenti usati dall'app.</span><span class="sxs-lookup"><span data-stu-id="7a09b-264">*Services* are components that are used by the app.</span></span> <span data-ttu-id="7a09b-265">Ad esempio, un componente di registrazione è un servizio.</span><span class="sxs-lookup"><span data-stu-id="7a09b-265">For example, a logging component is a service.</span></span> <span data-ttu-id="7a09b-266">Il codice per configurare o *registrare* i servizi viene aggiunto al metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7a09b-266">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="7a09b-267">La pipeline di gestione delle richieste è costituita da una serie di componenti *middleware* .</span><span class="sxs-lookup"><span data-stu-id="7a09b-267">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="7a09b-268">Un componente middleware, ad esempio, potrebbe gestire le richieste per i file statici o reindirizzare le richieste HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7a09b-268">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="7a09b-269">Ogni componente middleware esegue operazioni asincrone su un `HttpContext`, quindi richiama il componente middleware successivo della pipeline oppure termina la richiesta.</span><span class="sxs-lookup"><span data-stu-id="7a09b-269">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="7a09b-270">Il codice per configurare la pipeline di gestione delle richieste viene aggiunto al metodo `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="7a09b-270">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="7a09b-271">Ecco un esempio di classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="7a09b-271">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="7a09b-272">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="7a09b-273">Iniezione di dipendenze (servizi)</span><span class="sxs-lookup"><span data-stu-id="7a09b-273">Dependency injection (services)</span></span>

<span data-ttu-id="7a09b-274">ASP.NET Core include un framework di inserimento delle dipendenze predefinito che rende disponibili i servizi configurati per le classi di un'app.</span><span class="sxs-lookup"><span data-stu-id="7a09b-274">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="7a09b-275">Un modo per inserire un'istanza di un servizio in una classe consiste nel creare un costruttore con un parametro del tipo richiesto.</span><span class="sxs-lookup"><span data-stu-id="7a09b-275">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="7a09b-276">Il parametro può essere il tipo di servizio o un'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="7a09b-276">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="7a09b-277">Il sistema di inserimento delle dipendenze fornisce il servizio in runtime.</span><span class="sxs-lookup"><span data-stu-id="7a09b-277">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="7a09b-278">Di seguito viene proposto un esempio di classe che usa l'inserimento delle dipendenze per ottenere un oggetto di contesto Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="7a09b-278">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="7a09b-279">La riga evidenziata è un esempio di inserimento costruttore:</span><span class="sxs-lookup"><span data-stu-id="7a09b-279">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="7a09b-280">Nonostante l'inserimento delle dipendenze sia predefinito, è progettato per consentire il collegamento di un contenitore di inversione del controllo (IoC) di terze parti, qualora lo si preferisse.</span><span class="sxs-lookup"><span data-stu-id="7a09b-280">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="7a09b-281">Per altre informazioni, vedere <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-281">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="7a09b-282">Middleware</span><span class="sxs-lookup"><span data-stu-id="7a09b-282">Middleware</span></span>

<span data-ttu-id="7a09b-283">La pipeline di gestione delle richieste è strutturata come una serie di componenti middleware.</span><span class="sxs-lookup"><span data-stu-id="7a09b-283">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="7a09b-284">Ogni componente esegue operazioni asincrone su un `HttpContext`, quindi richiama il componente middleware successivo nella pipeline oppure termina la richiesta.</span><span class="sxs-lookup"><span data-stu-id="7a09b-284">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="7a09b-285">Per convenzione viene aggiunto un componente middleware alla pipeline richiamando il relativo metodo di estensione `Use...` nel metodo `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="7a09b-285">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="7a09b-286">Per abilitare il rendering dei file statici, ad esempio, chiamare `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="7a09b-286">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="7a09b-287">Il codice evidenziato nell'esempio seguente configura la pipeline di gestione delle richieste:</span><span class="sxs-lookup"><span data-stu-id="7a09b-287">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

<span data-ttu-id="7a09b-288">ASP.NET Core include un ampio set di middleware predefiniti, ma consente anche di scrivere middleware personalizzati.</span><span class="sxs-lookup"><span data-stu-id="7a09b-288">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="7a09b-289">Per altre informazioni, vedere <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-289">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="7a09b-290">Host</span><span class="sxs-lookup"><span data-stu-id="7a09b-290">Host</span></span>

<span data-ttu-id="7a09b-291">Un'app ASP.NET Core crea un *host* all'avvio.</span><span class="sxs-lookup"><span data-stu-id="7a09b-291">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="7a09b-292">L'host è un oggetto che incapsula tutte le risorse dell'app, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a09b-292">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="7a09b-293">Un'implementazione del server HTTP</span><span class="sxs-lookup"><span data-stu-id="7a09b-293">An HTTP server implementation</span></span>
* <span data-ttu-id="7a09b-294">I componenti middleware</span><span class="sxs-lookup"><span data-stu-id="7a09b-294">Middleware components</span></span>
* <span data-ttu-id="7a09b-295">Registrazione</span><span class="sxs-lookup"><span data-stu-id="7a09b-295">Logging</span></span>
* <span data-ttu-id="7a09b-296">DI</span><span class="sxs-lookup"><span data-stu-id="7a09b-296">DI</span></span>
* <span data-ttu-id="7a09b-297">Configurazione</span><span class="sxs-lookup"><span data-stu-id="7a09b-297">Configuration</span></span>

<span data-ttu-id="7a09b-298">Il motivo principale per cui tutte le risorse interdipendenti dell'app sono incluse in un unico oggetto è la gestione del ciclo di vita, vale a dire il controllo sull'avvio dell'app e sull'arresto normale.</span><span class="sxs-lookup"><span data-stu-id="7a09b-298">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="7a09b-299">Sono disponibili due host: l'host Web e l'host generico.</span><span class="sxs-lookup"><span data-stu-id="7a09b-299">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="7a09b-300">In ASP.NET Core 2.x, l'host generico è destinato solo agli scenari non Web.</span><span class="sxs-lookup"><span data-stu-id="7a09b-300">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="7a09b-301">Il codice per creare un host si trova in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="7a09b-301">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

<span data-ttu-id="7a09b-302">Il metodo `CreateDefaultBuilder` configura un host con le opzioni usate comunemente, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a09b-302">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="7a09b-303">Usare [Kestrel](#servers) come server Web e abilitare l'integrazione IIS.</span><span class="sxs-lookup"><span data-stu-id="7a09b-303">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="7a09b-304">Caricare la configurazione da *appsettings.json*, *appsettings.[EnvironmentName].json*, variabili di ambiente, argomenti della riga di comando e altri origini di configurazione.</span><span class="sxs-lookup"><span data-stu-id="7a09b-304">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="7a09b-305">Inviare l'output di registrazione alla console e ai provider di debug.</span><span class="sxs-lookup"><span data-stu-id="7a09b-305">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="7a09b-306">Per altre informazioni, vedere <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-306">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="7a09b-307">Scenari non Web</span><span class="sxs-lookup"><span data-stu-id="7a09b-307">Non-web scenarios</span></span>

<span data-ttu-id="7a09b-308">L'host generico consente ad altri tipi di app di usare estensioni del framework trasversali quali la registrazione, l'inserimento delle dipendenze, la configurazione e la gestione del ciclo di vita delle app.</span><span class="sxs-lookup"><span data-stu-id="7a09b-308">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="7a09b-309">Per altre informazioni, vedere <xref:fundamentals/host/generic-host> e <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-309">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="7a09b-310">Server</span><span class="sxs-lookup"><span data-stu-id="7a09b-310">Servers</span></span>

<span data-ttu-id="7a09b-311">Un'app ASP.NET Core usa un'implementazione del server HTTP per l'ascolto delle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="7a09b-311">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="7a09b-312">Il server espone le richieste all'app sotto forma di insieme di [funzionalità di richiesta](xref:fundamentals/request-features) strutturate in un `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="7a09b-312">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="7a09b-313">Windows</span><span class="sxs-lookup"><span data-stu-id="7a09b-313">Windows</span></span>](#tab/windows)

<span data-ttu-id="7a09b-314">ASP.NET Core include le implementazioni server seguenti:</span><span class="sxs-lookup"><span data-stu-id="7a09b-314">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="7a09b-315">*Kestrel* è un server Web multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="7a09b-315">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="7a09b-316">Kestrel viene spesso eseguito in una configurazione con proxy inverso tramite [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="7a09b-316">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="7a09b-317">Il gheppio può essere eseguito come server perimetrale pubblico esposto direttamente a Internet.</span><span class="sxs-lookup"><span data-stu-id="7a09b-317">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="7a09b-318">*Server HTTP IIS* è un server per Windows che usa IIS.</span><span class="sxs-lookup"><span data-stu-id="7a09b-318">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="7a09b-319">Con questo server l'app ASP.NET Core e IIS sono eseguiti nello stesso processo.</span><span class="sxs-lookup"><span data-stu-id="7a09b-319">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="7a09b-320">*HTTP.sys* è un server per Windows che non viene usato con IIS.</span><span class="sxs-lookup"><span data-stu-id="7a09b-320">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="7a09b-321">macOS</span><span class="sxs-lookup"><span data-stu-id="7a09b-321">macOS</span></span>](#tab/macos)

<span data-ttu-id="7a09b-322">ASP.NET Core fornisce l'implementazione del server multipiattaforma *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="7a09b-322">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="7a09b-323">Il gheppio può essere eseguito come server perimetrale pubblico esposto direttamente a Internet.</span><span class="sxs-lookup"><span data-stu-id="7a09b-323">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="7a09b-324">Kestrel viene spesso eseguito in una configurazione con proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="7a09b-324">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="7a09b-325">Linux</span><span class="sxs-lookup"><span data-stu-id="7a09b-325">Linux</span></span>](#tab/linux)

<span data-ttu-id="7a09b-326">ASP.NET Core fornisce l'implementazione del server multipiattaforma *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="7a09b-326">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="7a09b-327">Il gheppio può essere eseguito come server perimetrale pubblico esposto direttamente a Internet.</span><span class="sxs-lookup"><span data-stu-id="7a09b-327">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="7a09b-328">Kestrel viene spesso eseguito in una configurazione con proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="7a09b-328">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="7a09b-329">Windows</span><span class="sxs-lookup"><span data-stu-id="7a09b-329">Windows</span></span>](#tab/windows)

<span data-ttu-id="7a09b-330">ASP.NET Core include le implementazioni server seguenti:</span><span class="sxs-lookup"><span data-stu-id="7a09b-330">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="7a09b-331">*Kestrel* è un server Web multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="7a09b-331">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="7a09b-332">Kestrel viene spesso eseguito in una configurazione con proxy inverso tramite [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="7a09b-332">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="7a09b-333">Il gheppio può essere eseguito come server perimetrale pubblico esposto direttamente a Internet.</span><span class="sxs-lookup"><span data-stu-id="7a09b-333">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="7a09b-334">*HTTP.sys* è un server per Windows che non viene usato con IIS.</span><span class="sxs-lookup"><span data-stu-id="7a09b-334">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="7a09b-335">macOS</span><span class="sxs-lookup"><span data-stu-id="7a09b-335">macOS</span></span>](#tab/macos)

<span data-ttu-id="7a09b-336">ASP.NET Core fornisce l'implementazione del server multipiattaforma *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="7a09b-336">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="7a09b-337">Il gheppio può essere eseguito come server perimetrale pubblico esposto direttamente a Internet.</span><span class="sxs-lookup"><span data-stu-id="7a09b-337">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="7a09b-338">Kestrel viene spesso eseguito in una configurazione con proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="7a09b-338">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="7a09b-339">Linux</span><span class="sxs-lookup"><span data-stu-id="7a09b-339">Linux</span></span>](#tab/linux)

<span data-ttu-id="7a09b-340">ASP.NET Core fornisce l'implementazione del server multipiattaforma *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="7a09b-340">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="7a09b-341">Il gheppio può essere eseguito come server perimetrale pubblico esposto direttamente a Internet.</span><span class="sxs-lookup"><span data-stu-id="7a09b-341">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="7a09b-342">Kestrel viene spesso eseguito in una configurazione con proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="7a09b-342">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7a09b-343">Per altre informazioni, vedere <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-343">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="7a09b-344">Configurazione</span><span class="sxs-lookup"><span data-stu-id="7a09b-344">Configuration</span></span>

<span data-ttu-id="7a09b-345">ASP.NET Core offre un framework di configurazione che ottiene le impostazioni, ad esempio coppie nome-valore, da un set ordinato di provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="7a09b-345">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="7a09b-346">Esistono provider di configurazione predefiniti per un'ampia gamma di origini, ad esempio file con estensione *JSON*, file con estensione *XML*, variabili di ambiente e argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="7a09b-346">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="7a09b-347">È anche possibile scrivere provider di configurazione personalizzati.</span><span class="sxs-lookup"><span data-stu-id="7a09b-347">You can also write custom configuration providers.</span></span>

<span data-ttu-id="7a09b-348">Si potrebbe specificare, ad esempio, che la configurazione proviene da *appsettings.json* e da variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="7a09b-348">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="7a09b-349">Quindi, quando viene richiesto il valore di *ConnectionString*, il framework esaminerebbe per primo il file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7a09b-349">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="7a09b-350">Se il valore venisse trovato qui ma anche in una variabile di ambiente, il valore della variabile di ambiente avrebbe la precedenza.</span><span class="sxs-lookup"><span data-stu-id="7a09b-350">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="7a09b-351">Per la gestione dei dati di configurazione riservati, ad esempio le password, ASP.NET Core offre uno [strumento Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="7a09b-351">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="7a09b-352">Per i segreti di produzione, si consiglia di usare [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="7a09b-352">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="7a09b-353">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-353">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="7a09b-354">Opzioni</span><span class="sxs-lookup"><span data-stu-id="7a09b-354">Options</span></span>

<span data-ttu-id="7a09b-355">Ove possibile, ASP.NET Core segue il *modello di opzioni* per archiviare e recuperare i valori di configurazione.</span><span class="sxs-lookup"><span data-stu-id="7a09b-355">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="7a09b-356">Il modello di opzioni usa le classi per rappresentare i gruppi di impostazioni correlate.</span><span class="sxs-lookup"><span data-stu-id="7a09b-356">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="7a09b-357">Il codice seguente, ad esempio, imposta le opzioni WebSockets:</span><span class="sxs-lookup"><span data-stu-id="7a09b-357">For example, the following code sets WebSockets options:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

<span data-ttu-id="7a09b-358">Per altre informazioni, vedere <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-358">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="7a09b-359">Ambienti</span><span class="sxs-lookup"><span data-stu-id="7a09b-359">Environments</span></span>

<span data-ttu-id="7a09b-360">Gli ambienti di esecuzione, ad esempio *Sviluppo*, *Staging* e *Produzione*, sono un concetto fondamentale in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7a09b-360">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="7a09b-361">È possibile specificare l'ambiente in cui viene eseguita un'app impostando la variabile di ambiente `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="7a09b-361">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="7a09b-362">ASP.NET Core legge tale variabile di ambiente all'avvio dell'app e ne archivia il valore in un'implementazione `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="7a09b-362">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="7a09b-363">L'oggetto ambiente è disponibile in un punto qualsiasi dell'app tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7a09b-363">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="7a09b-364">Il seguente codice di esempio della classe `Startup` configura l'app in modo che fornisca informazioni dettagliate sull'errore solo quando viene eseguita nell'ambiente di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="7a09b-364">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="7a09b-365">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-365">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="7a09b-366">Registrazione</span><span class="sxs-lookup"><span data-stu-id="7a09b-366">Logging</span></span>

<span data-ttu-id="7a09b-367">ASP.NET Core supporta un'API di registrazione che funziona con un'ampia gamma di provider di registrazione predefiniti e di terze parti.</span><span class="sxs-lookup"><span data-stu-id="7a09b-367">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="7a09b-368">Tra i provider disponibili sono inclusi i seguenti:</span><span class="sxs-lookup"><span data-stu-id="7a09b-368">Available providers include the following:</span></span>

* <span data-ttu-id="7a09b-369">Console</span><span class="sxs-lookup"><span data-stu-id="7a09b-369">Console</span></span>
* <span data-ttu-id="7a09b-370">Debug</span><span class="sxs-lookup"><span data-stu-id="7a09b-370">Debug</span></span>
* <span data-ttu-id="7a09b-371">Event Tracing for Windows</span><span class="sxs-lookup"><span data-stu-id="7a09b-371">Event Tracing on Windows</span></span>
* <span data-ttu-id="7a09b-372">Registro eventi di Windows</span><span class="sxs-lookup"><span data-stu-id="7a09b-372">Windows Event Log</span></span>
* <span data-ttu-id="7a09b-373">TraceSource</span><span class="sxs-lookup"><span data-stu-id="7a09b-373">TraceSource</span></span>
* <span data-ttu-id="7a09b-374">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="7a09b-374">Azure App Service</span></span>
* <span data-ttu-id="7a09b-375">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="7a09b-375">Azure Application Insights</span></span>

<span data-ttu-id="7a09b-376">Scrivere i log da un punto qualsiasi del codice di un'app recuperando un oggetto `ILogger` dall'inserimento delle dipendenze e chiamando i metodi di registrazione.</span><span class="sxs-lookup"><span data-stu-id="7a09b-376">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="7a09b-377">Ecco un esempio di codice che usa un oggetto `ILogger` con inserimento costruttore e chiamate al metodo di registrazione evidenziati.</span><span class="sxs-lookup"><span data-stu-id="7a09b-377">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="7a09b-378">L'interfaccia `ILogger` consente di passare qualsiasi numero di campi al provider di registrazione.</span><span class="sxs-lookup"><span data-stu-id="7a09b-378">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="7a09b-379">I campi vengono usati comunemente per costruire una stringa di messaggio, ma il provider può anche inviarli come campi separati a un archivio dati.</span><span class="sxs-lookup"><span data-stu-id="7a09b-379">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="7a09b-380">Questa funzionalità consente ai provider di registrazione di implementare la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="7a09b-380">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="7a09b-381">Per altre informazioni, vedere <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-381">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="7a09b-382">Routing.</span><span class="sxs-lookup"><span data-stu-id="7a09b-382">Routing</span></span>

<span data-ttu-id="7a09b-383">Una *route* è un modello URL di cui è stato eseguito il mapping su un gestore.</span><span class="sxs-lookup"><span data-stu-id="7a09b-383">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="7a09b-384">Il gestore è in genere una Razor pagina, un metodo di azione in un controller MVC o un middleware.</span><span class="sxs-lookup"><span data-stu-id="7a09b-384">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="7a09b-385">Il routing di ASP.NET Core consente di controllare gli URL usati dall'app.</span><span class="sxs-lookup"><span data-stu-id="7a09b-385">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="7a09b-386">Per altre informazioni, vedere <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-386">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="7a09b-387">Gestione degli errori</span><span class="sxs-lookup"><span data-stu-id="7a09b-387">Error handling</span></span>

<span data-ttu-id="7a09b-388">ASP.NET Core dispone di funzionalità predefinite per la gestione degli errori, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a09b-388">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="7a09b-389">Una pagina delle eccezioni per gli sviluppatori</span><span class="sxs-lookup"><span data-stu-id="7a09b-389">A developer exception page</span></span>
* <span data-ttu-id="7a09b-390">Pagine di errore personalizzate</span><span class="sxs-lookup"><span data-stu-id="7a09b-390">Custom error pages</span></span>
* <span data-ttu-id="7a09b-391">Pagine dei codici di stato statiche</span><span class="sxs-lookup"><span data-stu-id="7a09b-391">Static status code pages</span></span>
* <span data-ttu-id="7a09b-392">Gestione delle eccezioni durante l'avvio</span><span class="sxs-lookup"><span data-stu-id="7a09b-392">Startup exception handling</span></span>

<span data-ttu-id="7a09b-393">Per altre informazioni, vedere <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-393">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="7a09b-394">Creare richieste HTTP</span><span class="sxs-lookup"><span data-stu-id="7a09b-394">Make HTTP requests</span></span>

<span data-ttu-id="7a09b-395">Un'implementazione di `IHttpClientFactory` è disponibile per la creazione di istanze `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="7a09b-395">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="7a09b-396">Il factory:</span><span class="sxs-lookup"><span data-stu-id="7a09b-396">The factory:</span></span>

* <span data-ttu-id="7a09b-397">Offre una posizione centrale per la denominazione e la configurazione di istanze di `HttpClient` logiche.</span><span class="sxs-lookup"><span data-stu-id="7a09b-397">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="7a09b-398">Ad esempio, un client *GitHub* può essere registrato e configurato per accedere a github.</span><span class="sxs-lookup"><span data-stu-id="7a09b-398">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="7a09b-399">È possibile registrare un client predefinito per altri scopi.</span><span class="sxs-lookup"><span data-stu-id="7a09b-399">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="7a09b-400">Supporta la registrazione e il concatenamento di più gestori di delega per creare una pipeline di middleware per le richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="7a09b-400">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="7a09b-401">Questo modello è simile alla pipeline di middleware in ingresso in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7a09b-401">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="7a09b-402">Il modello offre un meccanismo per gestire le problematiche trasversali relative alle richieste HTTP, tra cui memorizzazione nella cache, gestione degli errori, serializzazione e registrazione.</span><span class="sxs-lookup"><span data-stu-id="7a09b-402">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="7a09b-403">Si integra con *Polly*, una famosa libreria di terze parti per la gestione degli errori temporanei.</span><span class="sxs-lookup"><span data-stu-id="7a09b-403">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="7a09b-404">Gestisce il pooling e la durata delle istanze di `HttpClientHandler` sottostanti per evitare problemi DNS comuni che si verificano quando le durate di `HttpClient` vengono gestite manualmente.</span><span class="sxs-lookup"><span data-stu-id="7a09b-404">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="7a09b-405">Aggiunge un'esperienza di registrazione configurabile, tramite `ILogger`, per tutte le richieste inviate attraverso i client creati dalla factory.</span><span class="sxs-lookup"><span data-stu-id="7a09b-405">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="7a09b-406">Per altre informazioni, vedere <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-406">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="7a09b-407">Radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="7a09b-407">Content root</span></span>

<span data-ttu-id="7a09b-408">La radice del contenuto è il percorso di base di:</span><span class="sxs-lookup"><span data-stu-id="7a09b-408">The content root is the base path to the:</span></span>

* <span data-ttu-id="7a09b-409">Eseguibile che ospita l'app (*exe*).</span><span class="sxs-lookup"><span data-stu-id="7a09b-409">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="7a09b-410">Assembly compilati che costituiscono l'app (*. dll*).</span><span class="sxs-lookup"><span data-stu-id="7a09b-410">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="7a09b-411">File di contenuto non di codice usati dall'app, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a09b-411">Non-code content files used by the app, such as:</span></span>
  * Razor<span data-ttu-id="7a09b-412">file (*. cshtml*, *. Razor*)</span><span class="sxs-lookup"><span data-stu-id="7a09b-412"> files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="7a09b-413">File di configurazione (con*estensione JSON*, *XML*)</span><span class="sxs-lookup"><span data-stu-id="7a09b-413">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="7a09b-414">File di dati (*. DB*)</span><span class="sxs-lookup"><span data-stu-id="7a09b-414">Data files (*.db*)</span></span>
* <span data-ttu-id="7a09b-415">[Radice Web](#web-root), in genere la cartella *wwwroot* pubblicata.</span><span class="sxs-lookup"><span data-stu-id="7a09b-415">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="7a09b-416">Durante lo sviluppo:</span><span class="sxs-lookup"><span data-stu-id="7a09b-416">During development:</span></span>

* <span data-ttu-id="7a09b-417">Per impostazione predefinita, la radice del contenuto è la directory radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="7a09b-417">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="7a09b-418">La directory radice del progetto viene utilizzata per creare:</span><span class="sxs-lookup"><span data-stu-id="7a09b-418">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="7a09b-419">Percorso dei file di contenuto non di codice dell'app nella directory radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="7a09b-419">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="7a09b-420">[Radice Web](#web-root), in genere la cartella *wwwroot* nella directory radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="7a09b-420">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="7a09b-421">Quando si [Compila l'host](#host), è possibile specificare un percorso radice del contenuto alternativo.</span><span class="sxs-lookup"><span data-stu-id="7a09b-421">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="7a09b-422">Per altre informazioni, vedere <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-422">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="7a09b-423">Radice Web</span><span class="sxs-lookup"><span data-stu-id="7a09b-423">Web root</span></span>

<span data-ttu-id="7a09b-424">La radice Web è il percorso di base per i file di risorse pubblici, non di codice, statici, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7a09b-424">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="7a09b-425">Fogli di stile (*. CSS*)</span><span class="sxs-lookup"><span data-stu-id="7a09b-425">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="7a09b-426">JavaScript (*. js*)</span><span class="sxs-lookup"><span data-stu-id="7a09b-426">JavaScript (*.js*)</span></span>
* <span data-ttu-id="7a09b-427">Immagini (*. png*, *. jpg*)</span><span class="sxs-lookup"><span data-stu-id="7a09b-427">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="7a09b-428">I file statici vengono serviti per impostazione predefinita solo dalla directory radice Web (e dalle sottodirectory).</span><span class="sxs-lookup"><span data-stu-id="7a09b-428">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="7a09b-429">Per impostazione predefinita, il percorso radice Web è *{Content root}/wwwroot*, ma è possibile specificare una radice Web diversa durante [la compilazione dell'host](#host).</span><span class="sxs-lookup"><span data-stu-id="7a09b-429">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="7a09b-430">Per altre informazioni, vedere [Web root](xref:fundamentals/host/web-host#web-root) (Radice Web).</span><span class="sxs-lookup"><span data-stu-id="7a09b-430">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="7a09b-431">Impedire la pubblicazione di file in *wwwroot* con l' [ \<Content> elemento del progetto](/visualstudio/msbuild/common-msbuild-project-items#content) nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="7a09b-431">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="7a09b-432">Nell'esempio seguente viene impedita la pubblicazione di contenuto nella directory *wwwroot/locale* e nelle sottodirectory:</span><span class="sxs-lookup"><span data-stu-id="7a09b-432">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="7a09b-433">Nei Razor file (con*estensione cshtml*), la barra tilde ( `~/` ) punta alla radice Web.</span><span class="sxs-lookup"><span data-stu-id="7a09b-433">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="7a09b-434">Un percorso che inizia con `~/` viene definito *percorso virtuale*.</span><span class="sxs-lookup"><span data-stu-id="7a09b-434">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="7a09b-435">Per altre informazioni, vedere <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="7a09b-435">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
