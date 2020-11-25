---
title: Host Web ASP.NET Core
author: rick-anderson
description: Informazioni sull'host Web in ASP.NET Core, responsabile della gestione dell'avvio e della durata delle app.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
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
uid: fundamentals/host/web-host
ms.openlocfilehash: 904b57f95cbc48a8177174dc9be770e8a6abf146
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035879"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="ce742-103">Host Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce742-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="ce742-104">ASP.NET Core le app configurano e avviano un *host*.</span><span class="sxs-lookup"><span data-stu-id="ce742-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="ce742-105">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="ce742-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="ce742-106">L'host configura almeno un server e una pipeline di elaborazione delle richieste.</span><span class="sxs-lookup"><span data-stu-id="ce742-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="ce742-107">L'host può anche configurare la registrazione, l'inserimento delle dipendenze e la configurazione.</span><span class="sxs-lookup"><span data-stu-id="ce742-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ce742-108">Questo articolo descrive l'host Web, che rimane disponibile solo per compatibilità con le versioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="ce742-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="ce742-109">L'[host generico](xref:fundamentals/host/generic-host) è consigliato per tutti i tipi di app.</span><span class="sxs-lookup"><span data-stu-id="ce742-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ce742-110">Questo articolo descrive l'host Web destinato all'hosting di app Web.</span><span class="sxs-lookup"><span data-stu-id="ce742-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="ce742-111">Per altri tipi di app, usare l'[host generico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="ce742-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="ce742-112">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="ce742-112">Set up a host</span></span>

<span data-ttu-id="ce742-113">Creare un host usando un'istanza di [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="ce742-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="ce742-114">Questa operazione viene in genere eseguita nel punto di ingresso dell'app, il metodo `Main`.</span><span class="sxs-lookup"><span data-stu-id="ce742-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="ce742-115">Nei modelli di progetto `Main` si trova in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="ce742-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="ce742-116">Un'app tipica chiama [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) per avviare la configurazione di un host:</span><span class="sxs-lookup"><span data-stu-id="ce742-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

