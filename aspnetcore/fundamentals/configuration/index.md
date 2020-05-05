---
title: Configurazione in ASP.NET Core
author: rick-anderson
description: Informazioni su come usare l'API di configurazione per configurare un'app ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/index
ms.openlocfilehash: c2a7ef9c1523bc179524f328905f3a4b1460a1a5
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774496"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="3086e-103">Configurazione in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3086e-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="3086e-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="3086e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3086e-105">La configurazione in ASP.NET Core viene eseguita utilizzando uno o più [provider di configurazione](#cp).</span><span class="sxs-lookup"><span data-stu-id="3086e-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="3086e-106">I provider di configurazione leggono i dati di configurazione da coppie chiave-valore usando un'ampia gamma di origini di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="3086e-107">File di impostazioni, ad esempio *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="3086e-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="3086e-108">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="3086e-108">Environment variables</span></span>
* <span data-ttu-id="3086e-109">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="3086e-109">Azure Key Vault</span></span>
* <span data-ttu-id="3086e-110">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="3086e-110">Azure App Configuration</span></span>
* <span data-ttu-id="3086e-111">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="3086e-111">Command-line arguments</span></span>
* <span data-ttu-id="3086e-112">Provider personalizzati, installati o creati</span><span class="sxs-lookup"><span data-stu-id="3086e-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="3086e-113">File della directory</span><span class="sxs-lookup"><span data-stu-id="3086e-113">Directory files</span></span>
* <span data-ttu-id="3086e-114">Oggetti .NET in memoria</span><span class="sxs-lookup"><span data-stu-id="3086e-114">In-memory .NET objects</span></span>

<span data-ttu-id="3086e-115">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3086e-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="3086e-116">Configurazione predefinita</span><span class="sxs-lookup"><span data-stu-id="3086e-116">Default configuration</span></span>

<span data-ttu-id="3086e-117">ASP.NET Core app Web create con [DotNet New](/dotnet/core/tools/dotnet-new) o Visual Studio generano il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3086e-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="3086e-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fornisce la configurazione predefinita per l'app nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="3086e-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="3086e-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : aggiunge un oggetto `IConfiguration` esistente come origine.</span><span class="sxs-lookup"><span data-stu-id="3086e-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="3086e-120">Nel caso di configurazione predefinita, aggiunge la configurazione [host](#hvac) e la imposta come prima origine per la configurazione dell' _app_ .</span><span class="sxs-lookup"><span data-stu-id="3086e-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="3086e-121">[appSettings. JSON](#appsettingsjson) con il [provider di configurazione JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="3086e-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="3086e-122">*appSettings.* `Environment` *. JSON* con il [provider di configurazione JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="3086e-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="3086e-123">Ad esempio, *appSettings*. ***Produzione***. *JSON* e *appSettings*. ***Sviluppo***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="3086e-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="3086e-124">[Segreti dell'app](xref:security/app-secrets) quando l'app viene eseguita `Development` nell'ambiente.</span><span class="sxs-lookup"><span data-stu-id="3086e-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="3086e-125">Variabili di ambiente che usano il [provider di configurazione delle variabili di ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="3086e-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="3086e-126">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line).</span><span class="sxs-lookup"><span data-stu-id="3086e-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="3086e-127">I provider di configurazione aggiunti successivamente sostituiscono le impostazioni di chiave precedenti.</span><span class="sxs-lookup"><span data-stu-id="3086e-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="3086e-128">Se `MyKey` , ad esempio, è impostato sia in *appSettings. JSON* che nell'ambiente, viene utilizzato il valore dell'ambiente.</span><span class="sxs-lookup"><span data-stu-id="3086e-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="3086e-129">Utilizzando i provider di configurazione predefiniti, il [provider di configurazione della riga di comando](#command-line-configuration-provider) esegue l'override di tutti gli altri provider.</span><span class="sxs-lookup"><span data-stu-id="3086e-129">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="3086e-130">Per ulteriori informazioni su `CreateDefaultBuilder`, vedere [impostazioni predefinite del generatore](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="3086e-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="3086e-131">Il codice seguente Visualizza i provider di configurazione abilitati nell'ordine in cui sono stati aggiunti:</span><span class="sxs-lookup"><span data-stu-id="3086e-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="3086e-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="3086e-132">appsettings.json</span></span>

<span data-ttu-id="3086e-133">Si consideri il file *appSettings. JSON* seguente:</span><span class="sxs-lookup"><span data-stu-id="3086e-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="3086e-134">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="3086e-135">Il valore <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> predefinito carica la configurazione nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="3086e-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="3086e-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="3086e-136">*appsettings.json*</span></span>
1. <span data-ttu-id="3086e-137">*appSettings.* `Environment` *. JSON* : ad esempio, *appSettings*. ***Produzione***. *JSON* e *appSettings*. ***Sviluppo***. file *JSON* .</span><span class="sxs-lookup"><span data-stu-id="3086e-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="3086e-138">La versione dell'ambiente del file viene caricata in base a [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="3086e-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="3086e-139">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="3086e-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="3086e-140">*appSettings*. `Environment`. i valori *JSON* eseguono l'override delle chiavi in *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="3086e-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="3086e-141">Ad esempio, per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="3086e-141">For example, by default:</span></span>

* <span data-ttu-id="3086e-142">In fase di sviluppo, *appSettings*. ***Sviluppo***. la configurazione *JSON* sovrascrive i valori trovati in *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="3086e-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="3086e-143">In produzione, *appSettings*. ***Produzione***. la configurazione *JSON* sovrascrive i valori trovati in *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="3086e-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="3086e-144">Ad esempio, quando si distribuisce l'app in Azure.</span><span class="sxs-lookup"><span data-stu-id="3086e-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="3086e-145">Associare i dati di configurazione gerarchici usando il modello di opzioni</span><span class="sxs-lookup"><span data-stu-id="3086e-145">Bind hierarchical configuration data using the options pattern</span></span>

<span data-ttu-id="3086e-146">Il modo migliore per leggere i valori di configurazione correlati consiste nell'usare il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="3086e-146">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="3086e-147">Ad esempio, per leggere i valori di configurazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-147">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="3086e-148">Creare la classe `PositionOptions` seguente:</span><span class="sxs-lookup"><span data-stu-id="3086e-148">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="3086e-149">Tutte le proprietà di lettura/scrittura pubbliche del tipo sono associate.</span><span class="sxs-lookup"><span data-stu-id="3086e-149">All the public read-write properties of the type are bound.</span></span> <span data-ttu-id="3086e-150">I campi ***non*** sono associati.</span><span class="sxs-lookup"><span data-stu-id="3086e-150">Fields are ***not*** bound.</span></span>

<span data-ttu-id="3086e-151">Il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3086e-151">The following code:</span></span>

* <span data-ttu-id="3086e-152">Chiama [ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) per associare la `PositionOptions` classe alla `Position` sezione.</span><span class="sxs-lookup"><span data-stu-id="3086e-152">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="3086e-153">Consente di `Position` visualizzare i dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-153">Displays the `Position` configuration data.</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="3086e-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)associa e restituisce il tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="3086e-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="3086e-155">`ConfigurationBinder.Get<T>`può essere più pratico rispetto all' `ConfigurationBinder.Bind`uso di.</span><span class="sxs-lookup"><span data-stu-id="3086e-155">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="3086e-156">Il codice seguente illustra come usare `ConfigurationBinder.Get<T>` con la `PositionOptions` classe:</span><span class="sxs-lookup"><span data-stu-id="3086e-156">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="3086e-157">Un approccio alternativo quando si usa il ***modello di opzioni*** è associare `Position` la sezione e aggiungerla al [contenitore del servizio di inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3086e-157">An alternative approach when using the ***options pattern*** is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3086e-158">Nel codice seguente, `PositionOptions` viene aggiunto al contenitore del servizio con <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associato alla configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-158">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="3086e-159">Utilizzando il codice precedente, il codice seguente legge le opzioni di posizione:</span><span class="sxs-lookup"><span data-stu-id="3086e-159">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="3086e-160">Utilizzando la configurazione [predefinita](#default) , *appSettings. JSON* e *appSettings.* `Environment`i file con *estensione JSON* sono abilitati con [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="3086e-160">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="3086e-161">Modifiche apportate a *appSettings. JSON* e *appSettings.* `Environment`il file con *estensione JSON* ***dopo*** l'avvio dell'app viene letto dal [provider di configurazione JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="3086e-161">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="3086e-162">Per informazioni sull'aggiunta di altri file di configurazione JSON, vedere [provider di configurazione JSON](#jcp) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="3086e-162">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="3086e-163">Gestione della sicurezza e del segreto</span><span class="sxs-lookup"><span data-stu-id="3086e-163">Security and secret manager</span></span>

<span data-ttu-id="3086e-164">Linee guida sui dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-164">Configuration data guidelines:</span></span>

* <span data-ttu-id="3086e-165">Non archiviare mai la password o altri dati sensibili nel codice del provider di configurazione o in file di configurazione di testo normale.</span><span class="sxs-lookup"><span data-stu-id="3086e-165">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="3086e-166">Il [gestore del segreto](xref:security/app-secrets) può essere usato per archiviare i segreti in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="3086e-166">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="3086e-167">Non usare i segreti di produzione in ambienti di sviluppo o di test.</span><span class="sxs-lookup"><span data-stu-id="3086e-167">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="3086e-168">Specificare i segreti all'esterno del progetto in modo che non possano essere inavvertitamente inviati a un repository del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="3086e-168">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="3086e-169">Per [impostazione predefinita](#default), [Secret Manager](xref:security/app-secrets) legge le impostazioni di configurazione dopo *appSettings. JSON* e *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="3086e-169">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="3086e-170">Per ulteriori informazioni sull'archiviazione di password o altri dati sensibili:</span><span class="sxs-lookup"><span data-stu-id="3086e-170">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="3086e-171"><xref:security/app-secrets>: Include consigli sull'uso delle variabili di ambiente per archiviare dati riservati.</span><span class="sxs-lookup"><span data-stu-id="3086e-171"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="3086e-172">Il gestore dei segreti USA il [provider di configurazione file](#fcp) per archiviare i segreti utente in un file JSON nel sistema locale.</span><span class="sxs-lookup"><span data-stu-id="3086e-172">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="3086e-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) archivia in modo sicuro i segreti delle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3086e-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="3086e-174">Per altre informazioni, vedere <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3086e-174">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="3086e-175">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="3086e-175">Environment variables</span></span>

<span data-ttu-id="3086e-176">Usando la configurazione [predefinita](#default) , carica <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> la configurazione dalle coppie chiave-valore della variabile di ambiente dopo aver letto *appSettings. JSON*, *appSettings.* `Environment` *. JSON*e [gestione segreta](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="3086e-176">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="3086e-177">Pertanto, i valori di chiave letti dall'ambiente eseguono l'override dei valori letti da *appSettings. JSON*, *appSettings.* `Environment` *. JSON*e gestione segreta.</span><span class="sxs-lookup"><span data-stu-id="3086e-177">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="3086e-178">I comandi `set` seguenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-178">The following `set` commands:</span></span>

* <span data-ttu-id="3086e-179">Impostare le chiavi e i valori di ambiente dell' [esempio precedente](#appsettingsjson) in Windows.</span><span class="sxs-lookup"><span data-stu-id="3086e-179">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="3086e-180">Testare le impostazioni quando si usa il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="3086e-180">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="3086e-181">Il `dotnet run` comando deve essere eseguito nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="3086e-181">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="3086e-182">Le impostazioni di ambiente precedenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-182">The preceding environment settings:</span></span>

* <span data-ttu-id="3086e-183">Vengono impostati solo nei processi avviati dalla finestra di comando in cui sono stati impostati.</span><span class="sxs-lookup"><span data-stu-id="3086e-183">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="3086e-184">Non verrà letto dai browser avviati con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3086e-184">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="3086e-185">Per impostare le chiavi e i valori di ambiente in Windows, è possibile usare i comandi [Setx](/windows-server/administration/windows-commands/setx) seguenti.</span><span class="sxs-lookup"><span data-stu-id="3086e-185">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="3086e-186">Diversamente da `set`, `setx` le impostazioni sono rese permanente.</span><span class="sxs-lookup"><span data-stu-id="3086e-186">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="3086e-187">`/M`imposta la variabile nell'ambiente di sistema.</span><span class="sxs-lookup"><span data-stu-id="3086e-187">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="3086e-188">Se l' `/M` opzione non viene usata, viene impostata una variabile di ambiente utente.</span><span class="sxs-lookup"><span data-stu-id="3086e-188">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="3086e-189">Per verificare che i comandi precedenti eseguano l'override di *appSettings. JSON* e *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3086e-189">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="3086e-190">Con Visual Studio: chiudere e riavviare Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3086e-190">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="3086e-191">Con l'interfaccia della riga di comando: avviare una nuova `dotnet run`finestra di comando e immettere.</span><span class="sxs-lookup"><span data-stu-id="3086e-191">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="3086e-192">Chiamare <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> con una stringa per specificare un prefisso per le variabili di ambiente:</span><span class="sxs-lookup"><span data-stu-id="3086e-192">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="3086e-193">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="3086e-193">In the preceding code:</span></span>

* <span data-ttu-id="3086e-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`viene aggiunto dopo i [provider di configurazione predefiniti](#default).</span><span class="sxs-lookup"><span data-stu-id="3086e-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="3086e-195">Per un esempio di ordinamento dei provider di configurazione, vedere [provider di configurazione JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="3086e-195">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="3086e-196">Le variabili di ambiente impostate `MyCustomPrefix_` con il prefisso sostituiscono i [provider di configurazione predefiniti](#default).</span><span class="sxs-lookup"><span data-stu-id="3086e-196">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="3086e-197">Sono incluse le variabili di ambiente senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="3086e-197">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="3086e-198">Il prefisso viene rimosso quando vengono lette le coppie chiave-valore di configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-198">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="3086e-199">I comandi seguenti verificano il prefisso personalizzato:</span><span class="sxs-lookup"><span data-stu-id="3086e-199">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="3086e-200">La [configurazione predefinita](#default) carica le variabili di ambiente e gli argomenti della riga `DOTNET_` di `ASPNETCORE_`comando preceduti da e.</span><span class="sxs-lookup"><span data-stu-id="3086e-200">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="3086e-201">I `DOTNET_` prefissi e `ASPNETCORE_` vengono usati da ASP.NET Core per la [configurazione dell'host e dell'app](xref:fundamentals/host/generic-host#host-configuration), ma non per la configurazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="3086e-201">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="3086e-202">Per ulteriori informazioni sulla configurazione dell'host e dell'app, vedere [.NET Generic Host](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="3086e-202">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="3086e-203">In [app Azure servizio](https://azure.microsoft.com/services/app-service/)selezionare **impostazione nuova applicazione** nella pagina **Impostazioni > configurazione** .</span><span class="sxs-lookup"><span data-stu-id="3086e-203">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="3086e-204">App Azure impostazioni dell'applicazione del servizio sono:</span><span class="sxs-lookup"><span data-stu-id="3086e-204">Azure App Service application settings are:</span></span>

* <span data-ttu-id="3086e-205">Crittografati a riposo e trasmessi su un canale crittografato.</span><span class="sxs-lookup"><span data-stu-id="3086e-205">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="3086e-206">Esposto come variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="3086e-206">Exposed as environment variables.</span></span>

<span data-ttu-id="3086e-207">Per altre informazioni, vedere [App di Azure: Eseguire l'override della configurazione delle app usando il portale di Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="3086e-207">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="3086e-208">Per informazioni sulle stringhe di connessione del database di Azure, vedere [prefissi della stringa di connessione](#constr) .</span><span class="sxs-lookup"><span data-stu-id="3086e-208">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="3086e-209">Riga di comando</span><span class="sxs-lookup"><span data-stu-id="3086e-209">Command-line</span></span>

<span data-ttu-id="3086e-210">Utilizzando la configurazione [predefinita](#default) , carica <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> la configurazione dalle coppie chiave-valore dell'argomento della riga di comando dopo le origini di configurazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-210">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="3086e-211">*appSettings. JSON* e *appSettings*. `Environment`. file *JSON* .</span><span class="sxs-lookup"><span data-stu-id="3086e-211">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="3086e-212">[Segreti dell'app (gestione segreto)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="3086e-212">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="3086e-213">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="3086e-213">Environment variables.</span></span>

<span data-ttu-id="3086e-214">Per [impostazione predefinita](#default), i valori di configurazione impostati nei valori di configurazione di override della riga di comando impostati con tutti gli altri provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-214">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="3086e-215">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="3086e-215">Command-line arguments</span></span>

<span data-ttu-id="3086e-216">Il comando seguente imposta le chiavi e i `=`valori usando:</span><span class="sxs-lookup"><span data-stu-id="3086e-216">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="3086e-217">Il comando seguente imposta le chiavi e i `/`valori usando:</span><span class="sxs-lookup"><span data-stu-id="3086e-217">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="3086e-218">Il comando seguente imposta le chiavi e i `--`valori usando:</span><span class="sxs-lookup"><span data-stu-id="3086e-218">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="3086e-219">Valore chiave:</span><span class="sxs-lookup"><span data-stu-id="3086e-219">The key value:</span></span>

* <span data-ttu-id="3086e-220">Deve seguire `=`oppure la chiave deve avere un prefisso `--` o `/` quando il valore segue uno spazio.</span><span class="sxs-lookup"><span data-stu-id="3086e-220">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="3086e-221">Non è obbligatorio `=` se si usa.</span><span class="sxs-lookup"><span data-stu-id="3086e-221">Isn't required if `=` is used.</span></span> <span data-ttu-id="3086e-222">Ad esempio: `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="3086e-222">For example, `MySetting=`.</span></span>

<span data-ttu-id="3086e-223">All'interno dello stesso comando, non combinare coppie chiave-valore dell'argomento della riga di `=` comando che usano con coppie chiave-valore che usano uno spazio.</span><span class="sxs-lookup"><span data-stu-id="3086e-223">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="3086e-224">Mapping di sostituzione</span><span class="sxs-lookup"><span data-stu-id="3086e-224">Switch mappings</span></span>

<span data-ttu-id="3086e-225">I mapping switch consentono la logica di sostituzione del nome **chiave** .</span><span class="sxs-lookup"><span data-stu-id="3086e-225">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="3086e-226">Fornire un dizionario di sostituzioni switch al <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodo.</span><span class="sxs-lookup"><span data-stu-id="3086e-226">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="3086e-227">Quando viene utilizzato il dizionario dei mapping di sostituzione, nel dizionario viene controllata la presenza di una chiave corrispondente alla chiave fornita da un argomento della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="3086e-227">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="3086e-228">Se la chiave della riga di comando viene trovata nel dizionario, il valore del dizionario viene passato di nuovo per impostare la coppia chiave-valore nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="3086e-228">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="3086e-229">Un mapping di sostituzione è necessario per le chiavi della riga di comando con un trattino singolo (`-`) come prefisso.</span><span class="sxs-lookup"><span data-stu-id="3086e-229">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="3086e-230">Regole principali del dizionario dei mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="3086e-230">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="3086e-231">Le opzioni devono iniziare `-` con `--`o.</span><span class="sxs-lookup"><span data-stu-id="3086e-231">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="3086e-232">Il dizionario dei mapping di sostituzione non deve contenere chiavi duplicate.</span><span class="sxs-lookup"><span data-stu-id="3086e-232">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="3086e-233">Per usare un dizionario dei mapping switch, passarlo alla chiamata a `AddCommandLine`:</span><span class="sxs-lookup"><span data-stu-id="3086e-233">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="3086e-234">Il codice seguente mostra i valori chiave per le chiavi sostituite:</span><span class="sxs-lookup"><span data-stu-id="3086e-234">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="3086e-235">Eseguire il comando seguente per testare la sostituzione della chiave:</span><span class="sxs-lookup"><span data-stu-id="3086e-235">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="3086e-236">Nota: attualmente `=` non è possibile usare per impostare i valori di sostituzione delle chiavi con un `-`trattino singolo.</span><span class="sxs-lookup"><span data-stu-id="3086e-236">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="3086e-237">Vedere [il problema in GitHub](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="3086e-237">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="3086e-238">Il comando seguente funziona per testare la sostituzione della chiave:</span><span class="sxs-lookup"><span data-stu-id="3086e-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="3086e-239">Per le app che usano i mapping di sostituzione, la chiamata a `CreateDefaultBuilder` non deve passare argomenti.</span><span class="sxs-lookup"><span data-stu-id="3086e-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="3086e-240">La `CreateDefaultBuilder` `AddCommandLine` chiamata al metodo non include opzioni mappate e non è possibile passare il dizionario di mapping switch a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3086e-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="3086e-241">La soluzione non passa gli argomenti a `CreateDefaultBuilder` ma per consentire al `ConfigurationBuilder` `AddCommandLine` metodo del metodo di elaborare sia gli argomenti sia il dizionario di mapping delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="3086e-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="3086e-242">Dati di configurazione gerarchici</span><span class="sxs-lookup"><span data-stu-id="3086e-242">Hierarchical configuration data</span></span>

<span data-ttu-id="3086e-243">L'API di configurazione legge i dati di configurazione gerarchici rendendo flat i dati gerarchici con l'uso di un delimitatore nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="3086e-244">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il file *appSettings. JSON* seguente:</span><span class="sxs-lookup"><span data-stu-id="3086e-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="3086e-245">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="3086e-246">Il modo migliore per leggere i dati di configurazione gerarchici consiste nell'usare il modello di opzioni.</span><span class="sxs-lookup"><span data-stu-id="3086e-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="3086e-247">Per altre informazioni, vedere [associare dati di configurazione gerarchici](#optpat) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="3086e-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="3086e-248">I metodi <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sono disponibili per isolare le sezioni e gli elementi figlio di una sezione nei dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="3086e-249">Questi metodi sono descritti più avanti in [GetSection, GetChildren ed Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="3086e-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="3086e-250">Chiavi e valori di configurazione</span><span class="sxs-lookup"><span data-stu-id="3086e-250">Configuration keys and values</span></span>

<span data-ttu-id="3086e-251">Chiavi di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-251">Configuration keys:</span></span>

* <span data-ttu-id="3086e-252">Non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="3086e-252">Are case-insensitive.</span></span> <span data-ttu-id="3086e-253">Ad esempio, `ConnectionString` e `connectionstring` vengono considerate chiavi equivalenti.</span><span class="sxs-lookup"><span data-stu-id="3086e-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="3086e-254">Se una chiave e un valore vengono impostati in più provider di configurazione, viene utilizzato il valore dell'ultimo provider aggiunto.</span><span class="sxs-lookup"><span data-stu-id="3086e-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="3086e-255">Per ulteriori informazioni, vedere [configurazione predefinita](#default).</span><span class="sxs-lookup"><span data-stu-id="3086e-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="3086e-256">Chiavi gerarchiche</span><span class="sxs-lookup"><span data-stu-id="3086e-256">Hierarchical keys</span></span>
  * <span data-ttu-id="3086e-257">Nell'ambito dell'API di configurazione, il separatore due punti (`:`) funziona in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="3086e-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="3086e-258">Nelle variabili di ambiente, un separatore due punti potrebbe non funzionare in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="3086e-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="3086e-259">Un doppio carattere di sottolineatura, `__`, è supportato da tutte le piattaforme e viene convertito `:`automaticamente in due punti.</span><span class="sxs-lookup"><span data-stu-id="3086e-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="3086e-260">In Azure Key Vault, le chiavi gerarchiche `--` utilizzano come separatore.</span><span class="sxs-lookup"><span data-stu-id="3086e-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="3086e-261">Il [provider di configurazione Azure Key Vault](xref:security/key-vault-configuration) sostituisce `--` automaticamente con `:` un quando i segreti vengono caricati nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="3086e-261">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="3086e-262">Il <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supporta l'associazione di matrici agli oggetti usando gli indici delle matrici nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="3086e-263">L'associazione di matrici è descritta nella sezione [Associare una matrice a una classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="3086e-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="3086e-264">Valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-264">Configuration values:</span></span>

* <span data-ttu-id="3086e-265">Sono stringhe.</span><span class="sxs-lookup"><span data-stu-id="3086e-265">Are strings.</span></span>
* <span data-ttu-id="3086e-266">I valori null non possono essere archiviati nella configurazione o associati a oggetti.</span><span class="sxs-lookup"><span data-stu-id="3086e-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="3086e-267">Provider di configurazione</span><span class="sxs-lookup"><span data-stu-id="3086e-267">Configuration providers</span></span>

<span data-ttu-id="3086e-268">La tabella seguente mostra i provider di configurazione disponibili per le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3086e-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="3086e-269">Provider</span><span class="sxs-lookup"><span data-stu-id="3086e-269">Provider</span></span> | <span data-ttu-id="3086e-270">Fornisce la configurazione da</span><span class="sxs-lookup"><span data-stu-id="3086e-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="3086e-271">Provider di configurazione Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="3086e-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="3086e-272">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="3086e-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="3086e-273">Provider di configurazione app Azure</span><span class="sxs-lookup"><span data-stu-id="3086e-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="3086e-274">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="3086e-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="3086e-275">Provider di configurazione della riga di comando</span><span class="sxs-lookup"><span data-stu-id="3086e-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="3086e-276">Parametri della riga di comando</span><span class="sxs-lookup"><span data-stu-id="3086e-276">Command-line parameters</span></span> |
| [<span data-ttu-id="3086e-277">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="3086e-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="3086e-278">Origine personalizzata</span><span class="sxs-lookup"><span data-stu-id="3086e-278">Custom source</span></span> |
| [<span data-ttu-id="3086e-279">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="3086e-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="3086e-280">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="3086e-280">Environment variables</span></span> |
| [<span data-ttu-id="3086e-281">Provider di configurazione file</span><span class="sxs-lookup"><span data-stu-id="3086e-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="3086e-282">File INI, JSON e XML</span><span class="sxs-lookup"><span data-stu-id="3086e-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="3086e-283">Provider di configurazione chiave per file</span><span class="sxs-lookup"><span data-stu-id="3086e-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="3086e-284">File della directory</span><span class="sxs-lookup"><span data-stu-id="3086e-284">Directory files</span></span> |
| [<span data-ttu-id="3086e-285">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="3086e-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="3086e-286">Raccolte in memoria</span><span class="sxs-lookup"><span data-stu-id="3086e-286">In-memory collections</span></span> |
| [<span data-ttu-id="3086e-287">Gestione segreta</span><span class="sxs-lookup"><span data-stu-id="3086e-287">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="3086e-288">File nella directory dei profili utente</span><span class="sxs-lookup"><span data-stu-id="3086e-288">File in the user profile directory</span></span> |

<span data-ttu-id="3086e-289">Le origini di configurazione vengono lette nell'ordine in cui sono specificati i provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="3086e-290">Ordinare i provider di configurazione nel codice in base alle priorità per le origini di configurazione sottostanti richieste dall'app.</span><span class="sxs-lookup"><span data-stu-id="3086e-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="3086e-291">Una sequenza tipica di provider di configurazione è:</span><span class="sxs-lookup"><span data-stu-id="3086e-291">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="3086e-292">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="3086e-292">*appsettings.json*</span></span>
1. <span data-ttu-id="3086e-293">*appSettings*. `Environment`. *JSON*</span><span class="sxs-lookup"><span data-stu-id="3086e-293">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="3086e-294">Gestione segreta</span><span class="sxs-lookup"><span data-stu-id="3086e-294">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="3086e-295">Variabili di ambiente che usano il [provider di configurazione delle variabili di ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="3086e-295">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="3086e-296">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="3086e-296">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="3086e-297">Una procedura comune consiste nell'aggiungere il provider di configurazione della riga di comando per ultimo in una serie di provider per consentire agli argomenti della riga di comando di eseguire l'override del set di configurazione da parte degli altri provider.</span><span class="sxs-lookup"><span data-stu-id="3086e-297">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="3086e-298">La sequenza di provider precedente viene utilizzata nella [configurazione predefinita](#default).</span><span class="sxs-lookup"><span data-stu-id="3086e-298">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="3086e-299">Prefissi della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="3086e-299">Connection string prefixes</span></span>

<span data-ttu-id="3086e-300">L'API di configurazione dispone di regole di elaborazione speciali per quattro variabili di ambiente della stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="3086e-300">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="3086e-301">Queste stringhe di connessione sono necessarie per configurare le stringhe di connessione di Azure per l'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="3086e-301">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="3086e-302">Le variabili di ambiente con i prefissi visualizzati nella tabella vengono caricate nell'app con la [configurazione predefinita](#default) o quando non viene fornito alcun `AddEnvironmentVariables`prefisso a.</span><span class="sxs-lookup"><span data-stu-id="3086e-302">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="3086e-303">Prefisso della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="3086e-303">Connection string prefix</span></span> | <span data-ttu-id="3086e-304">Provider</span><span class="sxs-lookup"><span data-stu-id="3086e-304">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="3086e-305">Provider personalizzato</span><span class="sxs-lookup"><span data-stu-id="3086e-305">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="3086e-306">MySQL</span><span class="sxs-lookup"><span data-stu-id="3086e-306">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="3086e-307">Database SQL di Azure</span><span class="sxs-lookup"><span data-stu-id="3086e-307">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="3086e-308">SQL Server</span><span class="sxs-lookup"><span data-stu-id="3086e-308">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="3086e-309">Quando una variabile di ambiente viene individuata e caricata nella configurazione con uno qualsiasi dei quattro prefissi indicati nella tabella:</span><span class="sxs-lookup"><span data-stu-id="3086e-309">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="3086e-310">La chiave di configurazione viene creata rimuovendo il prefisso della variabile di ambiente e aggiungendo una sezione per la chiave di configurazione (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="3086e-310">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="3086e-311">Viene creata una nuova coppia chiave-valore della configurazione che rappresenta il provider di connessione al database (ad eccezione di `CUSTOMCONNSTR_`, che non ha un provider dichiarato).</span><span class="sxs-lookup"><span data-stu-id="3086e-311">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="3086e-312">Chiave della variabile di ambiente</span><span class="sxs-lookup"><span data-stu-id="3086e-312">Environment variable key</span></span> | <span data-ttu-id="3086e-313">Chiave di configurazione convertita</span><span class="sxs-lookup"><span data-stu-id="3086e-313">Converted configuration key</span></span> | <span data-ttu-id="3086e-314">Voce di configurazione del provider</span><span class="sxs-lookup"><span data-stu-id="3086e-314">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="3086e-315">Voce di configurazione non creata.</span><span class="sxs-lookup"><span data-stu-id="3086e-315">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="3086e-316">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="3086e-316">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="3086e-317">Valore: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="3086e-317">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="3086e-318">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="3086e-318">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="3086e-319">Valore: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="3086e-319">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="3086e-320">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="3086e-320">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="3086e-321">Valore: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="3086e-321">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="3086e-322">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="3086e-322">JSON configuration provider</span></span>

<span data-ttu-id="3086e-323">Carica <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> la configurazione dalle coppie chiave-valore del file JSON.</span><span class="sxs-lookup"><span data-stu-id="3086e-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="3086e-324">Gli overload possono specificare:</span><span class="sxs-lookup"><span data-stu-id="3086e-324">Overloads can specify:</span></span>

* <span data-ttu-id="3086e-325">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="3086e-325">Whether the file is optional.</span></span>
* <span data-ttu-id="3086e-326">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="3086e-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="3086e-327">Esaminare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3086e-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="3086e-328">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="3086e-328">The preceding code:</span></span>

* <span data-ttu-id="3086e-329">Configura il provider di configurazione JSON per caricare il file *config. JSON* con le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="3086e-330">`optional: true`: Il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="3086e-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="3086e-331">`reloadOnChange: true`: Il file viene ricaricato quando vengono salvate le modifiche.</span><span class="sxs-lookup"><span data-stu-id="3086e-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="3086e-332">Legge i [provider di configurazione predefiniti](#default) prima del file *config. JSON* .</span><span class="sxs-lookup"><span data-stu-id="3086e-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="3086e-333">Impostazioni nell'impostazione di sostituzione del file *config. JSON* nei provider di configurazione predefiniti, tra cui il [provider di configurazione delle variabili di ambiente](#evcp) e il provider di configurazione della riga di [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="3086e-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="3086e-334">In genere ***non*** si vuole che un file JSON personalizzato esegua l'override dei valori impostati nel [provider di configurazione delle variabili di ambiente](#evcp) e nel provider di configurazione della riga di [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="3086e-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="3086e-335">Il codice seguente cancella tutti i provider di configurazione e aggiunge diversi provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="3086e-336">Nel codice precedente, Settings in *config. JSON* e *config*. `Environment`. file *JSON* :</span><span class="sxs-lookup"><span data-stu-id="3086e-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="3086e-337">Eseguire l'override delle impostazioni in *appSettings. JSON* e *appSettings*. `Environment`. file *JSON* .</span><span class="sxs-lookup"><span data-stu-id="3086e-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="3086e-338">Viene sottoposto a override dalle impostazioni del [provider di configurazione delle variabili di ambiente](#evcp) e del provider di configurazione della riga di [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="3086e-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="3086e-339">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il file *config. JSON* seguente:</span><span class="sxs-lookup"><span data-stu-id="3086e-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="3086e-340">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="3086e-341">Provider di configurazione file</span><span class="sxs-lookup"><span data-stu-id="3086e-341">File configuration provider</span></span>

<span data-ttu-id="3086e-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> è la classe base per il caricamento della configurazione dal file system.</span><span class="sxs-lookup"><span data-stu-id="3086e-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="3086e-343">I provider di configurazione seguenti derivano da `FileConfigurationProvider`:</span><span class="sxs-lookup"><span data-stu-id="3086e-343">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="3086e-344">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="3086e-344">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="3086e-345">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="3086e-345">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="3086e-346">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="3086e-346">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="3086e-347">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="3086e-347">INI configuration provider</span></span>

<span data-ttu-id="3086e-348"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carica la configurazione da coppie chiave-valore di file INI in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="3086e-348">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="3086e-349">Il codice seguente cancella tutti i provider di configurazione e aggiunge diversi provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="3086e-350">Nel codice precedente, le impostazioni in *MyIniConfig. ini* e *MyIniConfig*. `Environment`. i file *ini* vengono sottoposti a override dalle impostazioni nel:</span><span class="sxs-lookup"><span data-stu-id="3086e-350">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="3086e-351">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="3086e-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="3086e-352">[Provider di configurazione della riga di comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="3086e-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="3086e-353">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il seguente file *MyIniConfig. ini* :</span><span class="sxs-lookup"><span data-stu-id="3086e-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="3086e-354">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="3086e-355">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="3086e-355">XML configuration provider</span></span>

<span data-ttu-id="3086e-356">Il <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carica la configurazione da coppie chiave-valore di file XML in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="3086e-356">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="3086e-357">Il codice seguente cancella tutti i provider di configurazione e aggiunge diversi provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-357">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="3086e-358">Nel codice precedente, le impostazioni in *MyXMLFile. XML* e *MyXMLFile*. `Environment`. i file *XML* vengono sottoposti a override dalle impostazioni in:</span><span class="sxs-lookup"><span data-stu-id="3086e-358">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="3086e-359">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="3086e-359">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="3086e-360">[Provider di configurazione della riga di comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="3086e-360">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="3086e-361">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il seguente file *MyXMLFile. XML* :</span><span class="sxs-lookup"><span data-stu-id="3086e-361">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="3086e-362">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-362">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="3086e-363">La ripetizione di elementi che usano lo stesso nome di elemento funziona se si usa l'attributo `name` per distinguere gli elementi:</span><span class="sxs-lookup"><span data-stu-id="3086e-363">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="3086e-364">Il codice seguente legge il file di configurazione precedente e visualizza le chiavi e i valori:</span><span class="sxs-lookup"><span data-stu-id="3086e-364">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="3086e-365">È possibile usare attributi per fornire valori:</span><span class="sxs-lookup"><span data-stu-id="3086e-365">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="3086e-366">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="3086e-366">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="3086e-367">key:attribute</span><span class="sxs-lookup"><span data-stu-id="3086e-367">key:attribute</span></span>
* <span data-ttu-id="3086e-368">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="3086e-368">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="3086e-369">Provider di configurazione chiave per file</span><span class="sxs-lookup"><span data-stu-id="3086e-369">Key-per-file configuration provider</span></span>

<span data-ttu-id="3086e-370">Il <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa i file di una directory come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-370">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="3086e-371">La chiave è il nome del file.</span><span class="sxs-lookup"><span data-stu-id="3086e-371">The key is the file name.</span></span> <span data-ttu-id="3086e-372">Il valore contiene il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="3086e-372">The value contains the file's contents.</span></span> <span data-ttu-id="3086e-373">Il provider di configurazione chiave per file viene usato negli scenari di hosting di Docker.</span><span class="sxs-lookup"><span data-stu-id="3086e-373">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="3086e-374">Per attivare la configurazione chiave-per-file, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="3086e-374">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="3086e-375">Il `directoryPath` per i file deve essere un percorso assoluto.</span><span class="sxs-lookup"><span data-stu-id="3086e-375">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="3086e-376">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="3086e-376">Overloads permit specifying:</span></span>

* <span data-ttu-id="3086e-377">Un delegato `Action<KeyPerFileConfigurationSource>` che configura l'origine.</span><span class="sxs-lookup"><span data-stu-id="3086e-377">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="3086e-378">Se la directory è facoltativa e il percorso della directory.</span><span class="sxs-lookup"><span data-stu-id="3086e-378">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="3086e-379">Il doppio carattere di sottolineatura (`__`) viene usato come delimitatore per le chiavi di configurazione nei nomi dei file.</span><span class="sxs-lookup"><span data-stu-id="3086e-379">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="3086e-380">Ad esempio, il nome di file `Logging__LogLevel__System` produce la chiave di configurazione `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="3086e-380">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="3086e-381">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="3086e-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="3086e-382">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="3086e-382">Memory configuration provider</span></span>

<span data-ttu-id="3086e-383">Il <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una raccolta in memoria come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-383">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="3086e-384">Il codice seguente aggiunge una raccolta di memoria al sistema di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-384">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="3086e-385">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza le impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-385">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="3086e-386">Nel codice precedente, `config.AddInMemoryCollection(Dict)` viene aggiunto dopo i provider di [configurazione predefiniti](#default).</span><span class="sxs-lookup"><span data-stu-id="3086e-386">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="3086e-387">Per un esempio di ordinamento dei provider di configurazione, vedere [provider di configurazione JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="3086e-387">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="3086e-388">Vedere [associare una matrice](#boa) per un altro esempio `MemoryConfigurationProvider`di utilizzo di.</span><span class="sxs-lookup"><span data-stu-id="3086e-388">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="3086e-389">GetValue</span><span class="sxs-lookup"><span data-stu-id="3086e-389">GetValue</span></span>

<span data-ttu-id="3086e-390">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)estrae un singolo valore dalla configurazione con una chiave specificata e lo converte nel tipo specificato:</span><span class="sxs-lookup"><span data-stu-id="3086e-390">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="3086e-391">Nel codice precedente, se `NumberKey` non è stato trovato nella configurazione, viene usato il valore `99` predefinito di.</span><span class="sxs-lookup"><span data-stu-id="3086e-391">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="3086e-392">GetSection, GetChildren ed Exists</span><span class="sxs-lookup"><span data-stu-id="3086e-392">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="3086e-393">Per gli esempi che seguono, prendere in considerazione il seguente file *MySubsection. JSON* :</span><span class="sxs-lookup"><span data-stu-id="3086e-393">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="3086e-394">Il codice seguente aggiunge *MySubsection. JSON* ai provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-394">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="3086e-395">GetSection</span><span class="sxs-lookup"><span data-stu-id="3086e-395">GetSection</span></span>

<span data-ttu-id="3086e-396">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) restituisce una sottosezione di configurazione con la chiave della sottosezione specificata.</span><span class="sxs-lookup"><span data-stu-id="3086e-396">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="3086e-397">Il codice seguente restituisce i valori `section1`per:</span><span class="sxs-lookup"><span data-stu-id="3086e-397">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="3086e-398">Il codice seguente restituisce i valori `section2:subsection0`per:</span><span class="sxs-lookup"><span data-stu-id="3086e-398">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="3086e-399">`GetSection` non restituisce mai `null`.</span><span class="sxs-lookup"><span data-stu-id="3086e-399">`GetSection` never returns `null`.</span></span> <span data-ttu-id="3086e-400">Se non viene trovata una sezione corrispondente, viene restituita una `IConfigurationSection` vuota.</span><span class="sxs-lookup"><span data-stu-id="3086e-400">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="3086e-401">Quando `GetSection` restituisce una sezione corrispondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> non viene compilata.</span><span class="sxs-lookup"><span data-stu-id="3086e-401">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="3086e-402">Quando la sezione esiste, vengono restituiti <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.</span><span class="sxs-lookup"><span data-stu-id="3086e-402">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="3086e-403">GetChildren ed EXISTS</span><span class="sxs-lookup"><span data-stu-id="3086e-403">GetChildren and Exists</span></span>

<span data-ttu-id="3086e-404">Il codice seguente chiama [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) e restituisce i valori `section2:subsection0`per:</span><span class="sxs-lookup"><span data-stu-id="3086e-404">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="3086e-405">Il codice precedente chiama [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) per verificare che la sezione esista:</span><span class="sxs-lookup"><span data-stu-id="3086e-405">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="3086e-406">Associare una matrice</span><span class="sxs-lookup"><span data-stu-id="3086e-406">Bind an array</span></span>

<span data-ttu-id="3086e-407">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supporta l'associazione di matrici a oggetti usando gli indici di matrice nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-407">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="3086e-408">Qualsiasi formato di matrice che espone un segmento di chiave numerica è in grado di associare array a una matrice di classi [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="3086e-408">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="3086e-409">Si consideri il file con *estensione JSON* dal [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="3086e-409">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="3086e-410">Il codice seguente aggiunge il file *Array. JSON* ai provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-410">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="3086e-411">Il codice seguente legge la configurazione e Visualizza i valori:</span><span class="sxs-lookup"><span data-stu-id="3086e-411">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="3086e-412">Il codice precedente restituisce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="3086e-412">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="3086e-413">Nell'output precedente, index 3 ha un valore `value40`, corrispondente a `"4": "value40",` in *ArrayList. JSON*.</span><span class="sxs-lookup"><span data-stu-id="3086e-413">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="3086e-414">Gli indici di matrice associati sono continui e non sono associati all'indice della chiave di configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-414">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="3086e-415">Il binder di configurazione non è in grado di associare valori null o di creare voci null negli oggetti associati</span><span class="sxs-lookup"><span data-stu-id="3086e-415">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="3086e-416">Il codice seguente carica la `array:entries` configurazione con il <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="3086e-416">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="3086e-417">Il codice seguente legge la configurazione in `arrayDict` `Dictionary` e Visualizza i valori:</span><span class="sxs-lookup"><span data-stu-id="3086e-417">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="3086e-418">Il codice precedente restituisce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="3086e-418">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="3086e-419">L'indice &num;3 nell'oggetto associato contiene i dati di configurazione per la chiave di configurazione `array:4` e il relativo valore `value4`.</span><span class="sxs-lookup"><span data-stu-id="3086e-419">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="3086e-420">Quando i dati di configurazione contenenti una matrice sono associati, gli indici di matrice nelle chiavi di configurazione vengono usati per scorrere i dati di configurazione durante la creazione dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="3086e-420">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="3086e-421">Un valore null non può essere mantenuto nei dati di configurazione e una voce con valore null non viene creata in un oggetto associato quando una matrice nelle chiavi di configurazione ignora uno o più indici.</span><span class="sxs-lookup"><span data-stu-id="3086e-421">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="3086e-422">È possibile specificare l'elemento di &num;configurazione mancante per l' `ArrayExample` indice 3 prima di eseguire il binding all'istanza da qualsiasi provider di &num;configurazione che legga la coppia chiave/valore dell'indice 3.</span><span class="sxs-lookup"><span data-stu-id="3086e-422">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="3086e-423">Si consideri il seguente file *valore3. JSON* dal Download di esempio:</span><span class="sxs-lookup"><span data-stu-id="3086e-423">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="3086e-424">Il codice seguente include la configurazione per *valore3. JSON* e `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="3086e-424">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="3086e-425">Il codice seguente legge la configurazione precedente e Visualizza i valori:</span><span class="sxs-lookup"><span data-stu-id="3086e-425">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="3086e-426">Il codice precedente restituisce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="3086e-426">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="3086e-427">I provider di configurazione personalizzati non devono implementare l'associazione di matrici.</span><span class="sxs-lookup"><span data-stu-id="3086e-427">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="3086e-428">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="3086e-428">Custom configuration provider</span></span>

<span data-ttu-id="3086e-429">L'app di esempio dimostra come creare un provider di configurazione di base che legge le coppie chiave-valore di configurazione da un database usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="3086e-429">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="3086e-430">Il provider ha le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-430">The provider has the following characteristics:</span></span>

* <span data-ttu-id="3086e-431">Il database in memoria di Entity Framework viene usato a scopo dimostrativo.</span><span class="sxs-lookup"><span data-stu-id="3086e-431">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="3086e-432">Per usare un database che richiede una stringa di connessione, implementare un `ConfigurationBuilder` secondario per fornire la stringa di connessione da un altro provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-432">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="3086e-433">Il provider legge una tabella di database in una configurazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="3086e-433">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="3086e-434">Il provider non esegue una query sul database per ogni chiave.</span><span class="sxs-lookup"><span data-stu-id="3086e-434">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="3086e-435">Il ricaricamento in caso di modifica non è implementato, quindi l'aggiornamento del database dopo l'avvio dell'app non ha alcun effetto sulla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="3086e-435">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="3086e-436">Definire un'entità `EFConfigurationValue` per l'archiviazione dei valori di configurazione nel database.</span><span class="sxs-lookup"><span data-stu-id="3086e-436">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="3086e-437">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="3086e-437">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="3086e-438">Aggiungere `EFConfigurationContext` per archiviare i valori configurati e accedervi.</span><span class="sxs-lookup"><span data-stu-id="3086e-438">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="3086e-439">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="3086e-439">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="3086e-440">Creare una classe che implementi <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="3086e-440">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="3086e-441">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="3086e-441">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="3086e-442">Creare il provider di configurazione personalizzato ereditando da <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="3086e-442">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="3086e-443">Il provider di configurazione inizializza il database quando è vuoto.</span><span class="sxs-lookup"><span data-stu-id="3086e-443">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="3086e-444">Poiché le [chiavi di configurazione non fanno distinzione tra maiuscole](#keys)e minuscole, il dizionario utilizzato per inizializzare il database viene creato con l'operatore di confronto senza distinzione tra maiuscole e minuscole ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="3086e-444">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="3086e-445">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="3086e-445">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="3086e-446">Un metodo di estensione `AddEFConfiguration` consente di aggiungere l'origine di configurazione a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3086e-446">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="3086e-447">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="3086e-447">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="3086e-448">L'esempio di codice seguente mostra come usare il `EFConfigurationProvider` personalizzato in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="3086e-448">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="3086e-449">Accedi alla configurazione all'avvio</span><span class="sxs-lookup"><span data-stu-id="3086e-449">Access configuration in Startup</span></span>

<span data-ttu-id="3086e-450">Il codice seguente consente di visualizzare i `Startup` dati di configurazione nei metodi:</span><span class="sxs-lookup"><span data-stu-id="3086e-450">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="3086e-451">Per un esempio di accesso alla configurazione usando metodi di servizio di avvio, vedere [Avvio dell'applicazione: Metodi pratici](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="3086e-451">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="3086e-452">Configurazione dell'accesso in Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3086e-452">Access configuration in Razor Pages</span></span>

<span data-ttu-id="3086e-453">Il codice seguente Visualizza i dati di configurazione in una pagina Razor:</span><span class="sxs-lookup"><span data-stu-id="3086e-453">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="3086e-454">Accedere alla configurazione in un file di visualizzazione MVC</span><span class="sxs-lookup"><span data-stu-id="3086e-454">Access configuration in a MVC view file</span></span>

<span data-ttu-id="3086e-455">Il codice seguente consente di visualizzare i dati di configurazione in una visualizzazione MVC:</span><span class="sxs-lookup"><span data-stu-id="3086e-455">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="3086e-456">Host e configurazione delle app</span><span class="sxs-lookup"><span data-stu-id="3086e-456">Host versus app configuration</span></span>

<span data-ttu-id="3086e-457">Prima che l'app venga configurata e avviata, viene configurato e avviato un *host*.</span><span class="sxs-lookup"><span data-stu-id="3086e-457">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="3086e-458">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="3086e-458">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="3086e-459">Sia l'app che l'host vengono configurati tramite i provider di configurazione descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="3086e-459">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="3086e-460">Anche le coppie chiave-valore di configurazione dell'host sono incluse nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="3086e-460">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="3086e-461">Per altre informazioni su come vengono usati i provider di configurazione quando viene creato l'host e sugli effetti delle origini di configurazione sull'host di configurazione, vedere <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="3086e-461">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="3086e-462">Configurazione host predefinita</span><span class="sxs-lookup"><span data-stu-id="3086e-462">Default host configuration</span></span>

<span data-ttu-id="3086e-463">Per informazioni dettagliate sulla configurazione predefinita quando viene usato l'[host Web](xref:fundamentals/host/web-host), vedere la [versione di questo argomento per ASP.NET Core 2.2](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="3086e-463">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="3086e-464">La configurazione dell'host viene specificata da:</span><span class="sxs-lookup"><span data-stu-id="3086e-464">Host configuration is provided from:</span></span>
  * <span data-ttu-id="3086e-465">Variabili di ambiente con `DOTNET_` prefisso (ad esempio, `DOTNET_ENVIRONMENT`) utilizzando il [provider di configurazione delle variabili di ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="3086e-465">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="3086e-466">Il prefisso (`DOTNET_`) viene rimosso al caricamento delle coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-466">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="3086e-467">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="3086e-467">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="3086e-468">La configurazione predefinita dell'host Web viene stabilita (`ConfigureWebHostDefaults`) nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="3086e-468">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="3086e-469">Kestrel viene usato come server Web e configurato con i provider di configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="3086e-469">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="3086e-470">Aggiungere il middleware di filtro host.</span><span class="sxs-lookup"><span data-stu-id="3086e-470">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="3086e-471">Aggiungere il middleware delle intestazioni inoltrate se la variabile di ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è impostata su `true`.</span><span class="sxs-lookup"><span data-stu-id="3086e-471">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="3086e-472">Abilitare l'integrazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="3086e-472">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="3086e-473">Altra configurazione</span><span class="sxs-lookup"><span data-stu-id="3086e-473">Other configuration</span></span>

<span data-ttu-id="3086e-474">Questo argomento riguarda solo la *configurazione dell'app*.</span><span class="sxs-lookup"><span data-stu-id="3086e-474">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="3086e-475">Altri aspetti dell'esecuzione e dell'hosting di app ASP.NET Core sono configurati usando i file di configurazione non trattati in questo argomento:</span><span class="sxs-lookup"><span data-stu-id="3086e-475">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="3086e-476">*Launch. JSON*/*launchSettings. JSON* sono i file di configurazione degli strumenti per l'ambiente di sviluppo, descritti:</span><span class="sxs-lookup"><span data-stu-id="3086e-476">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="3086e-477">In <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="3086e-477">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="3086e-478">Nella documentazione in cui vengono usati i file per configurare ASP.NET Core app per scenari di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="3086e-478">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="3086e-479">*Web. config* è un file di configurazione del server, descritto negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-479">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="3086e-480">Per ulteriori informazioni sulla migrazione della configurazione dell'app da versioni precedenti di ASP.NET <xref:migration/proper-to-2x/index#store-configurations>, vedere.</span><span class="sxs-lookup"><span data-stu-id="3086e-480">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="3086e-481">Aggiungere la configurazione da un assembly esterno</span><span class="sxs-lookup"><span data-stu-id="3086e-481">Add configuration from an external assembly</span></span>

<span data-ttu-id="3086e-482">Un'implementazione <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> consente l'aggiunta di miglioramenti a un'app all'avvio, da un assembly esterno alla classe `Startup` dell'app.</span><span class="sxs-lookup"><span data-stu-id="3086e-482">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="3086e-483">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3086e-483">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3086e-484">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3086e-484">Additional resources</span></span>

* [<span data-ttu-id="3086e-485">Codice sorgente configurazione</span><span class="sxs-lookup"><span data-stu-id="3086e-485">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3086e-486">La configurazione delle app in ASP.NET Core si basa su coppie chiave-valore stabilite dai *provider di configurazione*.</span><span class="sxs-lookup"><span data-stu-id="3086e-486">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="3086e-487">I provider di configurazione leggono i dati di configurazione in coppie chiave-valore da un'ampia gamma di origini di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-487">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="3086e-488">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="3086e-488">Azure Key Vault</span></span>
* <span data-ttu-id="3086e-489">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="3086e-489">Azure App Configuration</span></span>
* <span data-ttu-id="3086e-490">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="3086e-490">Command-line arguments</span></span>
* <span data-ttu-id="3086e-491">Provider personalizzati (installati o creati)</span><span class="sxs-lookup"><span data-stu-id="3086e-491">Custom providers (installed or created)</span></span>
* <span data-ttu-id="3086e-492">File della directory</span><span class="sxs-lookup"><span data-stu-id="3086e-492">Directory files</span></span>
* <span data-ttu-id="3086e-493">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="3086e-493">Environment variables</span></span>
* <span data-ttu-id="3086e-494">Oggetti .NET in memoria</span><span class="sxs-lookup"><span data-stu-id="3086e-494">In-memory .NET objects</span></span>
* <span data-ttu-id="3086e-495">File di impostazioni</span><span class="sxs-lookup"><span data-stu-id="3086e-495">Settings files</span></span>

<span data-ttu-id="3086e-496">I pacchetti di configurazione per gli scenari di provider di configurazione comuni ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sono inclusi nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="3086e-496">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="3086e-497">Gli esempi di codice che seguono e quelli inclusi nell'app di esempio usano lo spazio dei nomi <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="3086e-497">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="3086e-498">Il *modello di opzioni* è un'estensione dei concetti di configurazione descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="3086e-498">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="3086e-499">Le opzioni usano le classi per rappresentare i gruppi di impostazioni correlate.</span><span class="sxs-lookup"><span data-stu-id="3086e-499">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="3086e-500">Per altre informazioni, vedere <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="3086e-500">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="3086e-501">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3086e-501">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="3086e-502">Host e configurazione delle app</span><span class="sxs-lookup"><span data-stu-id="3086e-502">Host versus app configuration</span></span>

<span data-ttu-id="3086e-503">Prima che l'app venga configurata e avviata, viene configurato e avviato un *host*.</span><span class="sxs-lookup"><span data-stu-id="3086e-503">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="3086e-504">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="3086e-504">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="3086e-505">Sia l'app che l'host vengono configurati tramite i provider di configurazione descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="3086e-505">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="3086e-506">Anche le coppie chiave-valore di configurazione dell'host sono incluse nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="3086e-506">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="3086e-507">Per altre informazioni su come vengono usati i provider di configurazione quando viene creato l'host e sugli effetti delle origini di configurazione sull'host di configurazione, vedere <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="3086e-507">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="3086e-508">Altra configurazione</span><span class="sxs-lookup"><span data-stu-id="3086e-508">Other configuration</span></span>

<span data-ttu-id="3086e-509">Questo argomento riguarda solo la *configurazione dell'app*.</span><span class="sxs-lookup"><span data-stu-id="3086e-509">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="3086e-510">Altri aspetti dell'esecuzione e dell'hosting di app ASP.NET Core sono configurati usando i file di configurazione non trattati in questo argomento:</span><span class="sxs-lookup"><span data-stu-id="3086e-510">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="3086e-511">*Launch. JSON*/*launchSettings. JSON* sono i file di configurazione degli strumenti per l'ambiente di sviluppo, descritti:</span><span class="sxs-lookup"><span data-stu-id="3086e-511">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="3086e-512">In <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="3086e-512">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="3086e-513">Nella documentazione in cui vengono usati i file per configurare ASP.NET Core app per scenari di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="3086e-513">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="3086e-514">*Web. config* è un file di configurazione del server, descritto negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-514">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="3086e-515">Per ulteriori informazioni sulla migrazione della configurazione dell'app da versioni precedenti di ASP.NET <xref:migration/proper-to-2x/index#store-configurations>, vedere.</span><span class="sxs-lookup"><span data-stu-id="3086e-515">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="3086e-516">Configurazione predefinita</span><span class="sxs-lookup"><span data-stu-id="3086e-516">Default configuration</span></span>

<span data-ttu-id="3086e-517">Le app basate sui modelli [dotnet new](/dotnet/core/tools/dotnet-new) di ASP.NET Core chiamano <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> durante la compilazione di un host.</span><span class="sxs-lookup"><span data-stu-id="3086e-517">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="3086e-518">`CreateDefaultBuilder` fornisce la configurazione predefinita per l'app nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="3086e-518">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="3086e-519">La configurazione seguente si applica alle app che usano l'[host Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="3086e-519">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="3086e-520">Per informazioni dettagliate sulla configurazione predefinita quando viene usato l'[host generico](xref:fundamentals/host/generic-host), vedere la [versione più recente di questo argomento](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="3086e-520">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="3086e-521">La configurazione dell'host viene specificata da:</span><span class="sxs-lookup"><span data-stu-id="3086e-521">Host configuration is provided from:</span></span>
  * <span data-ttu-id="3086e-522">Variabili di ambiente con prefisso `ASPNETCORE_` (ad esempio `ASPNETCORE_ENVIRONMENT`) che usano il [provider di configurazione delle variabili di ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="3086e-522">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="3086e-523">Il prefisso (`ASPNETCORE_`) viene rimosso al caricamento delle coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-523">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="3086e-524">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="3086e-524">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="3086e-525">La configurazione dell'app viene fornita da:</span><span class="sxs-lookup"><span data-stu-id="3086e-525">App configuration is provided from:</span></span>
  * <span data-ttu-id="3086e-526">*appsettings.json* mediante il [provider di configurazione dei file](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="3086e-526">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="3086e-527">*appsettings.{Ambiente}.json* mediante il [provider di configurazione dei file](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="3086e-527">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="3086e-528">[Strumento di gestione dei segreti](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development` usando l'assembly di ingresso.</span><span class="sxs-lookup"><span data-stu-id="3086e-528">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="3086e-529">Variabili di ambiente che usano il [provider di configurazione delle variabili di ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="3086e-529">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="3086e-530">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="3086e-530">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="3086e-531">Sicurezza</span><span class="sxs-lookup"><span data-stu-id="3086e-531">Security</span></span>

<span data-ttu-id="3086e-532">Per proteggere i dati di configurazione sensibili, adottare le pratiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-532">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="3086e-533">Non archiviare mai la password o altri dati sensibili nel codice del provider di configurazione o in file di configurazione di testo normale.</span><span class="sxs-lookup"><span data-stu-id="3086e-533">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="3086e-534">Non usare i segreti di produzione in ambienti di sviluppo o di test.</span><span class="sxs-lookup"><span data-stu-id="3086e-534">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="3086e-535">Specificare i segreti all'esterno del progetto in modo che non possano essere inavvertitamente inviati a un repository del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="3086e-535">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="3086e-536">Per altre informazioni, vedere i seguenti argomenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-536">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="3086e-537"><xref:security/app-secrets>&ndash; Include consigli sull'uso delle variabili di ambiente per archiviare dati riservati.</span><span class="sxs-lookup"><span data-stu-id="3086e-537"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="3086e-538">Secret Manager usa il provider di configurazione dei file per archiviare i segreti utente in un file JSON nel sistema locale.</span><span class="sxs-lookup"><span data-stu-id="3086e-538">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="3086e-539">Il provider di configurazione dei file è descritto più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="3086e-539">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="3086e-540">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) archivia in modo sicuro i segreti delle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3086e-540">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="3086e-541">Per altre informazioni, vedere <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3086e-541">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="3086e-542">Dati di configurazione gerarchici</span><span class="sxs-lookup"><span data-stu-id="3086e-542">Hierarchical configuration data</span></span>

<span data-ttu-id="3086e-543">L'API di configurazione è in grado di mantenere i dati di configurazione gerarchici rendendoli flat grazie all'uso di un delimitatore nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-543">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="3086e-544">Nel file JSON seguente, esistono quattro chiavi in una gerarchia strutturata di due sezioni:</span><span class="sxs-lookup"><span data-stu-id="3086e-544">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="3086e-545">Quando il file viene letto nella configurazione, vengono create chiavi univoche per mantenere la struttura di dati gerarchici originale dell'origine di configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-545">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="3086e-546">Le sezioni e le chiavi vengono rese flat usando due punti (`:`) per mantenere la struttura originale:</span><span class="sxs-lookup"><span data-stu-id="3086e-546">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="3086e-547">section0:key0</span><span class="sxs-lookup"><span data-stu-id="3086e-547">section0:key0</span></span>
* <span data-ttu-id="3086e-548">section0:key1</span><span class="sxs-lookup"><span data-stu-id="3086e-548">section0:key1</span></span>
* <span data-ttu-id="3086e-549">section1:key0</span><span class="sxs-lookup"><span data-stu-id="3086e-549">section1:key0</span></span>
* <span data-ttu-id="3086e-550">section1:key1</span><span class="sxs-lookup"><span data-stu-id="3086e-550">section1:key1</span></span>

<span data-ttu-id="3086e-551">I metodi <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sono disponibili per isolare le sezioni e gli elementi figlio di una sezione nei dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-551"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="3086e-552">Questi metodi sono descritti più avanti in [GetSection, GetChildren ed Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="3086e-552">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="3086e-553">Convenzioni</span><span class="sxs-lookup"><span data-stu-id="3086e-553">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="3086e-554">Origini e provider</span><span class="sxs-lookup"><span data-stu-id="3086e-554">Sources and providers</span></span>

<span data-ttu-id="3086e-555">All'avvio dell'app, le origini di configurazione vengono lette nell'ordine con cui sono specificati i rispettivi provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-555">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="3086e-556">I provider di configurazione che implementano il rilevamento delle modifiche sono in grado di ricaricare la configurazione quando viene modificata un'impostazione sottostante.</span><span class="sxs-lookup"><span data-stu-id="3086e-556">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="3086e-557">Ad esempio, il provider di configurazione dei file (descritto più avanti in questo argomento) e il [provider di configurazione di Azure Key Vault](xref:security/key-vault-configuration) implementano il rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="3086e-557">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="3086e-558"><xref:Microsoft.Extensions.Configuration.IConfiguration> è disponibile nel contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) dell'app.</span><span class="sxs-lookup"><span data-stu-id="3086e-558"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3086e-559"><xref:Microsoft.Extensions.Configuration.IConfiguration>può essere inserito in un Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> o MVC <xref:Microsoft.AspNetCore.Mvc.Controller> per ottenere la configurazione per la classe.</span><span class="sxs-lookup"><span data-stu-id="3086e-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="3086e-560">Negli esempi seguenti il `_config` campo viene usato per accedere ai valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-560">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="3086e-561">I provider di configurazione non possono usare l'inserimento delle dipendenze, perché non è disponibile quando vengono configurati dall'host.</span><span class="sxs-lookup"><span data-stu-id="3086e-561">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="3086e-562">Tasti</span><span class="sxs-lookup"><span data-stu-id="3086e-562">Keys</span></span>

<span data-ttu-id="3086e-563">Le chiavi di configurazione adottano le convenzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-563">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="3086e-564">Per le chiavi non viene fatta distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="3086e-564">Keys are case-insensitive.</span></span> <span data-ttu-id="3086e-565">Ad esempio, `ConnectionString` e `connectionstring` vengono considerate chiavi equivalenti.</span><span class="sxs-lookup"><span data-stu-id="3086e-565">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="3086e-566">Se un valore per la stessa chiave viene impostato dallo stesso provider di configurazione o da provider diversi, viene usato l'ultimo valore impostato per la chiave.</span><span class="sxs-lookup"><span data-stu-id="3086e-566">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="3086e-567">Chiavi gerarchiche</span><span class="sxs-lookup"><span data-stu-id="3086e-567">Hierarchical keys</span></span>
  * <span data-ttu-id="3086e-568">Nell'ambito dell'API di configurazione, il separatore due punti (`:`) funziona in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="3086e-568">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="3086e-569">Nelle variabili di ambiente, un separatore due punti potrebbe non funzionare in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="3086e-569">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="3086e-570">Il doppio carattere di sottolineatura (`__`) è supportato da tutte le piattaforme e viene convertito automaticamente nei due punti.</span><span class="sxs-lookup"><span data-stu-id="3086e-570">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="3086e-571">In Azure Key Vault, le chiavi gerarchiche usano `--` (due trattini) come separatore.</span><span class="sxs-lookup"><span data-stu-id="3086e-571">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="3086e-572">Scrivere il codice per sostituire i trattini con i due punti quando i segreti vengono caricati nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="3086e-572">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="3086e-573">Il <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supporta l'associazione di matrici agli oggetti usando gli indici delle matrici nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-573">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="3086e-574">L'associazione di matrici è descritta nella sezione [Associare una matrice a una classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="3086e-574">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="3086e-575">Valori</span><span class="sxs-lookup"><span data-stu-id="3086e-575">Values</span></span>

<span data-ttu-id="3086e-576">I valori di configurazione adottano le convenzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-576">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="3086e-577">I valori sono stringhe.</span><span class="sxs-lookup"><span data-stu-id="3086e-577">Values are strings.</span></span>
* <span data-ttu-id="3086e-578">I valori null non possono essere archiviati nella configurazione o associati a oggetti.</span><span class="sxs-lookup"><span data-stu-id="3086e-578">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="3086e-579">Provider</span><span class="sxs-lookup"><span data-stu-id="3086e-579">Providers</span></span>

<span data-ttu-id="3086e-580">La tabella seguente mostra i provider di configurazione disponibili per le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3086e-580">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="3086e-581">Provider</span><span class="sxs-lookup"><span data-stu-id="3086e-581">Provider</span></span> | <span data-ttu-id="3086e-582">Fornisce la configurazione da&hellip;</span><span class="sxs-lookup"><span data-stu-id="3086e-582">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="3086e-583">[Provider di configurazione di Azure Key Vault](xref:security/key-vault-configuration) (argomenti *Sicurezza*)</span><span class="sxs-lookup"><span data-stu-id="3086e-583">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="3086e-584">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="3086e-584">Azure Key Vault</span></span> |
| <span data-ttu-id="3086e-585">[Provider di Configurazione app](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Documentazione di Azure)</span><span class="sxs-lookup"><span data-stu-id="3086e-585">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="3086e-586">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="3086e-586">Azure App Configuration</span></span> |
| [<span data-ttu-id="3086e-587">Provider di configurazione della riga di comando</span><span class="sxs-lookup"><span data-stu-id="3086e-587">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="3086e-588">Parametri della riga di comando</span><span class="sxs-lookup"><span data-stu-id="3086e-588">Command-line parameters</span></span> |
| [<span data-ttu-id="3086e-589">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="3086e-589">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="3086e-590">Origine personalizzata</span><span class="sxs-lookup"><span data-stu-id="3086e-590">Custom source</span></span> |
| [<span data-ttu-id="3086e-591">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="3086e-591">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="3086e-592">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="3086e-592">Environment variables</span></span> |
| [<span data-ttu-id="3086e-593">Provider di configurazione file</span><span class="sxs-lookup"><span data-stu-id="3086e-593">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="3086e-594">File (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="3086e-594">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="3086e-595">Provider di configurazione KeyPerFile</span><span class="sxs-lookup"><span data-stu-id="3086e-595">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="3086e-596">File della directory</span><span class="sxs-lookup"><span data-stu-id="3086e-596">Directory files</span></span> |
| [<span data-ttu-id="3086e-597">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="3086e-597">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="3086e-598">Raccolte in memoria</span><span class="sxs-lookup"><span data-stu-id="3086e-598">In-memory collections</span></span> |
| <span data-ttu-id="3086e-599">[Segreti utente (Secret Manager)](xref:security/app-secrets) (argomenti *Sicurezza*)</span><span class="sxs-lookup"><span data-stu-id="3086e-599">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="3086e-600">File nella directory dei profili utente</span><span class="sxs-lookup"><span data-stu-id="3086e-600">File in the user profile directory</span></span> |

<span data-ttu-id="3086e-601">Le origini di configurazione vengono lette nell'ordine in cui vengono specificati i rispetti provider di configurazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="3086e-601">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="3086e-602">I provider di configurazione descritti in questo argomento sono descritti in ordine alfabetico, non nell'ordine in cui il codice li dispone.</span><span class="sxs-lookup"><span data-stu-id="3086e-602">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="3086e-603">Ordinare i provider di configurazione nel codice in base alle priorità per le origini di configurazione sottostanti richieste dall'app.</span><span class="sxs-lookup"><span data-stu-id="3086e-603">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="3086e-604">Una sequenza tipica di provider di configurazione è:</span><span class="sxs-lookup"><span data-stu-id="3086e-604">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="3086e-605">Files (*appSettings. JSON*, *appSettings. { Environment}. JSON*, dove `{Environment}` è l'ambiente host corrente dell'app)</span><span class="sxs-lookup"><span data-stu-id="3086e-605">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="3086e-606">Insieme di credenziali chiave Azure</span><span class="sxs-lookup"><span data-stu-id="3086e-606">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="3086e-607">[Segreti utente (Secret Manager)](xref:security/app-secrets) (solo nell'ambiente di sviluppo)</span><span class="sxs-lookup"><span data-stu-id="3086e-607">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="3086e-608">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="3086e-608">Environment variables</span></span>
1. <span data-ttu-id="3086e-609">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="3086e-609">Command-line arguments</span></span>

<span data-ttu-id="3086e-610">È pratica comune posizionare il provider di configurazione della riga di comando per ultimo in una serie di provider per consentire agli argomenti della riga di comando di sostituire la configurazione impostata da altri provider.</span><span class="sxs-lookup"><span data-stu-id="3086e-610">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="3086e-611">La sequenza di provider precedente viene utilizzata quando viene inizializzato un nuovo generatore host `CreateDefaultBuilder`con.</span><span class="sxs-lookup"><span data-stu-id="3086e-611">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="3086e-612">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="3086e-612">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="3086e-613">Configurare il generatore di host con UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="3086e-613">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="3086e-614">Per configurare il generatore di host, chiamare <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> sul generatore di host con la configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-614">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="3086e-615">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="3086e-615">ConfigureAppConfiguration</span></span>

<span data-ttu-id="3086e-616">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare i provider di configurazione dell'app, oltre a quelli aggiunti automaticamente da `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="3086e-616">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="3086e-617">Sostituire la configurazione precedente con argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="3086e-617">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="3086e-618">Per fornire la configurazione dell'app che può essere sostituita con argomenti della riga di comando, chiamare `AddCommandLine` per ultimo:</span><span class="sxs-lookup"><span data-stu-id="3086e-618">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="3086e-619">Rimuovere i provider aggiunti da CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="3086e-619">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="3086e-620">Per rimuovere i provider aggiunti da `CreateDefaultBuilder`, chiamare prima [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) in [IConfigurationBuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="3086e-620">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="3086e-621">Utilizzare la configurazione durante l'avvio dell'app</span><span class="sxs-lookup"><span data-stu-id="3086e-621">Consume configuration during app startup</span></span>

<span data-ttu-id="3086e-622">La configurazione fornita all'app in `ConfigureAppConfiguration` è disponibile durante l'avvio dell'app, incluso `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3086e-622">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3086e-623">Per altre informazioni, vedere la sezione [Accedere alla configurazione durante l'avvio](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="3086e-623">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="3086e-624">Provider di configurazione della riga di comando</span><span class="sxs-lookup"><span data-stu-id="3086e-624">Command-line Configuration Provider</span></span>

<span data-ttu-id="3086e-625"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carica la configurazione da coppie chiave-valore di argomenti della riga di comando in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="3086e-625">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="3086e-626">Per attivare la configurazione della riga di comando, metodo di estensione <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> viene chiamato su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="3086e-626">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="3086e-627">`AddCommandLine` viene chiamato automaticamente quando viene chiamato il metodo `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="3086e-627">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="3086e-628">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="3086e-628">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="3086e-629">`CreateDefaultBuilder` carica anche:</span><span class="sxs-lookup"><span data-stu-id="3086e-629">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="3086e-630">Una configurazione facoltativa dai file *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="3086e-630">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="3086e-631">[Segreti utente (Secret Manager)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="3086e-631">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="3086e-632">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="3086e-632">Environment variables.</span></span>

<span data-ttu-id="3086e-633">`CreateDefaultBuilder` aggiunge il provider di configurazione della riga di comando per ultimo.</span><span class="sxs-lookup"><span data-stu-id="3086e-633">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="3086e-634">Gli argomenti della riga di comando passati in fase di esecuzione sostituiscono la configurazione impostata dagli altri provider.</span><span class="sxs-lookup"><span data-stu-id="3086e-634">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="3086e-635">`CreateDefaultBuilder` opera durante la creazione dell'host.</span><span class="sxs-lookup"><span data-stu-id="3086e-635">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="3086e-636">Pertanto, la configurazione della riga di comando attivata da `CreateDefaultBuilder` può influire sul modo in cui l'host viene configurato.</span><span class="sxs-lookup"><span data-stu-id="3086e-636">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="3086e-637">Per le app basate sui modelli di ASP.NET Core, `AddCommandLine` è già stato chiamato da `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3086e-637">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="3086e-638">Per aggiungere altri provider di configurazione e mantenere la possibilità di sostituire la configurazione di tali provider con argomenti della riga di comando, chiamare i provider aggiuntivi dell'app in `ConfigureAppConfiguration` e quindi chiamare `AddCommandLine` per ultimo.</span><span class="sxs-lookup"><span data-stu-id="3086e-638">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="3086e-639">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="3086e-639">**Example**</span></span>

<span data-ttu-id="3086e-640">L'app di esempio consente di sfruttare il metodo di servizio statico `CreateDefaultBuilder` per creare l'host, che include una chiamata a <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="3086e-640">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="3086e-641">Aprire un prompt dei comandi nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="3086e-641">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="3086e-642">Fornire un argomento della riga di comando per il comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="3086e-642">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="3086e-643">Dopo che l'app è in esecuzione, aprire un browser per l'app all'indirizzo `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="3086e-643">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="3086e-644">Notare che l'output contiene la coppia chiave-valore per l'argomento della riga di comando di configurazione fornito per `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="3086e-644">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="3086e-645">Arguments</span><span class="sxs-lookup"><span data-stu-id="3086e-645">Arguments</span></span>

<span data-ttu-id="3086e-646">Il valore deve seguire un segno di uguale (`=`) o la chiave deve avere un prefisso (`--` o `/`) quando il valore segue uno spazio.</span><span class="sxs-lookup"><span data-stu-id="3086e-646">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="3086e-647">Il valore non è necessario se viene usato un segno di uguale, ad esempio `CommandLineKey=`.</span><span class="sxs-lookup"><span data-stu-id="3086e-647">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="3086e-648">Prefisso chiave</span><span class="sxs-lookup"><span data-stu-id="3086e-648">Key prefix</span></span>               | <span data-ttu-id="3086e-649">Esempio</span><span class="sxs-lookup"><span data-stu-id="3086e-649">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="3086e-650">Nessun prefisso</span><span class="sxs-lookup"><span data-stu-id="3086e-650">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="3086e-651">Due trattini (`--`)</span><span class="sxs-lookup"><span data-stu-id="3086e-651">Two dashes (`--`)</span></span>        | <span data-ttu-id="3086e-652">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="3086e-652">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="3086e-653">Barra (`/`)</span><span class="sxs-lookup"><span data-stu-id="3086e-653">Forward slash (`/`)</span></span>      | <span data-ttu-id="3086e-654">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="3086e-654">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="3086e-655">Nello stesso comando, non mischiare coppie chiave-valore di argomenti della riga di comando che usano un segno di uguale con coppie chiave-valore che usano uno spazio.</span><span class="sxs-lookup"><span data-stu-id="3086e-655">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="3086e-656">Comandi di esempio:</span><span class="sxs-lookup"><span data-stu-id="3086e-656">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="3086e-657">Mapping di sostituzione</span><span class="sxs-lookup"><span data-stu-id="3086e-657">Switch mappings</span></span>

<span data-ttu-id="3086e-658">I mapping di sostituzione consentono di usare la logica di sostituzione del nome della chiave.</span><span class="sxs-lookup"><span data-stu-id="3086e-658">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="3086e-659">Quando si compila manualmente la <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> configurazione con un oggetto, fornire un dizionario di sostituzioni switch al metodo.</span><span class="sxs-lookup"><span data-stu-id="3086e-659">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="3086e-660">Quando viene utilizzato il dizionario dei mapping di sostituzione, nel dizionario viene controllata la presenza di una chiave corrispondente alla chiave fornita da un argomento della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="3086e-660">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="3086e-661">Se la chiave della riga di comando viene trovata nel dizionario, il valore del dizionario (la sostituzione della chiave) viene passato nuovamente per impostare la coppia chiave-valore nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="3086e-661">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="3086e-662">Un mapping di sostituzione è necessario per le chiavi della riga di comando con un trattino singolo (`-`) come prefisso.</span><span class="sxs-lookup"><span data-stu-id="3086e-662">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="3086e-663">Regole principali del dizionario dei mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="3086e-663">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="3086e-664">Le sostituzioni devono iniziare con un trattino (`-`) o un trattino doppio (`--`).</span><span class="sxs-lookup"><span data-stu-id="3086e-664">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="3086e-665">Il dizionario dei mapping di sostituzione non deve contenere chiavi duplicate.</span><span class="sxs-lookup"><span data-stu-id="3086e-665">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="3086e-666">Creare un dizionario dei mapping di sostituzione.</span><span class="sxs-lookup"><span data-stu-id="3086e-666">Create a switch mappings dictionary.</span></span> <span data-ttu-id="3086e-667">Nell'esempio seguente vengono creati due mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="3086e-667">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="3086e-668">Quando viene creato l'host, chiamare `AddCommandLine` con il dizionario dei mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="3086e-668">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="3086e-669">Per le app che usano i mapping di sostituzione, la chiamata a `CreateDefaultBuilder` non deve passare argomenti.</span><span class="sxs-lookup"><span data-stu-id="3086e-669">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="3086e-670">La chiamata `AddCommandLine` del metodo `CreateDefaultBuilder` non include sostituzioni mappate e non è possibile passare il dizionario dei mapping di sostituzione a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3086e-670">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="3086e-671">La soluzione consiste nel non passare gli argomenti a `CreateDefaultBuilder` consentendo invece al metodo `AddCommandLine` del metodo `ConfigurationBuilder` di elaborare entrambi gli argomenti e il dizionario dei mapping di sostituzione.</span><span class="sxs-lookup"><span data-stu-id="3086e-671">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="3086e-672">Il dizionario dei mapping di sostituzione creato contiene i dati visualizzati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="3086e-672">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="3086e-673">Chiave</span><span class="sxs-lookup"><span data-stu-id="3086e-673">Key</span></span>       | <span data-ttu-id="3086e-674">Valore</span><span class="sxs-lookup"><span data-stu-id="3086e-674">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="3086e-675">Se le chiavi con mapping di sostituzione vengono usate all'avvio dell'app, la configurazione riceve il valore di configurazione per la chiave fornita dal dizionario:</span><span class="sxs-lookup"><span data-stu-id="3086e-675">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="3086e-676">Dopo aver eseguito il comando precedente, la configurazione contiene i valori mostrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="3086e-676">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="3086e-677">Chiave</span><span class="sxs-lookup"><span data-stu-id="3086e-677">Key</span></span>               | <span data-ttu-id="3086e-678">Valore</span><span class="sxs-lookup"><span data-stu-id="3086e-678">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="3086e-679">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="3086e-679">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="3086e-680"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carica la configurazione da coppie chiave-valore di variabili di ambiente in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="3086e-680">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="3086e-681">Per attivare la configurazione delle variabili di ambiente, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="3086e-681">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="3086e-682">[App Azure servizio](https://azure.microsoft.com/services/app-service/) consente di impostare le variabili di ambiente nel portale di Azure che possono eseguire l'override della configurazione dell'app usando il provider di configurazione delle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="3086e-682">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="3086e-683">Per altre informazioni, vedere [App di Azure: Eseguire l'override della configurazione delle app usando il portale di Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="3086e-683">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="3086e-684">`AddEnvironmentVariables` consente di caricare variabili di ambiente con prefisso `ASPNETCORE_` per la [configurazione host](#host-versus-app-configuration) quando viene inizializzato un nuovo generatore di host con l'[host Web](xref:fundamentals/host/web-host) e viene chiamato `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3086e-684">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="3086e-685">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="3086e-685">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="3086e-686">`CreateDefaultBuilder` carica anche:</span><span class="sxs-lookup"><span data-stu-id="3086e-686">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="3086e-687">Configurazione delle app dalle variabili di ambiente senza prefisso chiamando `AddEnvironmentVariables` senza prefisso.</span><span class="sxs-lookup"><span data-stu-id="3086e-687">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="3086e-688">Una configurazione facoltativa dai file *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="3086e-688">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="3086e-689">[Segreti utente (Secret Manager)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="3086e-689">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="3086e-690">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="3086e-690">Command-line arguments.</span></span>

<span data-ttu-id="3086e-691">Il provider di configurazione delle variabili di ambiente viene chiamato dopo aver stabilito la configurazione dai segreti utente e dai file *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="3086e-691">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="3086e-692">La chiamata del provider in questa posizione consente alle variabili di ambiente lette in fase di esecuzione di sostituire la configurazione impostata dai segreti utente e dai file *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="3086e-692">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="3086e-693">Per fornire la configurazione dell'app da altre variabili di ambiente, chiamare i provider aggiuntivi `ConfigureAppConfiguration` dell'app `AddEnvironmentVariables` in e chiamare con il prefisso:</span><span class="sxs-lookup"><span data-stu-id="3086e-693">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="3086e-694">Chiamare `AddEnvironmentVariables` Last per consentire alle variabili di ambiente con il prefisso specificato di eseguire l'override dei valori di altri provider.</span><span class="sxs-lookup"><span data-stu-id="3086e-694">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="3086e-695">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="3086e-695">**Example**</span></span>

<span data-ttu-id="3086e-696">L'app di esempio consente di sfruttare il metodo di servizio statico `CreateDefaultBuilder` per creare l'host, che include una chiamata a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="3086e-696">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="3086e-697">Eseguire l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="3086e-697">Run the sample app.</span></span> <span data-ttu-id="3086e-698">Aprire un browser per l'app all'indirizzo `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="3086e-698">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="3086e-699">Notare che l'output contiene la coppia chiave-valore per la variabile di ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="3086e-699">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="3086e-700">Il valore riflette l'ambiente in cui l'app è in esecuzione, in genere `Development` durante l'esecuzione locale.</span><span class="sxs-lookup"><span data-stu-id="3086e-700">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="3086e-701">Per limitare l'elenco delle variabili di ambiente restituito dall'app, l'app filtra le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="3086e-701">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="3086e-702">Vedere il file *Pages/Index.cshtml.cs* dell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="3086e-702">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="3086e-703">Per esporre tutte le variabili di ambiente disponibili per l'app, modificare `FilteredConfiguration` in *pages/index. cshtml. cs* come riportato di seguito:</span><span class="sxs-lookup"><span data-stu-id="3086e-703">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="3086e-704">Prefissi</span><span class="sxs-lookup"><span data-stu-id="3086e-704">Prefixes</span></span>

<span data-ttu-id="3086e-705">Le variabili di ambiente caricate nella configurazione dell'app vengono filtrate quando si specifica un prefisso `AddEnvironmentVariables` per il metodo.</span><span class="sxs-lookup"><span data-stu-id="3086e-705">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="3086e-706">Ad esempio, per filtrare le variabili di ambiente in base al prefisso `CUSTOM_`, fornire il prefisso al provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-706">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="3086e-707">Il prefisso viene rimosso quando vengono create le coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-707">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="3086e-708">Quando viene creato il generatore di host, la configurazione dell'host viene fornita dalle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="3086e-708">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="3086e-709">Per altre informazioni sul prefisso usato per queste variabili di ambiente, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="3086e-709">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="3086e-710">**Prefissi della stringa di connessione**</span><span class="sxs-lookup"><span data-stu-id="3086e-710">**Connection string prefixes**</span></span>

<span data-ttu-id="3086e-711">L'API di configurazione prevede regole di elaborazione speciali per quattro variabili di ambiente di stringa di connessione coinvolte nella configurazione di stringhe di connessione di Azure per l'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="3086e-711">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="3086e-712">Le variabili di ambiente con i prefissi indicati nella tabella vengono caricate nell'app se non viene fornito alcun prefisso a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="3086e-712">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="3086e-713">Prefisso della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="3086e-713">Connection string prefix</span></span> | <span data-ttu-id="3086e-714">Provider</span><span class="sxs-lookup"><span data-stu-id="3086e-714">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="3086e-715">Provider personalizzato</span><span class="sxs-lookup"><span data-stu-id="3086e-715">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="3086e-716">MySQL</span><span class="sxs-lookup"><span data-stu-id="3086e-716">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="3086e-717">Database SQL di Azure</span><span class="sxs-lookup"><span data-stu-id="3086e-717">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="3086e-718">SQL Server</span><span class="sxs-lookup"><span data-stu-id="3086e-718">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="3086e-719">Quando una variabile di ambiente viene individuata e caricata nella configurazione con uno qualsiasi dei quattro prefissi indicati nella tabella:</span><span class="sxs-lookup"><span data-stu-id="3086e-719">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="3086e-720">La chiave di configurazione viene creata rimuovendo il prefisso della variabile di ambiente e aggiungendo una sezione per la chiave di configurazione (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="3086e-720">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="3086e-721">Viene creata una nuova coppia chiave-valore della configurazione che rappresenta il provider di connessione al database (ad eccezione di `CUSTOMCONNSTR_`, che non ha un provider dichiarato).</span><span class="sxs-lookup"><span data-stu-id="3086e-721">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="3086e-722">Chiave della variabile di ambiente</span><span class="sxs-lookup"><span data-stu-id="3086e-722">Environment variable key</span></span> | <span data-ttu-id="3086e-723">Chiave di configurazione convertita</span><span class="sxs-lookup"><span data-stu-id="3086e-723">Converted configuration key</span></span> | <span data-ttu-id="3086e-724">Voce di configurazione del provider</span><span class="sxs-lookup"><span data-stu-id="3086e-724">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="3086e-725">Voce di configurazione non creata.</span><span class="sxs-lookup"><span data-stu-id="3086e-725">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="3086e-726">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="3086e-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="3086e-727">Valore: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="3086e-727">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="3086e-728">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="3086e-728">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="3086e-729">Valore: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="3086e-729">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="3086e-730">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="3086e-730">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="3086e-731">Valore: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="3086e-731">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="3086e-732">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="3086e-732">**Example**</span></span>

<span data-ttu-id="3086e-733">Nel server viene creata una variabile di ambiente della stringa di connessione personalizzata:</span><span class="sxs-lookup"><span data-stu-id="3086e-733">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="3086e-734">Nome &ndash;`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="3086e-734">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="3086e-735">Valore &ndash; di`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="3086e-735">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="3086e-736">Se `IConfiguration` viene inserito e assegnato a un campo denominato `_config`, leggere il valore:</span><span class="sxs-lookup"><span data-stu-id="3086e-736">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="3086e-737">Provider di configurazione dei file</span><span class="sxs-lookup"><span data-stu-id="3086e-737">File Configuration Provider</span></span>

<span data-ttu-id="3086e-738"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> è la classe base per il caricamento della configurazione dal file system.</span><span class="sxs-lookup"><span data-stu-id="3086e-738"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="3086e-739">I provider di configurazione seguenti sono dedicati per specifici tipi di file:</span><span class="sxs-lookup"><span data-stu-id="3086e-739">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="3086e-740">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="3086e-740">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="3086e-741">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="3086e-741">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="3086e-742">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="3086e-742">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="3086e-743">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="3086e-743">INI Configuration Provider</span></span>

<span data-ttu-id="3086e-744"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carica la configurazione da coppie chiave-valore di file INI in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="3086e-744">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="3086e-745">Per attivare la configurazione di file INI, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="3086e-745">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="3086e-746">I due punti possono essere usati come delimitatore di sezione nella configurazione di file INI.</span><span class="sxs-lookup"><span data-stu-id="3086e-746">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="3086e-747">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="3086e-747">Overloads permit specifying:</span></span>

* <span data-ttu-id="3086e-748">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="3086e-748">Whether the file is optional.</span></span>
* <span data-ttu-id="3086e-749">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="3086e-749">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="3086e-750">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider> usato per accedere al file.</span><span class="sxs-lookup"><span data-stu-id="3086e-750">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="3086e-751">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="3086e-751">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="3086e-752">Un esempio generico di un file di configurazione INI:</span><span class="sxs-lookup"><span data-stu-id="3086e-752">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="3086e-753">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="3086e-753">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="3086e-754">section0:key0</span><span class="sxs-lookup"><span data-stu-id="3086e-754">section0:key0</span></span>
* <span data-ttu-id="3086e-755">section0:key1</span><span class="sxs-lookup"><span data-stu-id="3086e-755">section0:key1</span></span>
* <span data-ttu-id="3086e-756">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="3086e-756">section1:subsection:key</span></span>
* <span data-ttu-id="3086e-757">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="3086e-757">section2:subsection0:key</span></span>
* <span data-ttu-id="3086e-758">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="3086e-758">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="3086e-759">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="3086e-759">JSON Configuration Provider</span></span>

<span data-ttu-id="3086e-760">Il <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carica la configurazione da coppie chiave-valore di file JSON in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="3086e-760">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="3086e-761">Per attivare la configurazione di file JSON, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="3086e-761">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="3086e-762">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="3086e-762">Overloads permit specifying:</span></span>

* <span data-ttu-id="3086e-763">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="3086e-763">Whether the file is optional.</span></span>
* <span data-ttu-id="3086e-764">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="3086e-764">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="3086e-765">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider> usato per accedere al file.</span><span class="sxs-lookup"><span data-stu-id="3086e-765">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="3086e-766">`AddJsonFile`viene chiamato automaticamente due volte quando viene inizializzato un nuovo generatore `CreateDefaultBuilder`host con.</span><span class="sxs-lookup"><span data-stu-id="3086e-766">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="3086e-767">Il metodo viene chiamato per caricare la configurazione da:</span><span class="sxs-lookup"><span data-stu-id="3086e-767">The method is called to load configuration from:</span></span>

* <span data-ttu-id="3086e-768">*appsettings.json* &ndash; Questo file viene letto per primo.</span><span class="sxs-lookup"><span data-stu-id="3086e-768">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="3086e-769">La versione dell'ambiente del file può sostituire i valori forniti dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3086e-769">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="3086e-770">*appSettings. {Environment}. JSON* &ndash; la versione dell'ambiente del file viene caricata in base a [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="3086e-770">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="3086e-771">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="3086e-771">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="3086e-772">`CreateDefaultBuilder` carica anche:</span><span class="sxs-lookup"><span data-stu-id="3086e-772">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="3086e-773">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="3086e-773">Environment variables.</span></span>
* <span data-ttu-id="3086e-774">[Segreti utente (Secret Manager)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="3086e-774">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="3086e-775">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="3086e-775">Command-line arguments.</span></span>

<span data-ttu-id="3086e-776">Il provider di configurazione JSON viene stabilito per primo.</span><span class="sxs-lookup"><span data-stu-id="3086e-776">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="3086e-777">I segreti utente, le variabili di ambiente e gli argomenti della riga di comando sostituiscono quindi la configurazione impostata dai file *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="3086e-777">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="3086e-778">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app per file diversi da *appsettings.json* e *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="3086e-778">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="3086e-779">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="3086e-779">**Example**</span></span>

<span data-ttu-id="3086e-780">L'app di esempio sfrutta il metodo `CreateDefaultBuilder` di convenienza statica per compilare l'host, che include due chiamate a `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="3086e-780">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="3086e-781">La prima chiamata a `AddJsonFile` carica la configurazione da *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3086e-781">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="3086e-782">La seconda chiamata a `AddJsonFile` carica la configurazione da *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="3086e-782">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="3086e-783">Per *appSettings. Development. JSON* nell'app di esempio, viene caricato il file seguente:</span><span class="sxs-lookup"><span data-stu-id="3086e-783">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="3086e-784">Eseguire l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="3086e-784">Run the sample app.</span></span> <span data-ttu-id="3086e-785">Aprire un browser per l'app all'indirizzo `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="3086e-785">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="3086e-786">L'output contiene coppie chiave-valore per la configurazione basata sull'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="3086e-786">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="3086e-787">Il livello di registrazione della chiave `Logging:LogLevel:Default` è `Debug` quando si esegue l'app nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="3086e-787">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="3086e-788">Eseguire di nuovo l'app di esempio nell'ambiente di produzione:</span><span class="sxs-lookup"><span data-stu-id="3086e-788">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="3086e-789">Aprire il file *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="3086e-789">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="3086e-790">Nel `ConfigurationSample` profilo, modificare il valore della variabile di `ASPNETCORE_ENVIRONMENT` ambiente in `Production`.</span><span class="sxs-lookup"><span data-stu-id="3086e-790">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="3086e-791">Salvare il file ed eseguire l'app con `dotnet run` in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="3086e-791">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="3086e-792">Impostazioni in *appSettings. Development. JSON* non sostituisce più le impostazioni in *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="3086e-792">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="3086e-793">Il livello di registrazione per la `Logging:LogLevel:Default` chiave `Warning`è.</span><span class="sxs-lookup"><span data-stu-id="3086e-793">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="3086e-794">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="3086e-794">XML Configuration Provider</span></span>

<span data-ttu-id="3086e-795">Il <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carica la configurazione da coppie chiave-valore di file XML in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="3086e-795">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="3086e-796">Per attivare la configurazione di file XML, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="3086e-796">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="3086e-797">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="3086e-797">Overloads permit specifying:</span></span>

* <span data-ttu-id="3086e-798">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="3086e-798">Whether the file is optional.</span></span>
* <span data-ttu-id="3086e-799">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="3086e-799">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="3086e-800">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider> usato per accedere al file.</span><span class="sxs-lookup"><span data-stu-id="3086e-800">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="3086e-801">Il nodo radice del file di configurazione viene ignorato quando vengono create le coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-801">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="3086e-802">Non specificare una definizione DTD (Document Type Definition) o uno spazio dei nomi nel file.</span><span class="sxs-lookup"><span data-stu-id="3086e-802">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="3086e-803">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="3086e-803">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="3086e-804">I file di configurazione XML possono usare nomi di elementi distinti per le sezioni ripetute:</span><span class="sxs-lookup"><span data-stu-id="3086e-804">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="3086e-805">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="3086e-805">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="3086e-806">section0:key0</span><span class="sxs-lookup"><span data-stu-id="3086e-806">section0:key0</span></span>
* <span data-ttu-id="3086e-807">section0:key1</span><span class="sxs-lookup"><span data-stu-id="3086e-807">section0:key1</span></span>
* <span data-ttu-id="3086e-808">section1:key0</span><span class="sxs-lookup"><span data-stu-id="3086e-808">section1:key0</span></span>
* <span data-ttu-id="3086e-809">section1:key1</span><span class="sxs-lookup"><span data-stu-id="3086e-809">section1:key1</span></span>

<span data-ttu-id="3086e-810">La ripetizione di elementi che usano lo stesso nome di elemento funziona se si usa l'attributo `name` per distinguere gli elementi:</span><span class="sxs-lookup"><span data-stu-id="3086e-810">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="3086e-811">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="3086e-811">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="3086e-812">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="3086e-812">section:section0:key:key0</span></span>
* <span data-ttu-id="3086e-813">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="3086e-813">section:section0:key:key1</span></span>
* <span data-ttu-id="3086e-814">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="3086e-814">section:section1:key:key0</span></span>
* <span data-ttu-id="3086e-815">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="3086e-815">section:section1:key:key1</span></span>

<span data-ttu-id="3086e-816">È possibile usare attributi per fornire valori:</span><span class="sxs-lookup"><span data-stu-id="3086e-816">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="3086e-817">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="3086e-817">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="3086e-818">key:attribute</span><span class="sxs-lookup"><span data-stu-id="3086e-818">key:attribute</span></span>
* <span data-ttu-id="3086e-819">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="3086e-819">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="3086e-820">Provider di configurazione KeyPerFile</span><span class="sxs-lookup"><span data-stu-id="3086e-820">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="3086e-821">Il <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa i file di una directory come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-821">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="3086e-822">La chiave è il nome del file.</span><span class="sxs-lookup"><span data-stu-id="3086e-822">The key is the file name.</span></span> <span data-ttu-id="3086e-823">Il valore contiene il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="3086e-823">The value contains the file's contents.</span></span> <span data-ttu-id="3086e-824">Il provider di configurazione KeyPerFile viene usato in scenari di hosting Docker.</span><span class="sxs-lookup"><span data-stu-id="3086e-824">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="3086e-825">Per attivare la configurazione chiave-per-file, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="3086e-825">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="3086e-826">Il `directoryPath` per i file deve essere un percorso assoluto.</span><span class="sxs-lookup"><span data-stu-id="3086e-826">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="3086e-827">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="3086e-827">Overloads permit specifying:</span></span>

* <span data-ttu-id="3086e-828">Un delegato `Action<KeyPerFileConfigurationSource>` che configura l'origine.</span><span class="sxs-lookup"><span data-stu-id="3086e-828">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="3086e-829">Se la directory è facoltativa e il percorso della directory.</span><span class="sxs-lookup"><span data-stu-id="3086e-829">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="3086e-830">Il doppio carattere di sottolineatura (`__`) viene usato come delimitatore per le chiavi di configurazione nei nomi dei file.</span><span class="sxs-lookup"><span data-stu-id="3086e-830">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="3086e-831">Ad esempio, il nome di file `Logging__LogLevel__System` produce la chiave di configurazione `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="3086e-831">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="3086e-832">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="3086e-832">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="3086e-833">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="3086e-833">Memory Configuration Provider</span></span>

<span data-ttu-id="3086e-834">Il <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una raccolta in memoria come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-834">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="3086e-835">Per attivare la configurazione della raccolta in memoria, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="3086e-835">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="3086e-836">Il provider di configurazione può essere inizializzato con un `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="3086e-836">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="3086e-837">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="3086e-837">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="3086e-838">Nell'esempio seguente viene creato un dizionario di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-838">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="3086e-839">Il dizionario viene usato con una chiamata a `AddInMemoryCollection` per fornire la configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-839">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="3086e-840">GetValue</span><span class="sxs-lookup"><span data-stu-id="3086e-840">GetValue</span></span>

<span data-ttu-id="3086e-841">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)estrae un singolo valore dalla configurazione con una chiave specificata e lo converte nel tipo non di raccolta specificato.</span><span class="sxs-lookup"><span data-stu-id="3086e-841">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="3086e-842">Un overload accetta un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="3086e-842">An overload accepts a default value.</span></span>

<span data-ttu-id="3086e-843">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="3086e-843">The following example:</span></span>

* <span data-ttu-id="3086e-844">Estrae il valore di stringa dalla configurazione con la chiave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="3086e-844">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="3086e-845">Se `NumberKey` non viene trovato nelle chiavi di configurazione, viene usato il valore predefinito di `99`.</span><span class="sxs-lookup"><span data-stu-id="3086e-845">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="3086e-846">Assegna al valore il tipo `int`.</span><span class="sxs-lookup"><span data-stu-id="3086e-846">Types the value as an `int`.</span></span>
* <span data-ttu-id="3086e-847">Memorizza il valore nella proprietà `NumberConfig` per l'uso da parte della pagina.</span><span class="sxs-lookup"><span data-stu-id="3086e-847">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="3086e-848">GetSection, GetChildren ed Exists</span><span class="sxs-lookup"><span data-stu-id="3086e-848">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="3086e-849">Per gli esempi che seguono, prendere in considerazione il file JSON seguente.</span><span class="sxs-lookup"><span data-stu-id="3086e-849">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="3086e-850">Vengono trovate quattro chiavi in due sezioni, una delle quali include una coppia di sottosezioni:</span><span class="sxs-lookup"><span data-stu-id="3086e-850">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="3086e-851">Quando il file viene letto nella configurazione, vengono create le chiavi gerarchiche univoche seguenti per contenere i valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-851">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="3086e-852">section0:key0</span><span class="sxs-lookup"><span data-stu-id="3086e-852">section0:key0</span></span>
* <span data-ttu-id="3086e-853">section0:key1</span><span class="sxs-lookup"><span data-stu-id="3086e-853">section0:key1</span></span>
* <span data-ttu-id="3086e-854">section1:key0</span><span class="sxs-lookup"><span data-stu-id="3086e-854">section1:key0</span></span>
* <span data-ttu-id="3086e-855">section1:key1</span><span class="sxs-lookup"><span data-stu-id="3086e-855">section1:key1</span></span>
* <span data-ttu-id="3086e-856">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="3086e-856">section2:subsection0:key0</span></span>
* <span data-ttu-id="3086e-857">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="3086e-857">section2:subsection0:key1</span></span>
* <span data-ttu-id="3086e-858">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="3086e-858">section2:subsection1:key0</span></span>
* <span data-ttu-id="3086e-859">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="3086e-859">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="3086e-860">GetSection</span><span class="sxs-lookup"><span data-stu-id="3086e-860">GetSection</span></span>

<span data-ttu-id="3086e-861">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) estrae una sottosezione della configurazione con la chiave di sottosezione specificata.</span><span class="sxs-lookup"><span data-stu-id="3086e-861">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="3086e-862">Per restituire una <xref:Microsoft.Extensions.Configuration.IConfigurationSection> che contiene solo le coppie chiave-valore in `section1`, chiamare `GetSection` e fornire il nome della sezione:</span><span class="sxs-lookup"><span data-stu-id="3086e-862">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="3086e-863">`configSection` non ha un valore, ma solo una chiave e un percorso.</span><span class="sxs-lookup"><span data-stu-id="3086e-863">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="3086e-864">In modo analogo, per ottenere i valori per le chiavi in `section2:subsection0`, chiamare `GetSection` e fornire il percorso della sezione:</span><span class="sxs-lookup"><span data-stu-id="3086e-864">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="3086e-865">`GetSection` non restituisce mai `null`.</span><span class="sxs-lookup"><span data-stu-id="3086e-865">`GetSection` never returns `null`.</span></span> <span data-ttu-id="3086e-866">Se non viene trovata una sezione corrispondente, viene restituita una `IConfigurationSection` vuota.</span><span class="sxs-lookup"><span data-stu-id="3086e-866">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="3086e-867">Quando `GetSection` restituisce una sezione corrispondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> non viene compilata.</span><span class="sxs-lookup"><span data-stu-id="3086e-867">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="3086e-868">Quando la sezione esiste, vengono restituiti <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.</span><span class="sxs-lookup"><span data-stu-id="3086e-868">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="3086e-869">GetChildren</span><span class="sxs-lookup"><span data-stu-id="3086e-869">GetChildren</span></span>

<span data-ttu-id="3086e-870">Una chiamata a [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) per `section2` ottiene una `IEnumerable<IConfigurationSection>` che include:</span><span class="sxs-lookup"><span data-stu-id="3086e-870">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="3086e-871">Exists</span><span class="sxs-lookup"><span data-stu-id="3086e-871">Exists</span></span>

<span data-ttu-id="3086e-872">Usare [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) per determinare se esiste una sezione di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-872">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="3086e-873">Considerati i dati di esempio, `sectionExists` è `false` perché non esiste una sezione `section2:subsection2` nei dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-873">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="3086e-874">Associazione a un oggetto grafico</span><span class="sxs-lookup"><span data-stu-id="3086e-874">Bind to an object graph</span></span>

<span data-ttu-id="3086e-875"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> è in grado di associare un intero oggetto grafico POCO.</span><span class="sxs-lookup"><span data-stu-id="3086e-875"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="3086e-876">Come per l'associazione di un oggetto semplice, vengono associate solo le proprietà di lettura/scrittura pubbliche.</span><span class="sxs-lookup"><span data-stu-id="3086e-876">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="3086e-877">L'esempio contiene un modello `TvShow` il cui oggetto grafico include `Metadata` e le classi `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="3086e-877">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="3086e-878">L'app di esempio ha un file *tvshow.xml* che contiene i dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-878">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="3086e-879">La configurazione viene associata all'intero oggetto grafico `TvShow` con il metodo `Bind`.</span><span class="sxs-lookup"><span data-stu-id="3086e-879">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="3086e-880">L'istanza associata viene assegnata a una proprietà per il rendering:</span><span class="sxs-lookup"><span data-stu-id="3086e-880">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="3086e-881">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)associa e restituisce il tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="3086e-881">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="3086e-882">`Get<T>` può essere più comodo che usare `Bind`.</span><span class="sxs-lookup"><span data-stu-id="3086e-882">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="3086e-883">Il codice seguente illustra come usare `Get<T>` con l'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="3086e-883">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="3086e-884">Associare una matrice a una classe</span><span class="sxs-lookup"><span data-stu-id="3086e-884">Bind an array to a class</span></span>

<span data-ttu-id="3086e-885">*L'app di esempio dimostra i concetti spiegati in questa sezione.*</span><span class="sxs-lookup"><span data-stu-id="3086e-885">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="3086e-886">Il <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supporta l'associazione di matrici agli oggetti usando gli indici delle matrici nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-886">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="3086e-887">Qualsiasi formato di matrice che espone un segmento di chiave`:0:`numerico `:1:`( &hellip; `:{n}:`,,) è in grado di associare array a una matrice di classi poco.</span><span class="sxs-lookup"><span data-stu-id="3086e-887">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="3086e-888">L'associazione viene fornita per convenzione.</span><span class="sxs-lookup"><span data-stu-id="3086e-888">Binding is provided by convention.</span></span> <span data-ttu-id="3086e-889">I provider di configurazione personalizzati non devono implementare l'associazione di matrici.</span><span class="sxs-lookup"><span data-stu-id="3086e-889">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="3086e-890">**Elaborazione di matrici in memoria**</span><span class="sxs-lookup"><span data-stu-id="3086e-890">**In-memory array processing**</span></span>

<span data-ttu-id="3086e-891">Prendere in considerazione le chiavi di configurazione e i valori indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="3086e-891">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="3086e-892">Chiave</span><span class="sxs-lookup"><span data-stu-id="3086e-892">Key</span></span>             | <span data-ttu-id="3086e-893">Valore</span><span class="sxs-lookup"><span data-stu-id="3086e-893">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="3086e-894">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="3086e-894">array:entries:0</span></span> | <span data-ttu-id="3086e-895">value0</span><span class="sxs-lookup"><span data-stu-id="3086e-895">value0</span></span> |
| <span data-ttu-id="3086e-896">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="3086e-896">array:entries:1</span></span> | <span data-ttu-id="3086e-897">value1</span><span class="sxs-lookup"><span data-stu-id="3086e-897">value1</span></span> |
| <span data-ttu-id="3086e-898">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="3086e-898">array:entries:2</span></span> | <span data-ttu-id="3086e-899">value2</span><span class="sxs-lookup"><span data-stu-id="3086e-899">value2</span></span> |
| <span data-ttu-id="3086e-900">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="3086e-900">array:entries:4</span></span> | <span data-ttu-id="3086e-901">value4</span><span class="sxs-lookup"><span data-stu-id="3086e-901">value4</span></span> |
| <span data-ttu-id="3086e-902">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="3086e-902">array:entries:5</span></span> | <span data-ttu-id="3086e-903">value5</span><span class="sxs-lookup"><span data-stu-id="3086e-903">value5</span></span> |

<span data-ttu-id="3086e-904">Queste chiavi e i valori vengono caricati nell'app di esempio usando il provider di configurazione della memoria:</span><span class="sxs-lookup"><span data-stu-id="3086e-904">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="3086e-905">La matrice ignora un valore per l'indice &num;3.</span><span class="sxs-lookup"><span data-stu-id="3086e-905">The array skips a value for index &num;3.</span></span> <span data-ttu-id="3086e-906">Lo strumento di associazione di configurazione non è in grado di associare valori null o di creare voci null negli oggetti associati, situazione che diventerà chiara tra poco quando viene illustrato il risultato dell'associazione di questa matrice a un oggetto.</span><span class="sxs-lookup"><span data-stu-id="3086e-906">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="3086e-907">Nell'app di esempio, è disponibile una classe POCO per i dati di configurazione associati:</span><span class="sxs-lookup"><span data-stu-id="3086e-907">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="3086e-908">I dati di configurazione sono associati all'oggetto:</span><span class="sxs-lookup"><span data-stu-id="3086e-908">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="3086e-909">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)è anche possibile usare la sintassi, che comporta un codice più compatto:</span><span class="sxs-lookup"><span data-stu-id="3086e-909">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="3086e-910">L'oggetto associato, un'istanza di `ArrayExample`, riceve i dati di matrice dalla configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-910">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="3086e-911">Indice di `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="3086e-911">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="3086e-912">`ArrayExample.Entries` Valore</span><span class="sxs-lookup"><span data-stu-id="3086e-912">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="3086e-913">0</span><span class="sxs-lookup"><span data-stu-id="3086e-913">0</span></span>                            | <span data-ttu-id="3086e-914">value0</span><span class="sxs-lookup"><span data-stu-id="3086e-914">value0</span></span>                       |
| <span data-ttu-id="3086e-915">1</span><span class="sxs-lookup"><span data-stu-id="3086e-915">1</span></span>                            | <span data-ttu-id="3086e-916">value1</span><span class="sxs-lookup"><span data-stu-id="3086e-916">value1</span></span>                       |
| <span data-ttu-id="3086e-917">2</span><span class="sxs-lookup"><span data-stu-id="3086e-917">2</span></span>                            | <span data-ttu-id="3086e-918">value2</span><span class="sxs-lookup"><span data-stu-id="3086e-918">value2</span></span>                       |
| <span data-ttu-id="3086e-919">3</span><span class="sxs-lookup"><span data-stu-id="3086e-919">3</span></span>                            | <span data-ttu-id="3086e-920">value4</span><span class="sxs-lookup"><span data-stu-id="3086e-920">value4</span></span>                       |
| <span data-ttu-id="3086e-921">4</span><span class="sxs-lookup"><span data-stu-id="3086e-921">4</span></span>                            | <span data-ttu-id="3086e-922">value5</span><span class="sxs-lookup"><span data-stu-id="3086e-922">value5</span></span>                       |

<span data-ttu-id="3086e-923">L'indice &num;3 nell'oggetto associato contiene i dati di configurazione per la chiave di configurazione `array:4` e il relativo valore `value4`.</span><span class="sxs-lookup"><span data-stu-id="3086e-923">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="3086e-924">Quando i dati di configurazione contenenti una matrice vengono associati, gli indici di matrice nelle chiavi di configurazione vengono usati semplicemente per scorrere i dati di configurazione quando si crea l'oggetto.</span><span class="sxs-lookup"><span data-stu-id="3086e-924">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="3086e-925">Un valore null non può essere mantenuto nei dati di configurazione e una voce con valore null non viene creata in un oggetto associato quando una matrice nelle chiavi di configurazione ignora uno o più indici.</span><span class="sxs-lookup"><span data-stu-id="3086e-925">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="3086e-926">L'elemento di configurazione mancante per l'indice &num;3 può essere fornito prima dell'associazione all'istanza `ArrayExample` da qualsiasi provider di configurazione che produce la coppia chiave-valore corretta nella configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-926">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="3086e-927">Se il codice di esempio include un provider di configurazione JSON aggiuntivo con la coppia chiave-valore mancante, `ArrayExample.Entries` corrisponde alla matrice di configurazione completa:</span><span class="sxs-lookup"><span data-stu-id="3086e-927">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="3086e-928">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="3086e-928">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="3086e-929">In `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="3086e-929">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="3086e-930">La coppia chiave-valore mostrata nella tabella viene caricata nella configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-930">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="3086e-931">Chiave</span><span class="sxs-lookup"><span data-stu-id="3086e-931">Key</span></span>             | <span data-ttu-id="3086e-932">Valore</span><span class="sxs-lookup"><span data-stu-id="3086e-932">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="3086e-933">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="3086e-933">array:entries:3</span></span> | <span data-ttu-id="3086e-934">value3</span><span class="sxs-lookup"><span data-stu-id="3086e-934">value3</span></span> |

<span data-ttu-id="3086e-935">Se l'istanza della classe `ArrayExample` viene associata dopo che il provider di configurazione JSON include la voce per l'indice &num;3, la matrice `ArrayExample.Entries` include il valore.</span><span class="sxs-lookup"><span data-stu-id="3086e-935">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="3086e-936">Indice di `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="3086e-936">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="3086e-937">`ArrayExample.Entries` Valore</span><span class="sxs-lookup"><span data-stu-id="3086e-937">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="3086e-938">0</span><span class="sxs-lookup"><span data-stu-id="3086e-938">0</span></span>                            | <span data-ttu-id="3086e-939">value0</span><span class="sxs-lookup"><span data-stu-id="3086e-939">value0</span></span>                       |
| <span data-ttu-id="3086e-940">1</span><span class="sxs-lookup"><span data-stu-id="3086e-940">1</span></span>                            | <span data-ttu-id="3086e-941">value1</span><span class="sxs-lookup"><span data-stu-id="3086e-941">value1</span></span>                       |
| <span data-ttu-id="3086e-942">2</span><span class="sxs-lookup"><span data-stu-id="3086e-942">2</span></span>                            | <span data-ttu-id="3086e-943">value2</span><span class="sxs-lookup"><span data-stu-id="3086e-943">value2</span></span>                       |
| <span data-ttu-id="3086e-944">3</span><span class="sxs-lookup"><span data-stu-id="3086e-944">3</span></span>                            | <span data-ttu-id="3086e-945">value3</span><span class="sxs-lookup"><span data-stu-id="3086e-945">value3</span></span>                       |
| <span data-ttu-id="3086e-946">4</span><span class="sxs-lookup"><span data-stu-id="3086e-946">4</span></span>                            | <span data-ttu-id="3086e-947">value4</span><span class="sxs-lookup"><span data-stu-id="3086e-947">value4</span></span>                       |
| <span data-ttu-id="3086e-948">5</span><span class="sxs-lookup"><span data-stu-id="3086e-948">5</span></span>                            | <span data-ttu-id="3086e-949">value5</span><span class="sxs-lookup"><span data-stu-id="3086e-949">value5</span></span>                       |

<span data-ttu-id="3086e-950">**Elaborazione di matrice JSON**</span><span class="sxs-lookup"><span data-stu-id="3086e-950">**JSON array processing**</span></span>

<span data-ttu-id="3086e-951">Se un file JSON contiene una matrice, vengono create chiavi di configurazione per gli elementi della matrice con un indice di sezione in base zero.</span><span class="sxs-lookup"><span data-stu-id="3086e-951">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="3086e-952">Nel file di configurazione seguente, `subsection` è una matrice:</span><span class="sxs-lookup"><span data-stu-id="3086e-952">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="3086e-953">Il provider di configurazione JSON legge i dati di configurazione nelle coppie chiave-valore seguenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-953">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="3086e-954">Chiave</span><span class="sxs-lookup"><span data-stu-id="3086e-954">Key</span></span>                     | <span data-ttu-id="3086e-955">Valore</span><span class="sxs-lookup"><span data-stu-id="3086e-955">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="3086e-956">json_array:key</span><span class="sxs-lookup"><span data-stu-id="3086e-956">json_array:key</span></span>          | <span data-ttu-id="3086e-957">valueA</span><span class="sxs-lookup"><span data-stu-id="3086e-957">valueA</span></span> |
| <span data-ttu-id="3086e-958">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="3086e-958">json_array:subsection:0</span></span> | <span data-ttu-id="3086e-959">valueB</span><span class="sxs-lookup"><span data-stu-id="3086e-959">valueB</span></span> |
| <span data-ttu-id="3086e-960">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="3086e-960">json_array:subsection:1</span></span> | <span data-ttu-id="3086e-961">valueC</span><span class="sxs-lookup"><span data-stu-id="3086e-961">valueC</span></span> |
| <span data-ttu-id="3086e-962">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="3086e-962">json_array:subsection:2</span></span> | <span data-ttu-id="3086e-963">valueD</span><span class="sxs-lookup"><span data-stu-id="3086e-963">valueD</span></span> |

<span data-ttu-id="3086e-964">Nell'app di esempio, la classe POCO seguente è disponibile per associare le coppie chiave-valore della configurazione:</span><span class="sxs-lookup"><span data-stu-id="3086e-964">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="3086e-965">Dopo l'associazione, `JsonArrayExample.Key` contiene il valore `valueA`.</span><span class="sxs-lookup"><span data-stu-id="3086e-965">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="3086e-966">I valori di sottosezione vengono archiviati nella proprietà matrice POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="3086e-966">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="3086e-967">Indice di `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="3086e-967">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="3086e-968">`JsonArrayExample.Subsection` Valore</span><span class="sxs-lookup"><span data-stu-id="3086e-968">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="3086e-969">0</span><span class="sxs-lookup"><span data-stu-id="3086e-969">0</span></span>                                   | <span data-ttu-id="3086e-970">valueB</span><span class="sxs-lookup"><span data-stu-id="3086e-970">valueB</span></span>                              |
| <span data-ttu-id="3086e-971">1</span><span class="sxs-lookup"><span data-stu-id="3086e-971">1</span></span>                                   | <span data-ttu-id="3086e-972">valueC</span><span class="sxs-lookup"><span data-stu-id="3086e-972">valueC</span></span>                              |
| <span data-ttu-id="3086e-973">2</span><span class="sxs-lookup"><span data-stu-id="3086e-973">2</span></span>                                   | <span data-ttu-id="3086e-974">valueD</span><span class="sxs-lookup"><span data-stu-id="3086e-974">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="3086e-975">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="3086e-975">Custom configuration provider</span></span>

<span data-ttu-id="3086e-976">L'app di esempio dimostra come creare un provider di configurazione di base che legge le coppie chiave-valore di configurazione da un database usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="3086e-976">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="3086e-977">Il provider ha le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="3086e-977">The provider has the following characteristics:</span></span>

* <span data-ttu-id="3086e-978">Il database in memoria di Entity Framework viene usato a scopo dimostrativo.</span><span class="sxs-lookup"><span data-stu-id="3086e-978">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="3086e-979">Per usare un database che richiede una stringa di connessione, implementare un `ConfigurationBuilder` secondario per fornire la stringa di connessione da un altro provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="3086e-979">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="3086e-980">Il provider legge una tabella di database in una configurazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="3086e-980">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="3086e-981">Il provider non esegue una query sul database per ogni chiave.</span><span class="sxs-lookup"><span data-stu-id="3086e-981">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="3086e-982">Il ricaricamento in caso di modifica non è implementato, quindi l'aggiornamento del database dopo l'avvio dell'app non ha alcun effetto sulla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="3086e-982">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="3086e-983">Definire un'entità `EFConfigurationValue` per l'archiviazione dei valori di configurazione nel database.</span><span class="sxs-lookup"><span data-stu-id="3086e-983">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="3086e-984">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="3086e-984">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="3086e-985">Aggiungere `EFConfigurationContext` per archiviare i valori configurati e accedervi.</span><span class="sxs-lookup"><span data-stu-id="3086e-985">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="3086e-986">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="3086e-986">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="3086e-987">Creare una classe che implementi <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="3086e-987">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="3086e-988">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="3086e-988">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="3086e-989">Creare il provider di configurazione personalizzato ereditando da <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="3086e-989">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="3086e-990">Il provider di configurazione inizializza il database quando è vuoto.</span><span class="sxs-lookup"><span data-stu-id="3086e-990">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="3086e-991">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="3086e-991">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="3086e-992">Un metodo di estensione `AddEFConfiguration` consente di aggiungere l'origine di configurazione a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3086e-992">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="3086e-993">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="3086e-993">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="3086e-994">L'esempio di codice seguente mostra come usare il `EFConfigurationProvider` personalizzato in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="3086e-994">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="3086e-995">Accedere alla configurazione durante l'avvio</span><span class="sxs-lookup"><span data-stu-id="3086e-995">Access configuration during startup</span></span>

<span data-ttu-id="3086e-996">Inserire `IConfiguration` nel costruttore `Startup` per accedere ai valori di configurazione in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3086e-996">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3086e-997">Per accedere alla configurazione in `Startup.Configure`, inserire `IConfiguration` direttamente nel metodo o usare l'istanza dal costruttore:</span><span class="sxs-lookup"><span data-stu-id="3086e-997">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="3086e-998">Per un esempio di accesso alla configurazione usando metodi di servizio di avvio, vedere [Avvio dell'applicazione: Metodi pratici](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="3086e-998">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="3086e-999">Accedere alla configurazione in Razor una pagina di pagine o in una visualizzazione MVC</span><span class="sxs-lookup"><span data-stu-id="3086e-999">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="3086e-1000">Per accedere alle impostazioni di configurazione Razor in una pagina di pagine o in una visualizzazione MVC, aggiungere una [direttiva using](xref:mvc/views/razor#using) ([riferimenti per C#: direttiva using](/dotnet/csharp/language-reference/keywords/using-directive)) per lo [spazio dei nomi Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) e inserire <xref:Microsoft.Extensions.Configuration.IConfiguration> nella pagina o nella vista.</span><span class="sxs-lookup"><span data-stu-id="3086e-1000">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="3086e-1001">In una Razor pagina di pagine:</span><span class="sxs-lookup"><span data-stu-id="3086e-1001">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="3086e-1002">In una visualizzazione MVC:</span><span class="sxs-lookup"><span data-stu-id="3086e-1002">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="3086e-1003">Aggiungere la configurazione da un assembly esterno</span><span class="sxs-lookup"><span data-stu-id="3086e-1003">Add configuration from an external assembly</span></span>

<span data-ttu-id="3086e-1004">Un'implementazione <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> consente l'aggiunta di miglioramenti a un'app all'avvio, da un assembly esterno alla classe `Startup` dell'app.</span><span class="sxs-lookup"><span data-stu-id="3086e-1004">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="3086e-1005">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3086e-1005">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3086e-1006">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3086e-1006">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