<span data-ttu-id="ce742-117">Il codice che chiama `CreateDefaultBuilder` è incluso in un metodo denominato `CreateWebHostBuilder`, che lo separa dal codice in `Main` che chiama `Run` per l'oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="ce742-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="ce742-118">Questa separazione è necessaria se si usano gli [strumenti di Entity Framework Core](/ef/core/miscellaneous/cli/).</span><span class="sxs-lookup"><span data-stu-id="ce742-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="ce742-119">Gli strumenti si aspettano di trovare un metodo `CreateWebHostBuilder` che possono chiamare in fase di progettazione per configurare l'host senza eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="ce742-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="ce742-120">Un'alternativa consiste nell'implementare `IDesignTimeDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="ce742-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="ce742-121">Per altre informazioni, vedere [Creazione DbContext in fase di progettazione](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="ce742-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="ce742-122">`CreateDefaultBuilder` esegue queste attività:</span><span class="sxs-lookup"><span data-stu-id="ce742-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="ce742-123">Configura il server [Kestrel](xref:fundamentals/servers/kestrel) come server Web usando i provider di configurazione dell'host dell'app.</span><span class="sxs-lookup"><span data-stu-id="ce742-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="ce742-124">Per le opzioni predefinite del server Kestrel, vedere <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="ce742-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="ce742-125">Imposta la [radice del contenuto](xref:fundamentals/index#content-root) sul percorso restituito da [Directory. GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="ce742-125">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="ce742-126">Carica la [configurazione dell'host](#host-configuration-values) da:</span><span class="sxs-lookup"><span data-stu-id="ce742-126">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="ce742-127">Le variabili di ambiente con prefisso `ASPNETCORE_` (ad esempio, `ASPNETCORE_ENVIRONMENT`).</span><span class="sxs-lookup"><span data-stu-id="ce742-127">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="ce742-128">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="ce742-128">Command-line arguments.</span></span>
* <span data-ttu-id="ce742-129">Carica la configurazione dell'app nell'ordine seguente da:</span><span class="sxs-lookup"><span data-stu-id="ce742-129">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="ce742-130">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ce742-130">*appsettings.json*.</span></span>
  * <span data-ttu-id="ce742-131">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="ce742-131">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="ce742-132">[Segreti dell'utente](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development` usando l'assembly di ingresso.</span><span class="sxs-lookup"><span data-stu-id="ce742-132">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="ce742-133">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="ce742-133">Environment variables.</span></span>
  * <span data-ttu-id="ce742-134">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="ce742-134">Command-line arguments.</span></span>
* <span data-ttu-id="ce742-135">Configura la [registrazione](xref:fundamentals/logging/index) per l'output della console e del debug.</span><span class="sxs-lookup"><span data-stu-id="ce742-135">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="ce742-136">La registrazione include le regole di [filtro dei log](xref:fundamentals/logging/index#log-filtering) specificate in una sezione di configurazione di registrazione di un oggetto *appsettings.json* o *appSettings. { Environment}. file JSON* .</span><span class="sxs-lookup"><span data-stu-id="ce742-136">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="ce742-137">Quando è in esecuzione dietro IIS con il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` Abilita l' [integrazione di IIS](xref:host-and-deploy/iis/index), che configura l'indirizzo di base e la porta dell'app.</span><span class="sxs-lookup"><span data-stu-id="ce742-137">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="ce742-138">L'integrazione di IIS configura inoltre l'app per l'[acquisizione degli errori di avvio](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="ce742-138">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="ce742-139">Per le opzioni predefinite di IIS, vedere <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="ce742-139">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="ce742-140">Imposta [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` se l'ambiente dell'app è lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="ce742-140">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="ce742-141">Per ulteriori informazioni, vedere [Convalida dell'ambito](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="ce742-141">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="ce742-142">La configurazione definita da `CreateDefaultBuilder` può essere sottoposta a override e aumentata tramite [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) e altri metodi e metodi di estensione di [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="ce742-142">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="ce742-143">Di seguito sono riportati alcuni esempi:</span><span class="sxs-lookup"><span data-stu-id="ce742-143">A few examples follow:</span></span>

* <span data-ttu-id="ce742-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) viene usato per specificare altri elementi `IConfiguration` per l'app.</span><span class="sxs-lookup"><span data-stu-id="ce742-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="ce742-145">La chiamata a `ConfigureAppConfiguration` seguente consente di aggiungere un delegato per includere la configurazione dell'app nel file *appsettings.xml*.</span><span class="sxs-lookup"><span data-stu-id="ce742-145">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="ce742-146">`ConfigureAppConfiguration` può essere chiamato più volte.</span><span class="sxs-lookup"><span data-stu-id="ce742-146">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="ce742-147">Si noti che questa configurazione non è valida per l'host (ad esempio, gli URL del server o l'ambiente).</span><span class="sxs-lookup"><span data-stu-id="ce742-147">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="ce742-148">Vedere la sezione [Valori di configurazione dell'host](#host-configuration-values).</span><span class="sxs-lookup"><span data-stu-id="ce742-148">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="ce742-149">La chiamata a `ConfigureLogging` seguente consente di aggiungere un delegato per configurare il livello di registrazione minimo ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="ce742-149">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="ce742-150">Questa impostazione sostituisce le impostazioni *appsettings.Development.json* ( `LogLevel.Debug` ) e *appsettings.Production.json* ( `LogLevel.Error` ) configurate da `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ce742-150">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ce742-151">`ConfigureLogging` può essere chiamato più volte.</span><span class="sxs-lookup"><span data-stu-id="ce742-151">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="ce742-152">La chiamata a `ConfigureKestrel` seguente sostituisce il valore predefinito [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) di 30.000.000 di byte stabilito durante la configurazione del Kestrel da `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ce742-152">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="ce742-153">La chiamata a [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) seguente sostituisce il valore predefinito [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) di 30.000.000 di byte stabilito durante la configurazione del Kestrel da `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ce742-153">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="ce742-154">La [radice del contenuto](xref:fundamentals/index#content-root) determina la posizione in cui l'host cerca i file dei contenuti, ad esempio i file di visualizzazione MVC.</span><span class="sxs-lookup"><span data-stu-id="ce742-154">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="ce742-155">Quando l'app viene avviata dalla cartella radice del progetto, la cartella radice del progetto viene usata come radice del contenuto.</span><span class="sxs-lookup"><span data-stu-id="ce742-155">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="ce742-156">Si tratta dell'impostazione predefinita usata in [Visual Studio](https://visualstudio.microsoft.com) e nei [nuovi modelli dotnet](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="ce742-156">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="ce742-157">Per altre informazioni sulla configurazione dell'app, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ce742-157">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="ce742-158">In alternativa all'uso del metodo `CreateDefaultBuilder` statico, un approccio supportato in ASP.NET Core 2.x consiste nel creare un host da [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="ce742-158">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="ce742-159">Quando si configura un host, è possibile specificare i metodi [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices).</span><span class="sxs-lookup"><span data-stu-id="ce742-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="ce742-160">Se viene specificata una classe `Startup`, la classe deve definire un metodo `Configure`.</span><span class="sxs-lookup"><span data-stu-id="ce742-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="ce742-161">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="ce742-161">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="ce742-162">Se vengono effettuate più chiamate a `ConfigureServices`, le chiamate vengono aggiunte l'una all'altra.</span><span class="sxs-lookup"><span data-stu-id="ce742-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="ce742-163">Più chiamate a `Configure` o `UseStartup` in `WebHostBuilder` sostituiscono le impostazioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="ce742-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="ce742-164">Valori di configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="ce742-164">Host configuration values</span></span>

<span data-ttu-id="ce742-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) si basa sugli approcci seguenti per impostare i valori di configurazione dell'host:</span><span class="sxs-lookup"><span data-stu-id="ce742-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="ce742-166">Configurazione del generatore di host, che include le variabili di ambiente nel formato `ASPNETCORE_{configurationKey}`.</span><span class="sxs-lookup"><span data-stu-id="ce742-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="ce742-167">Ad esempio, `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="ce742-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="ce742-168">Le estensioni come [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) e [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (vedere la sezione [Override della configurazione](#override-configuration)).</span><span class="sxs-lookup"><span data-stu-id="ce742-168">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="ce742-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) e chiave associata.</span><span class="sxs-lookup"><span data-stu-id="ce742-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="ce742-170">Quando si imposta un valore con `UseSetting`, il valore viene impostato come stringa indipendentemente dal tipo.</span><span class="sxs-lookup"><span data-stu-id="ce742-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="ce742-171">L'host usa l'ultima opzione che imposta un valore.</span><span class="sxs-lookup"><span data-stu-id="ce742-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="ce742-172">Per altre informazioni, vedere [Override della configurazione](#override-configuration) nella prossima sezione.</span><span class="sxs-lookup"><span data-stu-id="ce742-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="ce742-173">Chiave applicazione (nome)</span><span class="sxs-lookup"><span data-stu-id="ce742-173">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ce742-174">La `IWebHostEnvironment.ApplicationName` proprietà viene impostata automaticamente quando [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) o [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) viene chiamato durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="ce742-174">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="ce742-175">Il valore viene impostato sul nome dell'assembly contenente il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="ce742-175">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="ce742-176">Per impostare il valore in modo esplicito, usare [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="ce742-176">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ce742-177">La proprietà [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) viene impostata automaticamente quando si chiama [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) o [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="ce742-177">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="ce742-178">Il valore viene impostato sul nome dell'assembly contenente il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="ce742-178">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="ce742-179">Per impostare il valore in modo esplicito, usare [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="ce742-179">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="ce742-180">**Chiave**: applicationName</span><span class="sxs-lookup"><span data-stu-id="ce742-180">**Key**: applicationName</span></span>  
<span data-ttu-id="ce742-181">**Tipo**: *stringa*</span><span class="sxs-lookup"><span data-stu-id="ce742-181">**Type**: *string*</span></span>  
<span data-ttu-id="ce742-182">**Impostazione predefinita**: il nome dell'assembly contenente il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="ce742-182">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="ce742-183">**Imposta utilizzando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="ce742-183">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="ce742-184">**Variabile di ambiente**: `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="ce742-184">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="ce742-185">Acquisizione degli errori di avvio</span><span class="sxs-lookup"><span data-stu-id="ce742-185">Capture Startup Errors</span></span>

<span data-ttu-id="ce742-186">Questa impostazione controlla l'acquisizione degli errori di avvio.</span><span class="sxs-lookup"><span data-stu-id="ce742-186">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="ce742-187">**Chiave**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="ce742-187">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="ce742-188">**Tipo**: *bool* (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="ce742-188">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="ce742-189">**Impostazione predefinita**: il valore predefinito è `false` a meno che l'app non venga eseguita con Kestrel in IIS, in tal caso il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="ce742-189">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="ce742-190">**Imposta utilizzando**: `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="ce742-190">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="ce742-191">**Variabile di ambiente**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="ce742-191">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="ce742-192">Quando è `false`, gli errori durante l'avvio causano la chiusura dell'host.</span><span class="sxs-lookup"><span data-stu-id="ce742-192">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="ce742-193">Quando è `true`, l'host acquisisce le eccezioni durante l'avvio e tenta di avviare il server.</span><span class="sxs-lookup"><span data-stu-id="ce742-193">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="ce742-194">Radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="ce742-194">Content root</span></span>

<span data-ttu-id="ce742-195">Questa impostazione determina la posizione in cui ASP.NET Core inizia la ricerca dei file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="ce742-195">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="ce742-196">**Chiave**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="ce742-196">**Key**: contentRoot</span></span>  
<span data-ttu-id="ce742-197">**Tipo**: *stringa*</span><span class="sxs-lookup"><span data-stu-id="ce742-197">**Type**: *string*</span></span>  
<span data-ttu-id="ce742-198">**Impostazione predefinita**: il valore predefinito corrisponde alla cartella contenente l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="ce742-198">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="ce742-199">**Imposta utilizzando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="ce742-199">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="ce742-200">**Variabile di ambiente**: `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="ce742-200">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="ce742-201">La radice del contenuto viene usata anche come percorso di base per la [radice Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="ce742-201">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="ce742-202">Se il percorso radice del contenuto non esiste, l'host non viene avviato.</span><span class="sxs-lookup"><span data-stu-id="ce742-202">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="ce742-203">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="ce742-203">For more information, see:</span></span>

* [<span data-ttu-id="ce742-204">Nozioni fondamentali: radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="ce742-204">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="ce742-205">Radice Web</span><span class="sxs-lookup"><span data-stu-id="ce742-205">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="ce742-206">Errori dettagliati</span><span class="sxs-lookup"><span data-stu-id="ce742-206">Detailed Errors</span></span>

<span data-ttu-id="ce742-207">Determina l'acquisizione degli errori dettagliati.</span><span class="sxs-lookup"><span data-stu-id="ce742-207">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="ce742-208">**Chiave**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="ce742-208">**Key**: detailedErrors</span></span>  
<span data-ttu-id="ce742-209">**Tipo**: *bool* (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="ce742-209">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="ce742-210">**Valore predefinito**: false</span><span class="sxs-lookup"><span data-stu-id="ce742-210">**Default**: false</span></span>  
<span data-ttu-id="ce742-211">**Imposta utilizzando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="ce742-211">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="ce742-212">**Variabile di ambiente**: `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="ce742-212">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="ce742-213">Quando è abilitata (o quando l'<a href="#environment">ambiente</a> è impostato su `Development`), l'app acquisisce le eccezioni dettagliate.</span><span class="sxs-lookup"><span data-stu-id="ce742-213">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="ce742-214">Ambiente</span><span class="sxs-lookup"><span data-stu-id="ce742-214">Environment</span></span>

<span data-ttu-id="ce742-215">Imposta l'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="ce742-215">Sets the app's environment.</span></span>

<span data-ttu-id="ce742-216">**Chiave**: environment</span><span class="sxs-lookup"><span data-stu-id="ce742-216">**Key**: environment</span></span>  
<span data-ttu-id="ce742-217">**Tipo**: *stringa*</span><span class="sxs-lookup"><span data-stu-id="ce742-217">**Type**: *string*</span></span>  
<span data-ttu-id="ce742-218">**Impostazione predefinita**: Production</span><span class="sxs-lookup"><span data-stu-id="ce742-218">**Default**: Production</span></span>  
<span data-ttu-id="ce742-219">**Imposta utilizzando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="ce742-219">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="ce742-220">**Variabile di ambiente**: `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="ce742-220">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="ce742-221">L'ambiente può essere impostato su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="ce742-221">The environment can be set to any value.</span></span> <span data-ttu-id="ce742-222">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="ce742-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="ce742-223">Nei valori non viene fatta distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="ce742-223">Values aren't case sensitive.</span></span> <span data-ttu-id="ce742-224">Per impostazione predefinita, l'*ambiente* viene letto dalla variabile di ambiente `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="ce742-224">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="ce742-225">Quando si usa [Visual Studio](https://visualstudio.microsoft.com), le variabili di ambiente possono essere impostate nel file *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ce742-225">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="ce742-226">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="ce742-226">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="ce742-227">Assembly di avvio dell'hosting</span><span class="sxs-lookup"><span data-stu-id="ce742-227">Hosting Startup Assemblies</span></span>

<span data-ttu-id="ce742-228">Imposta gli assembly di avvio dell'hosting dell'app.</span><span class="sxs-lookup"><span data-stu-id="ce742-228">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="ce742-229">**Chiave**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="ce742-229">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="ce742-230">**Tipo**: *stringa*</span><span class="sxs-lookup"><span data-stu-id="ce742-230">**Type**: *string*</span></span>  
<span data-ttu-id="ce742-231">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="ce742-231">**Default**: Empty string</span></span>  
<span data-ttu-id="ce742-232">**Imposta utilizzando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="ce742-232">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="ce742-233">**Variabile di ambiente**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ce742-233">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="ce742-234">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da caricare all'avvio.</span><span class="sxs-lookup"><span data-stu-id="ce742-234">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="ce742-235">Sebbene il valore di configurazione predefinito sia una stringa vuota, gli assembly di avvio dell'hosting includono sempre l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="ce742-235">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="ce742-236">Quando vengono specificati, gli assembly di avvio dell'hosting vengono aggiunti all'assembly dell'app per essere caricati quando l'app compila i servizi comuni durante l'avvio.</span><span class="sxs-lookup"><span data-stu-id="ce742-236">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="ce742-237">Porta HTTPS</span><span class="sxs-lookup"><span data-stu-id="ce742-237">HTTPS Port</span></span>

<span data-ttu-id="ce742-238">Impostare la porta di reindirizzamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ce742-238">Set the HTTPS redirect port.</span></span> <span data-ttu-id="ce742-239">Usata per [imporre HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="ce742-239">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="ce742-240">**Chiave**: https_port **Tipo**: *string*
**Valore predefinito**: non è impostato un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="ce742-240">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="ce742-241">**Imposta utilizzando**: `UseSetting` 
 **variabile di ambiente**:`ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="ce742-241">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="ce742-242">Assembly di avvio dell'hosting da escludere</span><span class="sxs-lookup"><span data-stu-id="ce742-242">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="ce742-243">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da escludere all'avvio.</span><span class="sxs-lookup"><span data-stu-id="ce742-243">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="ce742-244">**Chiave**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="ce742-244">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="ce742-245">**Tipo**: *stringa*</span><span class="sxs-lookup"><span data-stu-id="ce742-245">**Type**: *string*</span></span>  
<span data-ttu-id="ce742-246">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="ce742-246">**Default**: Empty string</span></span>  
<span data-ttu-id="ce742-247">**Imposta utilizzando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="ce742-247">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="ce742-248">**Variabile di ambiente**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ce742-248">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="ce742-249">Preferire gli URL di hosting</span><span class="sxs-lookup"><span data-stu-id="ce742-249">Prefer Hosting URLs</span></span>

<span data-ttu-id="ce742-250">Indica se l'host deve eseguire l'ascolto sugli URL configurati con `WebHostBuilder` anziché su quelli configurati con l'implementazione `IServer`.</span><span class="sxs-lookup"><span data-stu-id="ce742-250">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="ce742-251">**Chiave**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="ce742-251">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="ce742-252">**Tipo**: *bool* (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="ce742-252">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="ce742-253">**Impostazione predefinita**: true</span><span class="sxs-lookup"><span data-stu-id="ce742-253">**Default**: true</span></span>  
<span data-ttu-id="ce742-254">**Imposta utilizzando**: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="ce742-254">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="ce742-255">**Variabile di ambiente**: `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="ce742-255">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="ce742-256">Impedire l'avvio dell'hosting</span><span class="sxs-lookup"><span data-stu-id="ce742-256">Prevent Hosting Startup</span></span>

<span data-ttu-id="ce742-257">Impedisce il caricamento automatico degli assembly di avvio dell'hosting, inclusi gli assembly di avvio dell'hosting configurati dall'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="ce742-257">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="ce742-258">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ce742-258">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="ce742-259">**Chiave**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="ce742-259">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="ce742-260">**Tipo**: *bool* (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="ce742-260">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="ce742-261">**Valore predefinito**: false</span><span class="sxs-lookup"><span data-stu-id="ce742-261">**Default**: false</span></span>  
<span data-ttu-id="ce742-262">**Imposta utilizzando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="ce742-262">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="ce742-263">**Variabile di ambiente**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="ce742-263">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="ce742-264">URL del server</span><span class="sxs-lookup"><span data-stu-id="ce742-264">Server URLs</span></span>

<span data-ttu-id="ce742-265">Indica gli indirizzi IP o gli indirizzi host con le porte e protocolli su cui il server deve eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="ce742-265">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="ce742-266">**Chiave**: urls</span><span class="sxs-lookup"><span data-stu-id="ce742-266">**Key**: urls</span></span>  
<span data-ttu-id="ce742-267">**Tipo**: *stringa*</span><span class="sxs-lookup"><span data-stu-id="ce742-267">**Type**: *string*</span></span>  
<span data-ttu-id="ce742-268">**Impostazione predefinita**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="ce742-268">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="ce742-269">**Imposta utilizzando**: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="ce742-269">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="ce742-270">**Variabile di ambiente**: `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="ce742-270">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="ce742-271">Impostare su un elenco di prefissi URL separati da punto e virgola (;) ai quali il server deve rispondere.</span><span class="sxs-lookup"><span data-stu-id="ce742-271">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="ce742-272">Ad esempio, `http://localhost:123`</span><span class="sxs-lookup"><span data-stu-id="ce742-272">For example, `http://localhost:123`.</span></span> <span data-ttu-id="ce742-273">Usare "\*" per indicare che il server deve eseguire l'ascolto per le richieste su tutti gli indirizzi IP o nomi host usando la porta e il protocollo specificati (ad esempio, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="ce742-273">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="ce742-274">Il protocollo (`http://` o `https://`) deve essere incluso con ogni URL.</span><span class="sxs-lookup"><span data-stu-id="ce742-274">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="ce742-275">I formati supportati variano a seconda del server.</span><span class="sxs-lookup"><span data-stu-id="ce742-275">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="ce742-276">Kestrel ha una propria API di configurazione degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="ce742-276">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="ce742-277">Per altre informazioni, vedere <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ce742-277">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="ce742-278">Timeout di arresto</span><span class="sxs-lookup"><span data-stu-id="ce742-278">Shutdown Timeout</span></span>

<span data-ttu-id="ce742-279">Specifica il tempo di attesa per l'arresto dell'host Web.</span><span class="sxs-lookup"><span data-stu-id="ce742-279">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="ce742-280">**Chiave**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="ce742-280">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="ce742-281">**Tipo**: *int*</span><span class="sxs-lookup"><span data-stu-id="ce742-281">**Type**: *int*</span></span>  
<span data-ttu-id="ce742-282">**Valore predefinito**: 5</span><span class="sxs-lookup"><span data-stu-id="ce742-282">**Default**: 5</span></span>  
<span data-ttu-id="ce742-283">**Imposta utilizzando**: `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="ce742-283">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="ce742-284">**Variabile di ambiente**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="ce742-284">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="ce742-285">Sebbene la chiave accetti *int* con `UseSetting` (ad esempio, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), il metodo di estensione [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) accetta [TimeSpan](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="ce742-285">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="ce742-286">Durante il periodo di timeout, l'hosting:</span><span class="sxs-lookup"><span data-stu-id="ce742-286">During the timeout period, hosting:</span></span>

* <span data-ttu-id="ce742-287">Attiva [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="ce742-287">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="ce742-288">Tenta di arrestare i servizi ospitati, registrando eventuali errori per i servizi che non si interrompono.</span><span class="sxs-lookup"><span data-stu-id="ce742-288">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="ce742-289">Se il periodo di timeout scade prima che siano stati arrestati tutti i servizi ospitati, gli eventuali servizi attivi rimanenti vengono interrotti quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="ce742-289">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="ce742-290">I servizi si arrestano anche se non hanno completato l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="ce742-290">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="ce742-291">Se l'arresto dei servizi richiede più tempo, aumentare il valore di timeout.</span><span class="sxs-lookup"><span data-stu-id="ce742-291">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="ce742-292">Assembly di avvio</span><span class="sxs-lookup"><span data-stu-id="ce742-292">Startup Assembly</span></span>

<span data-ttu-id="ce742-293">Determina l'assembly per la ricerca della classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="ce742-293">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="ce742-294">**Chiave**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="ce742-294">**Key**: startupAssembly</span></span>  
<span data-ttu-id="ce742-295">**Tipo**: *stringa*</span><span class="sxs-lookup"><span data-stu-id="ce742-295">**Type**: *string*</span></span>  
<span data-ttu-id="ce742-296">**Impostazione predefinita**: assembly dell'app</span><span class="sxs-lookup"><span data-stu-id="ce742-296">**Default**: The app's assembly</span></span>  
<span data-ttu-id="ce742-297">**Imposta utilizzando**: `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="ce742-297">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="ce742-298">**Variabile di ambiente**: `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="ce742-298">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="ce742-299">È possibile fare riferimento all'assembly per nome (`string`) o per tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="ce742-299">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="ce742-300">Se vengono chiamati più metodi `UseStartup`, l'ultimo metodo ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="ce742-300">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="ce742-301">Radice Web</span><span class="sxs-lookup"><span data-stu-id="ce742-301">Web root</span></span>

<span data-ttu-id="ce742-302">Imposta il percorso relativo degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="ce742-302">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="ce742-303">**Chiave**: webroot</span><span class="sxs-lookup"><span data-stu-id="ce742-303">**Key**: webroot</span></span>  
<span data-ttu-id="ce742-304">**Tipo**: *stringa*</span><span class="sxs-lookup"><span data-stu-id="ce742-304">**Type**: *string*</span></span>  
<span data-ttu-id="ce742-305">**Impostazione predefinita**: il valore predefinito è `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="ce742-305">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="ce742-306">Il percorso di *{radice del contenuto}/wwwroot* deve esistere.</span><span class="sxs-lookup"><span data-stu-id="ce742-306">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="ce742-307">Se il percorso non esiste, viene usato un provider di file no-op.</span><span class="sxs-lookup"><span data-stu-id="ce742-307">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="ce742-308">**Imposta utilizzando**: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="ce742-308">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="ce742-309">**Variabile di ambiente**: `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="ce742-309">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="ce742-310">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="ce742-310">For more information, see:</span></span>

* [<span data-ttu-id="ce742-311">Nozioni fondamentali: radice Web</span><span class="sxs-lookup"><span data-stu-id="ce742-311">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="ce742-312">Radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="ce742-312">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="ce742-313">Override della configurazione</span><span class="sxs-lookup"><span data-stu-id="ce742-313">Override configuration</span></span>

<span data-ttu-id="ce742-314">Usare la [Configurazione](xref:fundamentals/configuration/index) per configurare l'host Web.</span><span class="sxs-lookup"><span data-stu-id="ce742-314">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="ce742-315">Nell'esempio seguente la configurazione dell'host è specificata facoltativamente nel file *hostsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ce742-315">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="ce742-316">Qualsiasi configurazione caricata dal file *hostsettings.json* può essere sostituita dagli argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="ce742-316">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="ce742-317">La configurazione compilata (in `config`) viene usata per configurare l'host con [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="ce742-317">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="ce742-318">La configurazione di `IWebHostBuilder` viene aggiunta alla configurazione dell'app, ma non il contrario: &mdash;`ConfigureAppConfiguration` non influenza la configurazione di `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ce742-318">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="ce742-319">Esecuzione dell'override della configurazione specificata da `UseUrls` con prima la configurazione *hostsettings.json* e quindi con la configurazione dell'argomento della riga di comando:</span><span class="sxs-lookup"><span data-stu-id="ce742-319">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

<span data-ttu-id="ce742-320">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ce742-320">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="ce742-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) copia solo le chiavi dall'oggetto fornito `IConfiguration` alla configurazione del generatore host.</span><span class="sxs-lookup"><span data-stu-id="ce742-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="ce742-322">Pertanto, l'impostazione di `reloadOnChange: true` per i file JSON, INI e XML non ha alcun effetto.</span><span class="sxs-lookup"><span data-stu-id="ce742-322">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="ce742-323">Per specificare l'host eseguito in un URL specifico, il valore desiderato può essere passato da un prompt dei comandi durante l'esecuzione di [dotnet run](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="ce742-323">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="ce742-324">L'argomento della riga di comando esegue l'override del valore `urls` dal file *hostsettings.json*, mentre il server esegue l'ascolto sulla porta 8080:</span><span class="sxs-lookup"><span data-stu-id="ce742-324">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="ce742-325">Gestire l'host</span><span class="sxs-lookup"><span data-stu-id="ce742-325">Manage the host</span></span>

<span data-ttu-id="ce742-326">**Esegui**</span><span class="sxs-lookup"><span data-stu-id="ce742-326">**Run**</span></span>

<span data-ttu-id="ce742-327">Il metodo `Run` avvia l'app Web e blocca il thread di chiamata fino all'arresto dell'host:</span><span class="sxs-lookup"><span data-stu-id="ce742-327">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="ce742-328">**Inizia**</span><span class="sxs-lookup"><span data-stu-id="ce742-328">**Start**</span></span>

<span data-ttu-id="ce742-329">Eseguire l'host in modo non bloccante chiamandone il metodo `Start`:</span><span class="sxs-lookup"><span data-stu-id="ce742-329">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="ce742-330">Se viene passato un elenco di URL al metodo `Start`, viene eseguito l'ascolto sugli URL specificati:</span><span class="sxs-lookup"><span data-stu-id="ce742-330">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="ce742-331">L'app può inizializzare e avviare un nuovo host usando i valori predefiniti preconfigurati di `CreateDefaultBuilder` con un metodo pratico statico.</span><span class="sxs-lookup"><span data-stu-id="ce742-331">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="ce742-332">Questi metodi avviano il server senza l'output della console e con l'attesa [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) di un'interruzione (Ctrl-C/SIGINT o SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="ce742-332">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="ce742-333">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="ce742-333">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="ce742-334">Start con `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="ce742-334">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="ce742-335">Creare una richiesta nel browser a `http://localhost:5000` per ricevere la risposta "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="ce742-335">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="ce742-336">`WaitForShutdown` rimane bloccato fino a quando non viene eseguita un'interruzione (Ctrl-C/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="ce742-336">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="ce742-337">L'app visualizza il messaggio `Console.WriteLine` e attende la pressione di un tasto per chiudersi.</span><span class="sxs-lookup"><span data-stu-id="ce742-337">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="ce742-338">**Start(string url, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="ce742-338">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="ce742-339">Start con un URL e `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="ce742-339">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="ce742-340">Produce lo stesso risultato di **Start(app RequestDelegate)**, ad eccezione del fatto che l'app risponde su `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="ce742-340">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="ce742-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="ce742-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="ce742-342">Usare un'istanza di `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) per usare il middleware di routing:</span><span class="sxs-lookup"><span data-stu-id="ce742-342">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="ce742-343">Usare le richieste del browser seguenti con l'esempio:</span><span class="sxs-lookup"><span data-stu-id="ce742-343">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="ce742-344">Richiesta</span><span class="sxs-lookup"><span data-stu-id="ce742-344">Request</span></span>                                    | <span data-ttu-id="ce742-345">Risposta</span><span class="sxs-lookup"><span data-stu-id="ce742-345">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="ce742-346">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="ce742-346">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="ce742-347">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="ce742-347">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="ce742-348">Genera un'eccezione con la stringa "ooops!"</span><span class="sxs-lookup"><span data-stu-id="ce742-348">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="ce742-349">Genera un'eccezione con la stringa "Uh oh!"</span><span class="sxs-lookup"><span data-stu-id="ce742-349">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="ce742-350">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="ce742-350">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="ce742-351">Hello World!</span><span class="sxs-lookup"><span data-stu-id="ce742-351">Hello World!</span></span>                             |

<span data-ttu-id="ce742-352">`WaitForShutdown` rimane bloccato fino a quando non viene eseguita un'interruzione (Ctrl-C/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="ce742-352">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="ce742-353">L'app visualizza il messaggio `Console.WriteLine` e attende la pressione di un tasto per chiudersi.</span><span class="sxs-lookup"><span data-stu-id="ce742-353">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="ce742-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="ce742-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="ce742-355">Usare un URL e un'istanza di `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ce742-355">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="ce742-356">Produce lo stesso risultato di **Start(Action\<IRouteBuilder> routeBuilder)**, ad eccezione del fatto che l'app risponde in `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="ce742-356">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="ce742-357">**StartWith(Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="ce742-357">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="ce742-358">Specificare un delegato per configurare `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ce742-358">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="ce742-359">Creare una richiesta nel browser a `http://localhost:5000` per ricevere la risposta "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="ce742-359">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="ce742-360">`WaitForShutdown` rimane bloccato fino a quando non viene eseguita un'interruzione (Ctrl-C/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="ce742-360">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="ce742-361">L'app visualizza il messaggio `Console.WriteLine` e attende la pressione di un tasto per chiudersi.</span><span class="sxs-lookup"><span data-stu-id="ce742-361">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="ce742-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="ce742-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="ce742-363">Specificare un URL e un delegato per configurare `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ce742-363">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="ce742-364">Produce lo stesso risultato di **StartWith(Action\<IApplicationBuilder> app)**, ad eccezione del fatto che l'app risponde su `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="ce742-364">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="ce742-365">Interfaccia IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="ce742-365">IWebHostEnvironment interface</span></span>

<span data-ttu-id="ce742-366">L' `IWebHostEnvironment` interfaccia fornisce informazioni sull'ambiente di hosting Web dell'app.</span><span class="sxs-lookup"><span data-stu-id="ce742-366">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="ce742-367">Usare l'[inserimento di un costruttore](xref:fundamentals/dependency-injection) per ottenere `IWebHostEnvironment` per poterne usare le proprietà e i metodi di estensione:</span><span class="sxs-lookup"><span data-stu-id="ce742-367">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="ce742-368">È possibile usare un [approccio basato su convenzione](xref:fundamentals/environments#environment-based-startup-class-and-methods) per configurare l'app all'avvio in base all'ambiente.</span><span class="sxs-lookup"><span data-stu-id="ce742-368">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="ce742-369">In alternativa, inserire `IWebHostEnvironment` nel costruttore `Startup` per l'utilizzo in `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ce742-369">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="ce742-370">Oltre al metodo di estensione `IsDevelopment`, `IWebHostEnvironment` offre i metodi `IsStaging`, `IsProduction` e `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="ce742-370">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="ce742-371">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="ce742-371">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="ce742-372">Il servizio `IWebHostEnvironment` può anche essere inserito direttamente nel metodo `Configure` per configurare la pipeline di elaborazione:</span><span class="sxs-lookup"><span data-stu-id="ce742-372">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="ce742-373">`IWebHostEnvironment` può essere inserito nel metodo `Invoke` durante la creazione di [middleware](xref:fundamentals/middleware/write) personalizzato:</span><span class="sxs-lookup"><span data-stu-id="ce742-373">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="ce742-374">Interfaccia IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="ce742-374">IHostingEnvironment interface</span></span>

<span data-ttu-id="ce742-375">L'[interfaccia IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) fornisce informazioni sull'ambiente di hosting Web dell'app.</span><span class="sxs-lookup"><span data-stu-id="ce742-375">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="ce742-376">Usare l'[inserimento di un costruttore](xref:fundamentals/dependency-injection) per ottenere `IHostingEnvironment` per poterne usare le proprietà e i metodi di estensione:</span><span class="sxs-lookup"><span data-stu-id="ce742-376">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="ce742-377">È possibile usare un [approccio basato su convenzione](xref:fundamentals/environments#environment-based-startup-class-and-methods) per configurare l'app all'avvio in base all'ambiente.</span><span class="sxs-lookup"><span data-stu-id="ce742-377">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="ce742-378">In alternativa, inserire `IHostingEnvironment` nel costruttore `Startup` per l'utilizzo in `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ce742-378">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="ce742-379">Oltre al metodo di estensione `IsDevelopment`, `IHostingEnvironment` offre i metodi `IsStaging`, `IsProduction` e `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="ce742-379">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="ce742-380">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="ce742-380">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="ce742-381">Il servizio `IHostingEnvironment` può anche essere inserito direttamente nel metodo `Configure` per configurare la pipeline di elaborazione:</span><span class="sxs-lookup"><span data-stu-id="ce742-381">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="ce742-382">`IHostingEnvironment` può essere inserito nel metodo `Invoke` durante la creazione di [middleware](xref:fundamentals/middleware/write) personalizzato:</span><span class="sxs-lookup"><span data-stu-id="ce742-382">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="ce742-383">Interfaccia IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ce742-383">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="ce742-384">`IHostApplicationLifetime` consente attività post-avvio e di arresto.</span><span class="sxs-lookup"><span data-stu-id="ce742-384">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="ce742-385">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi `Action` che definiscono gli eventi di avvio e arresto.</span><span class="sxs-lookup"><span data-stu-id="ce742-385">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="ce742-386">Token di annullamento</span><span class="sxs-lookup"><span data-stu-id="ce742-386">Cancellation Token</span></span>    | <span data-ttu-id="ce742-387">Attivato quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="ce742-387">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="ce742-388">L'host è stato completamente avviato.</span><span class="sxs-lookup"><span data-stu-id="ce742-388">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="ce742-389">L'host sta completando un arresto normale.</span><span class="sxs-lookup"><span data-stu-id="ce742-389">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="ce742-390">Tutte le richieste devono essere elaborate.</span><span class="sxs-lookup"><span data-stu-id="ce742-390">All requests should be processed.</span></span> <span data-ttu-id="ce742-391">L'arresto è bloccato fino al completamento di questo evento.</span><span class="sxs-lookup"><span data-stu-id="ce742-391">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="ce742-392">L'host sta eseguendo un arresto normale.</span><span class="sxs-lookup"><span data-stu-id="ce742-392">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="ce742-393">È possibile che le richieste siano ancora in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="ce742-393">Requests may still be processing.</span></span> <span data-ttu-id="ce742-394">L'arresto è bloccato fino al completamento di questo evento.</span><span class="sxs-lookup"><span data-stu-id="ce742-394">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="ce742-395">`StopApplication` richiede la chiusura dell'applicazione corrente.</span><span class="sxs-lookup"><span data-stu-id="ce742-395">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="ce742-396">La classe seguente usa `StopApplication` per arrestare normalmente un'app quando viene chiamato il metodo `Shutdown` della classe:</span><span class="sxs-lookup"><span data-stu-id="ce742-396">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="ce742-397">Interfaccia IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ce742-397">IApplicationLifetime interface</span></span>

<span data-ttu-id="ce742-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) consente attività post-avvio e di arresto.</span><span class="sxs-lookup"><span data-stu-id="ce742-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="ce742-399">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi `Action` che definiscono gli eventi di avvio e arresto.</span><span class="sxs-lookup"><span data-stu-id="ce742-399">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="ce742-400">Token di annullamento</span><span class="sxs-lookup"><span data-stu-id="ce742-400">Cancellation Token</span></span>    | <span data-ttu-id="ce742-401">Attivato quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="ce742-401">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="ce742-402">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="ce742-402">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="ce742-403">L'host è stato completamente avviato.</span><span class="sxs-lookup"><span data-stu-id="ce742-403">The host has fully started.</span></span> |
| [<span data-ttu-id="ce742-404">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="ce742-404">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="ce742-405">L'host sta completando un arresto normale.</span><span class="sxs-lookup"><span data-stu-id="ce742-405">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="ce742-406">Tutte le richieste devono essere elaborate.</span><span class="sxs-lookup"><span data-stu-id="ce742-406">All requests should be processed.</span></span> <span data-ttu-id="ce742-407">L'arresto è bloccato fino al completamento di questo evento.</span><span class="sxs-lookup"><span data-stu-id="ce742-407">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="ce742-408">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="ce742-408">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="ce742-409">L'host sta eseguendo un arresto normale.</span><span class="sxs-lookup"><span data-stu-id="ce742-409">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="ce742-410">È possibile che le richieste siano ancora in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="ce742-410">Requests may still be processing.</span></span> <span data-ttu-id="ce742-411">L'arresto è bloccato fino al completamento di questo evento.</span><span class="sxs-lookup"><span data-stu-id="ce742-411">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="ce742-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) richiede la chiusura dell'applicazione corrente.</span><span class="sxs-lookup"><span data-stu-id="ce742-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="ce742-413">La classe seguente usa `StopApplication` per arrestare normalmente un'app quando viene chiamato il metodo `Shutdown` della classe:</span><span class="sxs-lookup"><span data-stu-id="ce742-413">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="scope-validation"></a><span data-ttu-id="ce742-414">Convalida dell'ambito</span><span class="sxs-lookup"><span data-stu-id="ce742-414">Scope validation</span></span>

<span data-ttu-id="ce742-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) imposta [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) su `true` se l'ambiente dell'app è lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="ce742-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="ce742-416">Quando `ValidateScopes` è impostato su `true`, il provider di servizi predefinito esegue dei controlli per verificare che:</span><span class="sxs-lookup"><span data-stu-id="ce742-416">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="ce742-417">I servizi con ambito non vengano risolti direttamente o indirettamente dal provider di servizi radice.</span><span class="sxs-lookup"><span data-stu-id="ce742-417">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="ce742-418">I servizi con ambito non vengano inseriti direttamente o indirettamente in singleton.</span><span class="sxs-lookup"><span data-stu-id="ce742-418">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="ce742-419">Il provider di servizi radice venga creato con la chiamata di [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider).</span><span class="sxs-lookup"><span data-stu-id="ce742-419">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="ce742-420">La durata del provider di servizi radice corrisponde alla durata dell'app/server quando il provider viene avviato con l'app e viene eliminato alla chiusura dell'app.</span><span class="sxs-lookup"><span data-stu-id="ce742-420">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="ce742-421">I servizi con ambito vengono eliminati dal contenitore che li ha creati.</span><span class="sxs-lookup"><span data-stu-id="ce742-421">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="ce742-422">Se un servizio con ambito viene creato nel contenitore radice, la durata del servizio viene di fatto convertita in singleton, perché il servizio viene eliminato solo dal contenitore radice alla chiusura dell'app/server.</span><span class="sxs-lookup"><span data-stu-id="ce742-422">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="ce742-423">La convalida degli ambiti servizio rileva queste situazioni nel contesto della chiamata di `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="ce742-423">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="ce742-424">Per convalidare sempre gli ambiti, inclusi quelli dell'ambiente di produzione, configurare [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) con [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) nel generatore di host:</span><span class="sxs-lookup"><span data-stu-id="ce742-424">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="ce742-425">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="ce742-425">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
