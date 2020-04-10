---
title: Configurazione in ASP.NET Core
author: rick-anderson
description: Informazioni su come usare l'API di configurazione per configurare un'app ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: 506f01ace72d6e915c0f3ebdaae5b4a3328a79b9
ms.sourcegitcommit: e72a58d6ebde8604badd254daae8077628f9d63e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81007158"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="1e489-103">Configurazione in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1e489-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="1e489-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e Kirk [Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="1e489-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1e489-105">La configurazione in ASP.NET Core viene eseguita utilizzando uno o più provider di [configurazione.](#cp)</span><span class="sxs-lookup"><span data-stu-id="1e489-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="1e489-106">I provider di configurazione leggono i dati di configurazione da coppie chiave-valore usando diverse origini di configurazione:Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span><span class="sxs-lookup"><span data-stu-id="1e489-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="1e489-107">File di impostazioni, ad esempio *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="1e489-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="1e489-108">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="1e489-108">Environment variables</span></span>
* <span data-ttu-id="1e489-109">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="1e489-109">Azure Key Vault</span></span>
* <span data-ttu-id="1e489-110">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="1e489-110">Azure App Configuration</span></span>
* <span data-ttu-id="1e489-111">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="1e489-111">Command-line arguments</span></span>
* <span data-ttu-id="1e489-112">Provider personalizzati, installati o creati</span><span class="sxs-lookup"><span data-stu-id="1e489-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="1e489-113">File della directory</span><span class="sxs-lookup"><span data-stu-id="1e489-113">Directory files</span></span>
* <span data-ttu-id="1e489-114">Oggetti .NET in memoria</span><span class="sxs-lookup"><span data-stu-id="1e489-114">In-memory .NET objects</span></span>

<span data-ttu-id="1e489-115">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1e489-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="1e489-116">Configurazione predefinita</span><span class="sxs-lookup"><span data-stu-id="1e489-116">Default configuration</span></span>

<span data-ttu-id="1e489-117">ASP.NET core le app Web create con [dotnet new](/dotnet/core/tools/dotnet-new) o Visual Studio generano il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="1e489-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="1e489-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fornisce la configurazione predefinita per l'app nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="1e489-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="1e489-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : aggiunge `IConfiguration` un esistente come origine.</span><span class="sxs-lookup"><span data-stu-id="1e489-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="1e489-120">Nel caso di configurazione predefinito, aggiunge la configurazione [host](#hvac) e impostandola come prima origine per la configurazione _dell'app._</span><span class="sxs-lookup"><span data-stu-id="1e489-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="1e489-121">[appsettings.json](#appsettingsjson) utilizzando il provider di [configurazione JSON.](#file-configuration-provider)</span><span class="sxs-lookup"><span data-stu-id="1e489-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="1e489-122">*impostazioni dell'app.* `Environment` *.json* utilizzando il provider di [configurazione JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="1e489-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="1e489-123">Ad esempio, *appsettings*. ***Produzione***. *json* e *appsettings*. ***Sviluppo***. *json*.</span><span class="sxs-lookup"><span data-stu-id="1e489-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="1e489-124">[Segreti dell'app](xref:security/app-secrets) quando `Development` l'app viene eseguita nell'ambiente.</span><span class="sxs-lookup"><span data-stu-id="1e489-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="1e489-125">Variabili di ambiente che utilizzano il provider di [configurazione Delle variabili](#evcp)di ambiente .</span><span class="sxs-lookup"><span data-stu-id="1e489-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="1e489-126">Argomenti della riga di comando che utilizzano il provider di [configurazione della riga di comando](#command-line).</span><span class="sxs-lookup"><span data-stu-id="1e489-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="1e489-127">I provider di configurazione aggiunti in un secondo momento sostituiscono le impostazioni della chiave precedente.</span><span class="sxs-lookup"><span data-stu-id="1e489-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="1e489-128">Ad esempio, `MyKey` se è impostato sia in *appsettings.json* che nell'ambiente, viene utilizzato il valore dell'ambiente.</span><span class="sxs-lookup"><span data-stu-id="1e489-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="1e489-129">Utilizzando i provider di configurazione predefiniti, il provider di [configurazione della riga di](#command-line-configuration-provider) comando esegue l'override di tutti gli altri provider.</span><span class="sxs-lookup"><span data-stu-id="1e489-129">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="1e489-130">Per ulteriori `CreateDefaultBuilder`informazioni su , consultate [Impostazioni predefinite del generatore](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="1e489-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="1e489-131">Il codice seguente visualizza i provider di configurazione abilitati nell'ordine in cui sono stati aggiunti:</span><span class="sxs-lookup"><span data-stu-id="1e489-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="1e489-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="1e489-132">appsettings.json</span></span>

<span data-ttu-id="1e489-133">Si consideri il seguente file *appsettings.json:Consider* the following appsettings.json file:</span><span class="sxs-lookup"><span data-stu-id="1e489-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="1e489-134">Il codice seguente del [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) visualizza alcune delle impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="1e489-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="1e489-135">La <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> configurazione predefinita dei carichi nel seguente ordine:</span><span class="sxs-lookup"><span data-stu-id="1e489-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="1e489-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="1e489-136">*appsettings.json*</span></span>
1. <span data-ttu-id="1e489-137">*impostazioni dell'app.* `Environment` *.json* : Ad esempio, le *impostazioni dell'app*. ***Produzione***. *json* e *appsettings*. ***Sviluppo***. *file json.*</span><span class="sxs-lookup"><span data-stu-id="1e489-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="1e489-138">La versione dell'ambiente del file viene caricata in base a [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="1e489-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="1e489-139">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="1e489-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="1e489-140">*appsettings*. `Environment`. *I* valori json eseguono l'override delle chiavi in *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1e489-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="1e489-141">Ad esempio, per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="1e489-141">For example, by default:</span></span>

* <span data-ttu-id="1e489-142">In fase di sviluppo, *appsettings*. ***Sviluppo***. *la* configurazione json sovrascrive i valori trovati in *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1e489-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="1e489-143">In produzione, *appsettings*. ***Produzione***. *la* configurazione json sovrascrive i valori trovati in *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1e489-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="1e489-144">Ad esempio, quando si distribuisce l'app in Azure.For example, when deploying the app to Azure.</span><span class="sxs-lookup"><span data-stu-id="1e489-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="1e489-145">Associare i dati di configurazione gerarchici usando il modello di opzioniBind hierarchical configuration data using the options pattern</span><span class="sxs-lookup"><span data-stu-id="1e489-145">Bind hierarchical configuration data using the options pattern</span></span>

<span data-ttu-id="1e489-146">Il modo migliore per leggere i valori di configurazione correlati consiste nell'utilizzare il modello di [opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="1e489-146">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="1e489-147">Ad esempio, per leggere i seguenti valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="1e489-147">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="1e489-148">Creare la `PositionOptions` classe seguente:Create the following class:</span><span class="sxs-lookup"><span data-stu-id="1e489-148">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="1e489-149">Tutte le proprietà pubbliche di lettura/scrittura del tipo sono associate.</span><span class="sxs-lookup"><span data-stu-id="1e489-149">All the public read-write properties of the type are bound.</span></span> <span data-ttu-id="1e489-150">I campi ***non*** sono associati.</span><span class="sxs-lookup"><span data-stu-id="1e489-150">Fields are ***not*** bound.</span></span>

<span data-ttu-id="1e489-151">Il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="1e489-151">The following code:</span></span>

* <span data-ttu-id="1e489-152">Chiama [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) per `PositionOptions` associare `Position` la classe alla sezione .</span><span class="sxs-lookup"><span data-stu-id="1e489-152">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="1e489-153">Visualizza `Position` i dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-153">Displays the `Position` configuration data.</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="1e489-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)associa e restituisce il tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="1e489-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="1e489-155">`ConfigurationBinder.Get<T>`potrebbe essere più `ConfigurationBinder.Bind`conveniente rispetto all'utilizzo di .</span><span class="sxs-lookup"><span data-stu-id="1e489-155">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="1e489-156">Il codice seguente viene `ConfigurationBinder.Get<T>` illustrato `PositionOptions` come utilizzare con la classe:The following code shows how to use with the class:</span><span class="sxs-lookup"><span data-stu-id="1e489-156">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="1e489-157">Un approccio alternativo quando si usa `Position` il modello di ***opzioni*** consiste nell'associare la sezione e aggiungerla al contenitore del servizio di inserimento delle [dipendenze.](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="1e489-157">An alternative approach when using the ***options pattern*** is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1e489-158">Nel codice seguente `PositionOptions` viene aggiunto al <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> contenitore dei servizi con e associato alla configurazione:In the following code, is added to the service container with and bound to configuration:</span><span class="sxs-lookup"><span data-stu-id="1e489-158">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="1e489-159">Utilizzando il codice precedente, il codice seguente legge le opzioni di posizione:Using the preceding code, the following code reads the position options:</span><span class="sxs-lookup"><span data-stu-id="1e489-159">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="1e489-160">Usando la configurazione [predefinita,](#default) *appsettings.json* e *appsettings.* `Environment`I file *.json* sono abilitati con [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="1e489-160">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="1e489-161">Modifiche apportate a *appsettings.json* e *appsettings.* `Environment`Il file *.json* ***dopo*** l'avvio dell'app viene letto dal provider di [configurazione JSON.](#jcp)</span><span class="sxs-lookup"><span data-stu-id="1e489-161">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="1e489-162">Vedere Provider di [configurazione JSON](#jcp) in questo documento per informazioni sull'aggiunta di file di configurazione JSON aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="1e489-162">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="1e489-163">Responsabile della sicurezza e dei segreti</span><span class="sxs-lookup"><span data-stu-id="1e489-163">Security and secret manager</span></span>

<span data-ttu-id="1e489-164">Linee guida per i dati di configurazione:Configuration data guidelines:</span><span class="sxs-lookup"><span data-stu-id="1e489-164">Configuration data guidelines:</span></span>

* <span data-ttu-id="1e489-165">Non archiviare mai la password o altri dati sensibili nel codice del provider di configurazione o in file di configurazione di testo normale.</span><span class="sxs-lookup"><span data-stu-id="1e489-165">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="1e489-166">Il [gestore segreto](xref:security/app-secrets) può essere utilizzato per archiviare i segreti in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="1e489-166">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="1e489-167">Non usare i segreti di produzione in ambienti di sviluppo o di test.</span><span class="sxs-lookup"><span data-stu-id="1e489-167">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="1e489-168">Specificare i segreti all'esterno del progetto in modo che non possano essere inavvertitamente inviati a un repository del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="1e489-168">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="1e489-169">Per [impostazione predefinita,](#default) [Secret Manager](xref:security/app-secrets) legge le impostazioni di configurazione dopo *appsettings.json* e *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="1e489-169">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="1e489-170">Per ulteriori informazioni sull'archiviazione di password o altri dati sensibili:</span><span class="sxs-lookup"><span data-stu-id="1e489-170">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="1e489-171"><xref:security/app-secrets>: include consigli sull'utilizzo delle variabili di ambiente per memorizzare dati sensibili.</span><span class="sxs-lookup"><span data-stu-id="1e489-171"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="1e489-172">Secret Manager usa il provider di [configurazione file](#fcp) per archiviare i segreti utente in un file JSON nel sistema locale.</span><span class="sxs-lookup"><span data-stu-id="1e489-172">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="1e489-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) archivia in modo sicuro i segreti delle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e489-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="1e489-174">Per altre informazioni, vedere <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="1e489-174">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="1e489-175">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="1e489-175">Environment variables</span></span>

<span data-ttu-id="1e489-176">Utilizzando la configurazione <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> [predefinita,](#default) la configurazione dei carichi dalle coppie chiave-valore delle variabili di ambiente dopo la lettura di *appsettings.json*, *appsettings.* `Environment` *.json*e [Secret manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="1e489-176">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="1e489-177">Di conseguenza, i valori chiave letti dall'ambiente eseguono l'override dei valori letti da *appsettings.json*, *appsettings.* `Environment` *.json*e Gestore segreto.</span><span class="sxs-lookup"><span data-stu-id="1e489-177">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="1e489-178">I `set` seguenti comandi:</span><span class="sxs-lookup"><span data-stu-id="1e489-178">The following `set` commands:</span></span>

* <span data-ttu-id="1e489-179">Impostare le chiavi di ambiente e i valori [dell'esempio precedente](#appsettingsjson) in Windows.</span><span class="sxs-lookup"><span data-stu-id="1e489-179">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="1e489-180">Verificare le impostazioni quando si utilizza il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="1e489-180">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="1e489-181">Il `dotnet run` comando deve essere eseguito nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="1e489-181">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="1e489-182">Le impostazioni di ambiente precedenti:The preceding environment settings:</span><span class="sxs-lookup"><span data-stu-id="1e489-182">The preceding environment settings:</span></span>

* <span data-ttu-id="1e489-183">Vengono impostati solo nei processi avviati dalla finestra di comando in cui sono stati impostati.</span><span class="sxs-lookup"><span data-stu-id="1e489-183">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="1e489-184">Non verrà letto dai browser avviati con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1e489-184">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="1e489-185">I seguenti comandi [setx](/windows-server/administration/windows-commands/setx) possono essere utilizzati per impostare le chiavi e i valori dell'ambiente in Windows.</span><span class="sxs-lookup"><span data-stu-id="1e489-185">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="1e489-186">A `set` `setx` differenza di , le impostazioni vengono mantenute.</span><span class="sxs-lookup"><span data-stu-id="1e489-186">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="1e489-187">`/M`imposta la variabile nell'ambiente di sistema.</span><span class="sxs-lookup"><span data-stu-id="1e489-187">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="1e489-188">Se `/M` l'opzione non viene utilizzata, viene impostata una variabile di ambiente utente.</span><span class="sxs-lookup"><span data-stu-id="1e489-188">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="1e489-189">Per verificare che i comandi precedenti esequillino per *appsettings.json* e *appsettings.* `Environment` *.json*:</span><span class="sxs-lookup"><span data-stu-id="1e489-189">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="1e489-190">Con Visual Studio: esci e riavvia Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1e489-190">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="1e489-191">Con l'interfaccia della riga di `dotnet run`comando: avviare una nuova finestra di comando e immettere .</span><span class="sxs-lookup"><span data-stu-id="1e489-191">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="1e489-192">Chiamare <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> con una stringa per specificare un prefisso per le variabili di ambiente:Call with a string to specify a prefix for environment variables:</span><span class="sxs-lookup"><span data-stu-id="1e489-192">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="1e489-193">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="1e489-193">In the preceding code:</span></span>

* <span data-ttu-id="1e489-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`viene aggiunto dopo i provider di [configurazione predefiniti.](#default)</span><span class="sxs-lookup"><span data-stu-id="1e489-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="1e489-195">Per un esempio di ordinamento dei provider di configurazione, vedere Provider di [configurazione JSON.](#jcp)</span><span class="sxs-lookup"><span data-stu-id="1e489-195">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="1e489-196">Le variabili di `MyCustomPrefix_` ambiente impostate con il prefisso eseguono l'override dei [provider di configurazione predefiniti.](#default)</span><span class="sxs-lookup"><span data-stu-id="1e489-196">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="1e489-197">Sono incluse le variabili di ambiente senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="1e489-197">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="1e489-198">Il prefisso viene rimosso quando vengono lette le coppie chiave-valore di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-198">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="1e489-199">I comandi seguenti testano il prefisso personalizzato:</span><span class="sxs-lookup"><span data-stu-id="1e489-199">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="1e489-200">La [configurazione predefinita](#default) carica le variabili di `DOTNET_` `ASPNETCORE_`ambiente e gli argomenti della riga di comando preceduti da e .</span><span class="sxs-lookup"><span data-stu-id="1e489-200">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="1e489-201">I `DOTNET_` `ASPNETCORE_` prefissi e vengono utilizzati da ASP.NET Core per la configurazione di [host e app,](xref:fundamentals/host/generic-host#host-configuration)ma non per la configurazione utente.</span><span class="sxs-lookup"><span data-stu-id="1e489-201">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="1e489-202">Per altre informazioni sulla configurazione di host e app, vedere [Host generico .NET.](xref:fundamentals/host/generic-host)</span><span class="sxs-lookup"><span data-stu-id="1e489-202">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="1e489-203">In [Servizio app di Azure](https://azure.microsoft.com/services/app-service/)selezionare Nuova **impostazione applicazione** nella pagina Impostazioni > **configurazione.**</span><span class="sxs-lookup"><span data-stu-id="1e489-203">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="1e489-204">Le impostazioni dell'applicazione del servizio app di Azure sono:Azure App Service application settings are:</span><span class="sxs-lookup"><span data-stu-id="1e489-204">Azure App Service application settings are:</span></span>

* <span data-ttu-id="1e489-205">Crittografato a riposo e trasmesso su un canale crittografato.</span><span class="sxs-lookup"><span data-stu-id="1e489-205">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="1e489-206">Esposto come variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="1e489-206">Exposed as environment variables.</span></span>

<span data-ttu-id="1e489-207">Per altre informazioni, vedere [App di Azure: Eseguire l'override della configurazione delle app usando il portale di Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="1e489-207">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="1e489-208">Per informazioni sulle stringhe di connessione al database di Azure, vedere Prefissi delle stringhe di [connessione.](#constr)</span><span class="sxs-lookup"><span data-stu-id="1e489-208">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="1e489-209">Riga di comando</span><span class="sxs-lookup"><span data-stu-id="1e489-209">Command-line</span></span>

<span data-ttu-id="1e489-210">Utilizzando la configurazione <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> [predefinita,](#default) la configurazione dei carichi dalle coppie chiave-valore degli argomenti della riga di comando dopo le origini di configurazione seguenti:Using the default configuration, the loads configuration from command-line argument key-value pairs after the following configuration sources:</span><span class="sxs-lookup"><span data-stu-id="1e489-210">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="1e489-211">*appsettings.json* e *appsettings*. `Environment`. *file json.*</span><span class="sxs-lookup"><span data-stu-id="1e489-211">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="1e489-212">[Segreti dell'app (Secret Manager)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="1e489-212">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="1e489-213">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="1e489-213">Environment variables.</span></span>

<span data-ttu-id="1e489-214">Per [impostazione predefinita,](#default)i valori di configurazione impostati nella riga di comando eseguono l'override dei valori di configurazione impostati con tutti gli altri provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-214">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="1e489-215">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="1e489-215">Command-line arguments</span></span>

<span data-ttu-id="1e489-216">Il comando seguente imposta `=`chiavi e valori utilizzando :</span><span class="sxs-lookup"><span data-stu-id="1e489-216">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="1e489-217">Il comando seguente imposta `/`chiavi e valori utilizzando :</span><span class="sxs-lookup"><span data-stu-id="1e489-217">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="1e489-218">Il comando seguente imposta `--`chiavi e valori utilizzando :</span><span class="sxs-lookup"><span data-stu-id="1e489-218">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="1e489-219">Il valore della chiave:</span><span class="sxs-lookup"><span data-stu-id="1e489-219">The key value:</span></span>

* <span data-ttu-id="1e489-220">Deve `=`seguire , o la chiave `--` `/` deve avere un prefisso o quando il valore segue uno spazio.</span><span class="sxs-lookup"><span data-stu-id="1e489-220">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="1e489-221">Non è obbligatorio `=` se viene utilizzato.</span><span class="sxs-lookup"><span data-stu-id="1e489-221">Isn't required if `=` is used.</span></span> <span data-ttu-id="1e489-222">Ad esempio: `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="1e489-222">For example, `MySetting=`.</span></span>

<span data-ttu-id="1e489-223">All'interno dello stesso comando, non combinare coppie di `=` argomenti chiave-valore della riga di comando che utilizzano coppie chiave-valore che utilizzano uno spazio.</span><span class="sxs-lookup"><span data-stu-id="1e489-223">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="1e489-224">Mapping di sostituzione</span><span class="sxs-lookup"><span data-stu-id="1e489-224">Switch mappings</span></span>

<span data-ttu-id="1e489-225">I mapping switch consentono la logica di sostituzione del nome **della chiave.**</span><span class="sxs-lookup"><span data-stu-id="1e489-225">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="1e489-226">Fornire un dizionario di <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> sostituzioni di switch al metodo.</span><span class="sxs-lookup"><span data-stu-id="1e489-226">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="1e489-227">Quando viene utilizzato il dizionario dei mapping di sostituzione, nel dizionario viene controllata la presenza di una chiave corrispondente alla chiave fornita da un argomento della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="1e489-227">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="1e489-228">Se la chiave della riga di comando viene trovata nel dizionario, il valore del dizionario viene passato per impostare la coppia chiave-valore nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1e489-228">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="1e489-229">Un mapping di sostituzione è necessario per le chiavi della riga di comando con un trattino singolo (`-`) come prefisso.</span><span class="sxs-lookup"><span data-stu-id="1e489-229">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="1e489-230">Regole principali del dizionario dei mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="1e489-230">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="1e489-231">Gli interruttori `-` `--`devono iniziare con o .</span><span class="sxs-lookup"><span data-stu-id="1e489-231">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="1e489-232">Il dizionario dei mapping di sostituzione non deve contenere chiavi duplicate.</span><span class="sxs-lookup"><span data-stu-id="1e489-232">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="1e489-233">Per utilizzare un dizionario dei mapping degli `AddCommandLine`switch, passarlo alla chiamata a :</span><span class="sxs-lookup"><span data-stu-id="1e489-233">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="1e489-234">Nel codice seguente vengono illustrati i valori chiave per le chiavi sostituite:</span><span class="sxs-lookup"><span data-stu-id="1e489-234">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="1e489-235">Eseguire il comando seguente per verificare la sostituzione del tasto:</span><span class="sxs-lookup"><span data-stu-id="1e489-235">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="1e489-236">Nota: `=` Attualmente, non può essere utilizzato per `-`impostare i valori di sostituzione delle chiavi con un singolo trattino .</span><span class="sxs-lookup"><span data-stu-id="1e489-236">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="1e489-237">Vedere [il problema in GitHub](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="1e489-237">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="1e489-238">Il comando seguente funziona per testare la sostituzione dei tasti:</span><span class="sxs-lookup"><span data-stu-id="1e489-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="1e489-239">Per le app che usano i mapping di sostituzione, la chiamata a `CreateDefaultBuilder` non deve passare argomenti.</span><span class="sxs-lookup"><span data-stu-id="1e489-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="1e489-240">La `CreateDefaultBuilder` chiamata `AddCommandLine` del metodo non include opzioni mappate e non è possibile passare `CreateDefaultBuilder`il dizionario di mapping delle switch a .</span><span class="sxs-lookup"><span data-stu-id="1e489-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="1e489-241">La soluzione non è quella `CreateDefaultBuilder` di passare gli `ConfigurationBuilder` argomenti `AddCommandLine` a ma invece di consentire al metodo del metodo di elaborare sia gli argomenti che il dizionario di switch-mapping.</span><span class="sxs-lookup"><span data-stu-id="1e489-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="1e489-242">Dati di configurazione gerarchici</span><span class="sxs-lookup"><span data-stu-id="1e489-242">Hierarchical configuration data</span></span>

<span data-ttu-id="1e489-243">L'API di configurazione legge i dati di configurazione gerarchici appiattindo i dati gerarchici con l'uso di un delimitatore nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="1e489-244">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il seguente file *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="1e489-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="1e489-245">Il codice seguente del [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) visualizza diverse impostazioni di configurazione:</span><span class="sxs-lookup"><span data-stu-id="1e489-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="1e489-246">Il modo migliore per leggere i dati di configurazione gerarchici consiste nell'utilizzare il modello di opzioni.</span><span class="sxs-lookup"><span data-stu-id="1e489-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="1e489-247">Per altre informazioni, vedere Associare dati di [configurazione gerarchici](#optpat) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="1e489-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="1e489-248">I metodi <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sono disponibili per isolare le sezioni e gli elementi figlio di una sezione nei dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="1e489-249">Questi metodi sono descritti più avanti in [GetSection, GetChildren ed Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="1e489-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="1e489-250">Chiavi e valori di configurazione</span><span class="sxs-lookup"><span data-stu-id="1e489-250">Configuration keys and values</span></span>

<span data-ttu-id="1e489-251">Chiavi di configurazione:</span><span class="sxs-lookup"><span data-stu-id="1e489-251">Configuration keys:</span></span>

* <span data-ttu-id="1e489-252">Non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="1e489-252">Are case-insensitive.</span></span> <span data-ttu-id="1e489-253">Ad esempio, `ConnectionString` e `connectionstring` vengono considerate chiavi equivalenti.</span><span class="sxs-lookup"><span data-stu-id="1e489-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="1e489-254">Se una chiave e un valore sono impostati in più provider di configurazione, viene utilizzato il valore dell'ultimo provider aggiunto.</span><span class="sxs-lookup"><span data-stu-id="1e489-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="1e489-255">Per ulteriori informazioni, vedere [Configurazione predefinita](#default).</span><span class="sxs-lookup"><span data-stu-id="1e489-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="1e489-256">Chiavi gerarchiche</span><span class="sxs-lookup"><span data-stu-id="1e489-256">Hierarchical keys</span></span>
  * <span data-ttu-id="1e489-257">Nell'ambito dell'API di configurazione, il separatore due punti (`:`) funziona in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="1e489-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="1e489-258">Nelle variabili di ambiente, un separatore due punti potrebbe non funzionare in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="1e489-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="1e489-259">Un doppio carattere di sottolineatura, `__`, è supportato da tutte le piattaforme e viene convertito automaticamente in due punti `:`.</span><span class="sxs-lookup"><span data-stu-id="1e489-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="1e489-260">In Archiviazione chiave di `--` Azure le chiavi gerarchiche vengono usate come separatore.</span><span class="sxs-lookup"><span data-stu-id="1e489-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="1e489-261">Il provider di configurazione di `--` Azure `:` [Key Vault](xref:security/key-vault-configuration) viene sostituito automaticamente con un quando i segreti vengono caricati nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1e489-261">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="1e489-262">Il <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supporta l'associazione di matrici agli oggetti usando gli indici delle matrici nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="1e489-263">L'associazione di matrici è descritta nella sezione [Associare una matrice a una classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="1e489-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="1e489-264">Valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="1e489-264">Configuration values:</span></span>

* <span data-ttu-id="1e489-265">Sono stringhe.</span><span class="sxs-lookup"><span data-stu-id="1e489-265">Are strings.</span></span>
* <span data-ttu-id="1e489-266">I valori null non possono essere archiviati nella configurazione o associati a oggetti.</span><span class="sxs-lookup"><span data-stu-id="1e489-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="1e489-267">Provider di configurazione</span><span class="sxs-lookup"><span data-stu-id="1e489-267">Configuration providers</span></span>

<span data-ttu-id="1e489-268">La tabella seguente mostra i provider di configurazione disponibili per le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e489-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="1e489-269">Provider</span><span class="sxs-lookup"><span data-stu-id="1e489-269">Provider</span></span> | <span data-ttu-id="1e489-270">Fornisce la configurazione da</span><span class="sxs-lookup"><span data-stu-id="1e489-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="1e489-271">Provider di configurazione di Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="1e489-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="1e489-272">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="1e489-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="1e489-273">Provider di configurazione dell'app di AzureAzure App configuration provider</span><span class="sxs-lookup"><span data-stu-id="1e489-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="1e489-274">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="1e489-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="1e489-275">Provider di configurazione della riga di comando</span><span class="sxs-lookup"><span data-stu-id="1e489-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="1e489-276">Parametri della riga di comando</span><span class="sxs-lookup"><span data-stu-id="1e489-276">Command-line parameters</span></span> |
| [<span data-ttu-id="1e489-277">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="1e489-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="1e489-278">Origine personalizzata</span><span class="sxs-lookup"><span data-stu-id="1e489-278">Custom source</span></span> |
| [<span data-ttu-id="1e489-279">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="1e489-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="1e489-280">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="1e489-280">Environment variables</span></span> |
| [<span data-ttu-id="1e489-281">Provider di configurazione file</span><span class="sxs-lookup"><span data-stu-id="1e489-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="1e489-282">File INI, JSON e XML</span><span class="sxs-lookup"><span data-stu-id="1e489-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="1e489-283">Provider di configurazione chiave per fileKey-per-file configuration provider</span><span class="sxs-lookup"><span data-stu-id="1e489-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="1e489-284">File della directory</span><span class="sxs-lookup"><span data-stu-id="1e489-284">Directory files</span></span> |
| [<span data-ttu-id="1e489-285">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="1e489-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="1e489-286">Raccolte in memoria</span><span class="sxs-lookup"><span data-stu-id="1e489-286">In-memory collections</span></span> |
| [<span data-ttu-id="1e489-287">Responsabile Segreto</span><span class="sxs-lookup"><span data-stu-id="1e489-287">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="1e489-288">File nella directory dei profili utente</span><span class="sxs-lookup"><span data-stu-id="1e489-288">File in the user profile directory</span></span> |

<span data-ttu-id="1e489-289">Le origini di configurazione vengono lette nell'ordine in cui sono specificati i provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="1e489-290">Ordina i provider di configurazione nel codice in base alle priorità per le origini di configurazione sottostanti che l'app richiede.</span><span class="sxs-lookup"><span data-stu-id="1e489-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="1e489-291">Una sequenza tipica di provider di configurazione è:</span><span class="sxs-lookup"><span data-stu-id="1e489-291">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="1e489-292">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="1e489-292">*appsettings.json*</span></span>
1. <span data-ttu-id="1e489-293">*appsettings*. `Environment`. *json*</span><span class="sxs-lookup"><span data-stu-id="1e489-293">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="1e489-294">Responsabile Segreto</span><span class="sxs-lookup"><span data-stu-id="1e489-294">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="1e489-295">Variabili di ambiente che utilizzano il provider di [configurazione Delle variabili](#evcp)di ambiente .</span><span class="sxs-lookup"><span data-stu-id="1e489-295">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="1e489-296">Argomenti della riga di comando che utilizzano il provider di [configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="1e489-296">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="1e489-297">Una pratica comune consiste nell'aggiungere il provider di configurazione della riga di comando per ultimo in una serie di provider per consentire agli argomenti della riga di comando di eseguire l'override della configurazione impostata dagli altri provider.</span><span class="sxs-lookup"><span data-stu-id="1e489-297">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="1e489-298">La sequenza di provider precedente viene utilizzata nella [configurazione predefinita.](#default)</span><span class="sxs-lookup"><span data-stu-id="1e489-298">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="1e489-299">Prefissi della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="1e489-299">Connection string prefixes</span></span>

<span data-ttu-id="1e489-300">L'API di configurazione dispone di regole di elaborazione speciali per quattro variabili di ambiente della stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="1e489-300">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="1e489-301">Queste stringhe di connessione sono coinvolte nella configurazione delle stringhe di connessione di Azure per l'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="1e489-301">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="1e489-302">Le variabili di ambiente con i prefissi visualizzati nella tabella vengono caricate nell'app con la [configurazione predefinita](#default) o quando non viene fornito alcun prefisso per `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="1e489-302">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="1e489-303">Prefisso della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="1e489-303">Connection string prefix</span></span> | <span data-ttu-id="1e489-304">Provider</span><span class="sxs-lookup"><span data-stu-id="1e489-304">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="1e489-305">Provider personalizzato</span><span class="sxs-lookup"><span data-stu-id="1e489-305">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="1e489-306">MySQL</span><span class="sxs-lookup"><span data-stu-id="1e489-306">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="1e489-307">Database SQL di Azure</span><span class="sxs-lookup"><span data-stu-id="1e489-307">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="1e489-308">SQL Server</span><span class="sxs-lookup"><span data-stu-id="1e489-308">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="1e489-309">Quando una variabile di ambiente viene individuata e caricata nella configurazione con uno qualsiasi dei quattro prefissi indicati nella tabella:</span><span class="sxs-lookup"><span data-stu-id="1e489-309">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="1e489-310">La chiave di configurazione viene creata rimuovendo il prefisso della variabile di ambiente e aggiungendo una sezione per la chiave di configurazione (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="1e489-310">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="1e489-311">Viene creata una nuova coppia chiave-valore della configurazione che rappresenta il provider di connessione al database (ad eccezione di `CUSTOMCONNSTR_`, che non ha un provider dichiarato).</span><span class="sxs-lookup"><span data-stu-id="1e489-311">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="1e489-312">Chiave della variabile di ambiente</span><span class="sxs-lookup"><span data-stu-id="1e489-312">Environment variable key</span></span> | <span data-ttu-id="1e489-313">Chiave di configurazione convertita</span><span class="sxs-lookup"><span data-stu-id="1e489-313">Converted configuration key</span></span> | <span data-ttu-id="1e489-314">Voce di configurazione del provider</span><span class="sxs-lookup"><span data-stu-id="1e489-314">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="1e489-315">Voce di configurazione non creata.</span><span class="sxs-lookup"><span data-stu-id="1e489-315">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="1e489-316">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="1e489-316">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="1e489-317">Valore: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="1e489-317">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="1e489-318">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="1e489-318">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="1e489-319">Valore: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="1e489-319">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="1e489-320">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="1e489-320">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="1e489-321">Valore: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="1e489-321">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="1e489-322">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="1e489-322">JSON configuration provider</span></span>

<span data-ttu-id="1e489-323">La <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> configurazione dei carica dalle coppie chiave-valore del file JSON.</span><span class="sxs-lookup"><span data-stu-id="1e489-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="1e489-324">Gli overload possono specificare:Overloads can specify:</span><span class="sxs-lookup"><span data-stu-id="1e489-324">Overloads can specify:</span></span>

* <span data-ttu-id="1e489-325">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="1e489-325">Whether the file is optional.</span></span>
* <span data-ttu-id="1e489-326">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="1e489-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="1e489-327">Esaminare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="1e489-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="1e489-328">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="1e489-328">The preceding code:</span></span>

* <span data-ttu-id="1e489-329">Configura il provider di configurazione JSON per caricare il file *MyConfig.json* con le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1e489-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="1e489-330">`optional: true`: il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="1e489-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="1e489-331">`reloadOnChange: true`: il file viene ricaricato quando vengono salvate le modifiche.</span><span class="sxs-lookup"><span data-stu-id="1e489-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="1e489-332">Legge i provider di [configurazione predefiniti](#default) prima del file *MyConfig.json.*</span><span class="sxs-lookup"><span data-stu-id="1e489-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="1e489-333">Le impostazioni nell'impostazione di override del file *MyConfig.json* nei provider di configurazione predefiniti, inclusi il provider di [configurazione Variabili](#evcp) di ambiente e il provider di configurazione della riga [di comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="1e489-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="1e489-334">In genere ***non si*** desidera impostare un file JSON personalizzato che esegue l'override dei valori nel provider di configurazione [Delle variabili](#evcp) di ambiente e nel provider di configurazione della riga [di comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="1e489-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="1e489-335">Il codice seguente cancella tutti i provider di configurazione e aggiunge diversi provider di configurazione:The following code clears all the configuration providers and adds several configuration providers:</span><span class="sxs-lookup"><span data-stu-id="1e489-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="1e489-336">Nel codice precedente, le impostazioni in *MyConfig.json* e *MyConfig*. `Environment`. *file json:*</span><span class="sxs-lookup"><span data-stu-id="1e489-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="1e489-337">Eseguire l'override delle impostazioni in *appsettings.json* e *appsettings*. `Environment`. *file json.*</span><span class="sxs-lookup"><span data-stu-id="1e489-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="1e489-338">Vengono sostituiti dalle impostazioni nel provider di [configurazione Delle variabili](#evcp) di ambiente e nel provider di configurazione della riga di [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="1e489-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="1e489-339">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il seguente file *MyConfig.json:*</span><span class="sxs-lookup"><span data-stu-id="1e489-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="1e489-340">Il codice seguente del [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) visualizza alcune delle impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="1e489-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="1e489-341">Provider di configurazione file</span><span class="sxs-lookup"><span data-stu-id="1e489-341">File configuration provider</span></span>

<span data-ttu-id="1e489-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> è la classe base per il caricamento della configurazione dal file system.</span><span class="sxs-lookup"><span data-stu-id="1e489-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="1e489-343">I seguenti provider `FileConfigurationProvider`di configurazione derivano da :</span><span class="sxs-lookup"><span data-stu-id="1e489-343">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="1e489-344">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="1e489-344">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="1e489-345">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="1e489-345">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="1e489-346">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="1e489-346">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="1e489-347">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="1e489-347">INI configuration provider</span></span>

<span data-ttu-id="1e489-348"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carica la configurazione da coppie chiave-valore di file INI in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="1e489-348">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="1e489-349">Il codice seguente cancella tutti i provider di configurazione e aggiunge diversi provider di configurazione:The following code clears all the configuration providers and adds several configuration providers:</span><span class="sxs-lookup"><span data-stu-id="1e489-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="1e489-350">Nel codice precedente, le impostazioni in *MyIniConfig.ini* e *MyIniConfig*. `Environment`. i file *ini* vengono sostituiti dalle impostazioni in:</span><span class="sxs-lookup"><span data-stu-id="1e489-350">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="1e489-351">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="1e489-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="1e489-352">[Provider di configurazione della riga di comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="1e489-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="1e489-353">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il seguente file *MyIniConfig.ini:*</span><span class="sxs-lookup"><span data-stu-id="1e489-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="1e489-354">Il codice seguente del [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) visualizza alcune delle impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="1e489-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="1e489-355">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="1e489-355">XML configuration provider</span></span>

<span data-ttu-id="1e489-356">Il <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carica la configurazione da coppie chiave-valore di file XML in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="1e489-356">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="1e489-357">Il codice seguente cancella tutti i provider di configurazione e aggiunge diversi provider di configurazione:The following code clears all the configuration providers and adds several configuration providers:</span><span class="sxs-lookup"><span data-stu-id="1e489-357">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="1e489-358">Nel codice precedente, le impostazioni in *MyXMLFile.xml* e *MyXMLFile*. `Environment`. *i* file xml vengono sostituiti dalle impostazioni in:</span><span class="sxs-lookup"><span data-stu-id="1e489-358">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="1e489-359">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="1e489-359">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="1e489-360">[Provider di configurazione della riga di comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="1e489-360">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="1e489-361">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il seguente file *MyXMLFile.xml:*</span><span class="sxs-lookup"><span data-stu-id="1e489-361">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="1e489-362">Il codice seguente del [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) visualizza alcune delle impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="1e489-362">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="1e489-363">La ripetizione di elementi che usano lo stesso nome di elemento funziona se si usa l'attributo `name` per distinguere gli elementi:</span><span class="sxs-lookup"><span data-stu-id="1e489-363">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="1e489-364">Il codice seguente legge il file di configurazione precedente e visualizza le chiavi e i valori:The following code reads the previous configuration file and displays the keys and values:</span><span class="sxs-lookup"><span data-stu-id="1e489-364">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="1e489-365">È possibile usare attributi per fornire valori:</span><span class="sxs-lookup"><span data-stu-id="1e489-365">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="1e489-366">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="1e489-366">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="1e489-367">key:attribute</span><span class="sxs-lookup"><span data-stu-id="1e489-367">key:attribute</span></span>
* <span data-ttu-id="1e489-368">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="1e489-368">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="1e489-369">Provider di configurazione chiave per fileKey-per-file configuration provider</span><span class="sxs-lookup"><span data-stu-id="1e489-369">Key-per-file configuration provider</span></span>

<span data-ttu-id="1e489-370">Il <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa i file di una directory come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-370">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="1e489-371">La chiave è il nome del file.</span><span class="sxs-lookup"><span data-stu-id="1e489-371">The key is the file name.</span></span> <span data-ttu-id="1e489-372">Il valore contiene il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="1e489-372">The value contains the file's contents.</span></span> <span data-ttu-id="1e489-373">Il provider di configurazione chiave per file viene utilizzato negli scenari di hosting Docker.The Key-per-file configuration provider is used in Docker hosting scenarios.</span><span class="sxs-lookup"><span data-stu-id="1e489-373">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="1e489-374">Per attivare la configurazione chiave-per-file, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="1e489-374">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="1e489-375">Il `directoryPath` per i file deve essere un percorso assoluto.</span><span class="sxs-lookup"><span data-stu-id="1e489-375">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="1e489-376">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="1e489-376">Overloads permit specifying:</span></span>

* <span data-ttu-id="1e489-377">Un delegato `Action<KeyPerFileConfigurationSource>` che configura l'origine.</span><span class="sxs-lookup"><span data-stu-id="1e489-377">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="1e489-378">Se la directory è facoltativa e il percorso della directory.</span><span class="sxs-lookup"><span data-stu-id="1e489-378">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="1e489-379">Il doppio carattere di sottolineatura (`__`) viene usato come delimitatore per le chiavi di configurazione nei nomi dei file.</span><span class="sxs-lookup"><span data-stu-id="1e489-379">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="1e489-380">Ad esempio, il nome di file `Logging__LogLevel__System` produce la chiave di configurazione `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="1e489-380">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="1e489-381">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="1e489-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="1e489-382">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="1e489-382">Memory configuration provider</span></span>

<span data-ttu-id="1e489-383">Il <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una raccolta in memoria come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-383">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="1e489-384">Il codice seguente aggiunge una raccolta di memoria al sistema di configurazione:The following code adds a memory collection to the configuration system:</span><span class="sxs-lookup"><span data-stu-id="1e489-384">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="1e489-385">Il codice seguente del [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) visualizza le impostazioni di configurazione precedenti:The following code from the sample download displays the preceding configurations settings:</span><span class="sxs-lookup"><span data-stu-id="1e489-385">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="1e489-386">Nel codice precedente, `config.AddInMemoryCollection(Dict)` viene aggiunto dopo i provider di [configurazione predefiniti.](#default)</span><span class="sxs-lookup"><span data-stu-id="1e489-386">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="1e489-387">Per un esempio di ordinamento dei provider di configurazione, vedere Provider di [configurazione JSON.](#jcp)</span><span class="sxs-lookup"><span data-stu-id="1e489-387">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="1e489-388">Per un esempio di ordinamento dei provider di configurazione, vedere Provider di [configurazione JSON.](#jcp)</span><span class="sxs-lookup"><span data-stu-id="1e489-388">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="1e489-389">Consultate [Associare una](#boa) matrice `MemoryConfigurationProvider`per un altro esempio utilizzando .</span><span class="sxs-lookup"><span data-stu-id="1e489-389">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="1e489-390">GetValue</span><span class="sxs-lookup"><span data-stu-id="1e489-390">GetValue</span></span>

<span data-ttu-id="1e489-391">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)estrae un singolo valore dalla configurazione con una chiave specificata e lo converte nel tipo specificato:</span><span class="sxs-lookup"><span data-stu-id="1e489-391">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="1e489-392">Nel codice precedente, `NumberKey` se non viene trovato nella configurazione, `99` viene utilizzato il valore predefinito di.</span><span class="sxs-lookup"><span data-stu-id="1e489-392">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="1e489-393">GetSection, GetChildren ed Exists</span><span class="sxs-lookup"><span data-stu-id="1e489-393">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="1e489-394">Per gli esempi seguenti, si consideri il seguente file *MySubsection.json:*</span><span class="sxs-lookup"><span data-stu-id="1e489-394">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="1e489-395">Il codice seguente aggiunge *MySubsection.json* ai provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="1e489-395">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="1e489-396">GetSection</span><span class="sxs-lookup"><span data-stu-id="1e489-396">GetSection</span></span>

<span data-ttu-id="1e489-397">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) restituisce una sottosezione di configurazione con la chiave della sottosezione specificata.</span><span class="sxs-lookup"><span data-stu-id="1e489-397">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="1e489-398">Il codice seguente `section1`restituisce valori per :</span><span class="sxs-lookup"><span data-stu-id="1e489-398">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="1e489-399">Il codice seguente `section2:subsection0`restituisce valori per :</span><span class="sxs-lookup"><span data-stu-id="1e489-399">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="1e489-400">`GetSection` non restituisce mai `null`.</span><span class="sxs-lookup"><span data-stu-id="1e489-400">`GetSection` never returns `null`.</span></span> <span data-ttu-id="1e489-401">Se non viene trovata una sezione corrispondente, viene restituita una `IConfigurationSection` vuota.</span><span class="sxs-lookup"><span data-stu-id="1e489-401">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="1e489-402">Quando `GetSection` restituisce una sezione corrispondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> non viene compilata.</span><span class="sxs-lookup"><span data-stu-id="1e489-402">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="1e489-403">Quando la sezione esiste, vengono restituiti <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.</span><span class="sxs-lookup"><span data-stu-id="1e489-403">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="1e489-404">GetChildren ed esiste</span><span class="sxs-lookup"><span data-stu-id="1e489-404">GetChildren and Exists</span></span>

<span data-ttu-id="1e489-405">Il codice seguente chiama [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) e restituisce i valori per `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="1e489-405">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="1e489-406">Il codice precedente chiama [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) per verificare l'esistente nella sezione:</span><span class="sxs-lookup"><span data-stu-id="1e489-406">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="1e489-407">Associare una matriceBind an array</span><span class="sxs-lookup"><span data-stu-id="1e489-407">Bind an array</span></span>

<span data-ttu-id="1e489-408">Il [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supporta l'associazione di matrici agli oggetti utilizzando gli indici di matrice nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-408">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="1e489-409">Qualsiasi formato di matrice che espone un segmento di chiave numerica è in grado di associare una matrice di matrici a una matrice di classi [POCO.](https://wikipedia.org/wiki/Plain_Old_CLR_Object)</span><span class="sxs-lookup"><span data-stu-id="1e489-409">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="1e489-410">Si consideri *MyArray.json* dal [download di esempio:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)</span><span class="sxs-lookup"><span data-stu-id="1e489-410">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="1e489-411">Il codice seguente aggiunge *MyArray.json* ai provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="1e489-411">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="1e489-412">Il codice seguente legge la configurazione e visualizza i valori:</span><span class="sxs-lookup"><span data-stu-id="1e489-412">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="1e489-413">Il codice precedente restituisce l'output seguente:The preceding code returns the following output:</span><span class="sxs-lookup"><span data-stu-id="1e489-413">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="1e489-414">Nell'output precedente, l'indice `value40`3 `"4": "value40",` ha valore , corrispondente a in *MyArray.json*.</span><span class="sxs-lookup"><span data-stu-id="1e489-414">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="1e489-415">Gli indici di matrice associati sono continui e non associati all'indice della chiave di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-415">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="1e489-416">Il gestore di associazione della configurazione non è in grado di associare valori null o di creare voci null negli oggetti associatiThe configuration binder isn't capable of binding null values or creating null entries in bound objects</span><span class="sxs-lookup"><span data-stu-id="1e489-416">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="1e489-417">Il codice seguente `array:entries` carica <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> la configurazione con il metodo di estensione:The following code loads the configuration with the extension method:</span><span class="sxs-lookup"><span data-stu-id="1e489-417">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="1e489-418">Il codice seguente legge la `arrayDict` `Dictionary` configurazione in e visualizza i valori:</span><span class="sxs-lookup"><span data-stu-id="1e489-418">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="1e489-419">Il codice precedente restituisce l'output seguente:The preceding code returns the following output:</span><span class="sxs-lookup"><span data-stu-id="1e489-419">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="1e489-420">L'indice &num;3 nell'oggetto associato contiene i dati di configurazione per la chiave di configurazione `array:4` e il relativo valore `value4`.</span><span class="sxs-lookup"><span data-stu-id="1e489-420">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="1e489-421">Quando i dati di configurazione contenenti una matrice sono associati, gli indici della matrice nelle chiavi di configurazione vengono utilizzati per scorrere i dati di configurazione durante la creazione dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="1e489-421">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="1e489-422">Un valore null non può essere mantenuto nei dati di configurazione e una voce con valore null non viene creata in un oggetto associato quando una matrice nelle chiavi di configurazione ignora uno o più indici.</span><span class="sxs-lookup"><span data-stu-id="1e489-422">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="1e489-423">L'elemento di &num;configurazione mancante per l'indice 3 può essere fornito prima dell'associazione all'istanza da qualsiasi provider di configurazione che legge la coppia chiave/valore dell'indice 3.The missing configuration item for index 3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span><span class="sxs-lookup"><span data-stu-id="1e489-423">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="1e489-424">Si consideri il seguente file Value3.json dal download di esempio:Consider the following *Value3.json* file from the sample download:</span><span class="sxs-lookup"><span data-stu-id="1e489-424">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="1e489-425">Il codice seguente include la configurazione `arrayDict` `Dictionary`per *Value3.json* e :</span><span class="sxs-lookup"><span data-stu-id="1e489-425">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="1e489-426">Il codice seguente legge la configurazione precedente e visualizza i valori:</span><span class="sxs-lookup"><span data-stu-id="1e489-426">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="1e489-427">Il codice precedente restituisce l'output seguente:The preceding code returns the following output:</span><span class="sxs-lookup"><span data-stu-id="1e489-427">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="1e489-428">I provider di configurazione personalizzati non devono implementare l'associazione di matrici.</span><span class="sxs-lookup"><span data-stu-id="1e489-428">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="1e489-429">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="1e489-429">Custom configuration provider</span></span>

<span data-ttu-id="1e489-430">L'app di esempio dimostra come creare un provider di configurazione di base che legge le coppie chiave-valore di configurazione da un database usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="1e489-430">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="1e489-431">Il provider ha le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="1e489-431">The provider has the following characteristics:</span></span>

* <span data-ttu-id="1e489-432">Il database in memoria di Entity Framework viene usato a scopo dimostrativo.</span><span class="sxs-lookup"><span data-stu-id="1e489-432">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="1e489-433">Per usare un database che richiede una stringa di connessione, implementare un `ConfigurationBuilder` secondario per fornire la stringa di connessione da un altro provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-433">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="1e489-434">Il provider legge una tabella di database in una configurazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="1e489-434">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="1e489-435">Il provider non esegue una query sul database per ogni chiave.</span><span class="sxs-lookup"><span data-stu-id="1e489-435">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="1e489-436">Il ricaricamento in caso di modifica non è implementato, quindi l'aggiornamento del database dopo l'avvio dell'app non ha alcun effetto sulla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1e489-436">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="1e489-437">Definire un'entità `EFConfigurationValue` per l'archiviazione dei valori di configurazione nel database.</span><span class="sxs-lookup"><span data-stu-id="1e489-437">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="1e489-438">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e489-438">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="1e489-439">Aggiungere `EFConfigurationContext` per archiviare i valori configurati e accedervi.</span><span class="sxs-lookup"><span data-stu-id="1e489-439">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="1e489-440">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e489-440">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="1e489-441">Creare una classe che implementi <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="1e489-441">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="1e489-442">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e489-442">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="1e489-443">Creare il provider di configurazione personalizzato ereditando da <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="1e489-443">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="1e489-444">Il provider di configurazione inizializza il database quando è vuoto.</span><span class="sxs-lookup"><span data-stu-id="1e489-444">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="1e489-445">Poiché le chiavi di configurazione non fanno distinzione [tra maiuscole e minuscole](#keys), il dizionario utilizzato per inizializzare il database viene creato con l'operatore di confronto senza distinzione tra maiuscole e minuscole ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="1e489-445">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="1e489-446">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e489-446">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="1e489-447">Un metodo di estensione `AddEFConfiguration` consente di aggiungere l'origine di configurazione a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1e489-447">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="1e489-448">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e489-448">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="1e489-449">L'esempio di codice seguente mostra come usare il `EFConfigurationProvider` personalizzato in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e489-449">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="1e489-450">Configurazione dell'accesso in Avvio</span><span class="sxs-lookup"><span data-stu-id="1e489-450">Access configuration in Startup</span></span>

<span data-ttu-id="1e489-451">Il codice seguente visualizza `Startup` i dati di configurazione nei metodi:The following code displays configuration data in methods:</span><span class="sxs-lookup"><span data-stu-id="1e489-451">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="1e489-452">Per un esempio di accesso alla configurazione usando metodi di servizio di avvio, vedere [Avvio dell'applicazione: Metodi pratici](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="1e489-452">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="1e489-453">Configurazione dell'accesso nelle pagine RazorAccess configuration in Razor Pages</span><span class="sxs-lookup"><span data-stu-id="1e489-453">Access configuration in Razor Pages</span></span>

<span data-ttu-id="1e489-454">Il codice seguente visualizza i dati di configurazione in una pagina Razor:The following code displays configuration data in a Razor Page:</span><span class="sxs-lookup"><span data-stu-id="1e489-454">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="1e489-455">Configurazione di accesso in un file di visualizzazione MVCAccess configuration in a MVC view file</span><span class="sxs-lookup"><span data-stu-id="1e489-455">Access configuration in a MVC view file</span></span>

<span data-ttu-id="1e489-456">Il codice seguente visualizza i dati di configurazione in una visualizzazione MVC:The following code displays configuration data in a MVC view:</span><span class="sxs-lookup"><span data-stu-id="1e489-456">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="1e489-457">Host e configurazione delle app</span><span class="sxs-lookup"><span data-stu-id="1e489-457">Host versus app configuration</span></span>

<span data-ttu-id="1e489-458">Prima che l'app venga configurata e avviata, viene configurato e avviato un *host*.</span><span class="sxs-lookup"><span data-stu-id="1e489-458">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="1e489-459">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="1e489-459">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="1e489-460">Sia l'app che l'host vengono configurati tramite i provider di configurazione descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="1e489-460">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="1e489-461">Anche le coppie chiave-valore di configurazione dell'host sono incluse nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1e489-461">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="1e489-462">Per altre informazioni su come vengono usati i provider di configurazione quando viene creato l'host e sugli effetti delle origini di configurazione sull'host di configurazione, vedere <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="1e489-462">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="1e489-463">Configurazione host predefinita</span><span class="sxs-lookup"><span data-stu-id="1e489-463">Default host configuration</span></span>

<span data-ttu-id="1e489-464">Per informazioni dettagliate sulla configurazione predefinita quando viene usato l'[host Web](xref:fundamentals/host/web-host), vedere la [versione di questo argomento per ASP.NET Core 2.2](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="1e489-464">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="1e489-465">La configurazione dell'host viene specificata da:</span><span class="sxs-lookup"><span data-stu-id="1e489-465">Host configuration is provided from:</span></span>
  * <span data-ttu-id="1e489-466">Variabili di ambiente `DOTNET_` precedute `DOTNET_ENVIRONMENT`da (ad esempio, ) utilizzando il provider di [configurazione Delle variabili](#environment-variables-configuration-provider)di ambiente .</span><span class="sxs-lookup"><span data-stu-id="1e489-466">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="1e489-467">Il prefisso (`DOTNET_`) viene rimosso al caricamento delle coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-467">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="1e489-468">Argomenti della riga di comando che utilizzano il provider di [configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="1e489-468">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="1e489-469">La configurazione predefinita dell'host Web viene stabilita (`ConfigureWebHostDefaults`) nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="1e489-469">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="1e489-470">Kestrel viene usato come server Web e configurato con i provider di configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1e489-470">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="1e489-471">Aggiungere il middleware di filtro host.</span><span class="sxs-lookup"><span data-stu-id="1e489-471">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="1e489-472">Aggiungere il middleware delle intestazioni inoltrate se la variabile di ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è impostata su `true`.</span><span class="sxs-lookup"><span data-stu-id="1e489-472">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="1e489-473">Abilitare l'integrazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="1e489-473">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="1e489-474">Altra configurazione</span><span class="sxs-lookup"><span data-stu-id="1e489-474">Other configuration</span></span>

<span data-ttu-id="1e489-475">Questo argomento riguarda solo la *configurazione dell'app*.</span><span class="sxs-lookup"><span data-stu-id="1e489-475">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="1e489-476">Altri aspetti dell'esecuzione e dell'hosting di app ASP.NET Core vengono configurati usando file di configurazione non trattati in questo argomento:Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span><span class="sxs-lookup"><span data-stu-id="1e489-476">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="1e489-477">*launch.json*/*launchSettings.json* sono file di configurazione degli strumenti per l'ambiente di sviluppo, descritti di:</span><span class="sxs-lookup"><span data-stu-id="1e489-477">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="1e489-478">In <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="1e489-478">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="1e489-479">Nel set di documentazione in cui vengono usati i file per configurare ASP.NET app di base per gli scenari di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="1e489-479">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="1e489-480">*web.config* è un file di configurazione del server, descritto negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="1e489-480">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="1e489-481">Per ulteriori informazioni sulla migrazione della configurazione <xref:migration/proper-to-2x/index#store-configurations>dell'app da versioni precedenti di ASP.NET, vedere .</span><span class="sxs-lookup"><span data-stu-id="1e489-481">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="1e489-482">Aggiungere la configurazione da un assembly esterno</span><span class="sxs-lookup"><span data-stu-id="1e489-482">Add configuration from an external assembly</span></span>

<span data-ttu-id="1e489-483">Un'implementazione <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> consente l'aggiunta di miglioramenti a un'app all'avvio, da un assembly esterno alla classe `Startup` dell'app.</span><span class="sxs-lookup"><span data-stu-id="1e489-483">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="1e489-484">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="1e489-484">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1e489-485">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="1e489-485">Additional resources</span></span>

* [<span data-ttu-id="1e489-486">Codice sorgente di configurazione</span><span class="sxs-lookup"><span data-stu-id="1e489-486">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1e489-487">La configurazione delle app in ASP.NET Core si basa su coppie chiave-valore stabilite dai *provider di configurazione*.</span><span class="sxs-lookup"><span data-stu-id="1e489-487">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="1e489-488">I provider di configurazione leggono i dati di configurazione in coppie chiave-valore da un'ampia gamma di origini di configurazione:</span><span class="sxs-lookup"><span data-stu-id="1e489-488">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="1e489-489">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="1e489-489">Azure Key Vault</span></span>
* <span data-ttu-id="1e489-490">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="1e489-490">Azure App Configuration</span></span>
* <span data-ttu-id="1e489-491">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="1e489-491">Command-line arguments</span></span>
* <span data-ttu-id="1e489-492">Provider personalizzati (installati o creati)</span><span class="sxs-lookup"><span data-stu-id="1e489-492">Custom providers (installed or created)</span></span>
* <span data-ttu-id="1e489-493">File della directory</span><span class="sxs-lookup"><span data-stu-id="1e489-493">Directory files</span></span>
* <span data-ttu-id="1e489-494">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="1e489-494">Environment variables</span></span>
* <span data-ttu-id="1e489-495">Oggetti .NET in memoria</span><span class="sxs-lookup"><span data-stu-id="1e489-495">In-memory .NET objects</span></span>
* <span data-ttu-id="1e489-496">File di impostazioni</span><span class="sxs-lookup"><span data-stu-id="1e489-496">Settings files</span></span>

<span data-ttu-id="1e489-497">I pacchetti di configurazione per gli scenari di provider di configurazione comuni ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sono inclusi nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="1e489-497">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="1e489-498">Gli esempi di codice che seguono e quelli inclusi nell'app di esempio usano lo spazio dei nomi <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="1e489-498">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="1e489-499">Il *modello di opzioni* è un'estensione dei concetti di configurazione descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="1e489-499">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="1e489-500">Le opzioni usano le classi per rappresentare i gruppi di impostazioni correlate.</span><span class="sxs-lookup"><span data-stu-id="1e489-500">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="1e489-501">Per altre informazioni, vedere <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="1e489-501">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="1e489-502">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1e489-502">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="1e489-503">Host e configurazione delle app</span><span class="sxs-lookup"><span data-stu-id="1e489-503">Host versus app configuration</span></span>

<span data-ttu-id="1e489-504">Prima che l'app venga configurata e avviata, viene configurato e avviato un *host*.</span><span class="sxs-lookup"><span data-stu-id="1e489-504">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="1e489-505">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="1e489-505">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="1e489-506">Sia l'app che l'host vengono configurati tramite i provider di configurazione descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="1e489-506">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="1e489-507">Anche le coppie chiave-valore di configurazione dell'host sono incluse nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1e489-507">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="1e489-508">Per altre informazioni su come vengono usati i provider di configurazione quando viene creato l'host e sugli effetti delle origini di configurazione sull'host di configurazione, vedere <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="1e489-508">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="1e489-509">Altra configurazione</span><span class="sxs-lookup"><span data-stu-id="1e489-509">Other configuration</span></span>

<span data-ttu-id="1e489-510">Questo argomento riguarda solo la *configurazione dell'app*.</span><span class="sxs-lookup"><span data-stu-id="1e489-510">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="1e489-511">Altri aspetti dell'esecuzione e dell'hosting di app ASP.NET Core vengono configurati usando file di configurazione non trattati in questo argomento:Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span><span class="sxs-lookup"><span data-stu-id="1e489-511">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="1e489-512">*launch.json*/*launchSettings.json* sono file di configurazione degli strumenti per l'ambiente di sviluppo, descritti di:</span><span class="sxs-lookup"><span data-stu-id="1e489-512">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="1e489-513">In <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="1e489-513">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="1e489-514">Nel set di documentazione in cui vengono usati i file per configurare ASP.NET app di base per gli scenari di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="1e489-514">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="1e489-515">*web.config* è un file di configurazione del server, descritto negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="1e489-515">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="1e489-516">Per ulteriori informazioni sulla migrazione della configurazione <xref:migration/proper-to-2x/index#store-configurations>dell'app da versioni precedenti di ASP.NET, vedere .</span><span class="sxs-lookup"><span data-stu-id="1e489-516">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="1e489-517">Configurazione predefinita</span><span class="sxs-lookup"><span data-stu-id="1e489-517">Default configuration</span></span>

<span data-ttu-id="1e489-518">Le app basate sui modelli [dotnet new](/dotnet/core/tools/dotnet-new) di ASP.NET Core chiamano <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> durante la compilazione di un host.</span><span class="sxs-lookup"><span data-stu-id="1e489-518">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="1e489-519">`CreateDefaultBuilder` fornisce la configurazione predefinita per l'app nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="1e489-519">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="1e489-520">La configurazione seguente si applica alle app che usano l'[host Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="1e489-520">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="1e489-521">Per informazioni dettagliate sulla configurazione predefinita quando viene usato l'[host generico](xref:fundamentals/host/generic-host), vedere la [versione più recente di questo argomento](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="1e489-521">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="1e489-522">La configurazione dell'host viene specificata da:</span><span class="sxs-lookup"><span data-stu-id="1e489-522">Host configuration is provided from:</span></span>
  * <span data-ttu-id="1e489-523">Variabili di ambiente con prefisso `ASPNETCORE_` (ad esempio `ASPNETCORE_ENVIRONMENT`) che usano il [provider di configurazione delle variabili di ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="1e489-523">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="1e489-524">Il prefisso (`ASPNETCORE_`) viene rimosso al caricamento delle coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-524">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="1e489-525">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="1e489-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="1e489-526">La configurazione dell'app viene fornita da:</span><span class="sxs-lookup"><span data-stu-id="1e489-526">App configuration is provided from:</span></span>
  * <span data-ttu-id="1e489-527">*appsettings.json* mediante il [provider di configurazione dei file](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="1e489-527">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="1e489-528">*appsettings.{Ambiente}.json* mediante il [provider di configurazione dei file](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="1e489-528">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="1e489-529">[Strumento di gestione dei segreti](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development` usando l'assembly di ingresso.</span><span class="sxs-lookup"><span data-stu-id="1e489-529">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="1e489-530">Variabili di ambiente che usano il [provider di configurazione delle variabili di ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="1e489-530">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="1e489-531">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="1e489-531">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="1e489-532">Sicurezza</span><span class="sxs-lookup"><span data-stu-id="1e489-532">Security</span></span>

<span data-ttu-id="1e489-533">Per proteggere i dati di configurazione sensibili, adottare le pratiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="1e489-533">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="1e489-534">Non archiviare mai la password o altri dati sensibili nel codice del provider di configurazione o in file di configurazione di testo normale.</span><span class="sxs-lookup"><span data-stu-id="1e489-534">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="1e489-535">Non usare i segreti di produzione in ambienti di sviluppo o di test.</span><span class="sxs-lookup"><span data-stu-id="1e489-535">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="1e489-536">Specificare i segreti all'esterno del progetto in modo che non possano essere inavvertitamente inviati a un repository del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="1e489-536">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="1e489-537">Per altre informazioni, vedere gli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="1e489-537">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="1e489-538"><xref:security/app-secrets>&ndash; Include consigli sull'utilizzo delle variabili di ambiente per archiviare dati sensibili.</span><span class="sxs-lookup"><span data-stu-id="1e489-538"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="1e489-539">Secret Manager usa il provider di configurazione dei file per archiviare i segreti utente in un file JSON nel sistema locale.</span><span class="sxs-lookup"><span data-stu-id="1e489-539">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="1e489-540">Il provider di configurazione dei file è descritto più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="1e489-540">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="1e489-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) archivia in modo sicuro i segreti delle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e489-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="1e489-542">Per altre informazioni, vedere <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="1e489-542">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="1e489-543">Dati di configurazione gerarchici</span><span class="sxs-lookup"><span data-stu-id="1e489-543">Hierarchical configuration data</span></span>

<span data-ttu-id="1e489-544">L'API di configurazione è in grado di mantenere i dati di configurazione gerarchici rendendoli flat grazie all'uso di un delimitatore nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-544">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="1e489-545">Nel file JSON seguente, esistono quattro chiavi in una gerarchia strutturata di due sezioni:</span><span class="sxs-lookup"><span data-stu-id="1e489-545">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="1e489-546">Quando il file viene letto nella configurazione, vengono create chiavi univoche per mantenere la struttura di dati gerarchici originale dell'origine di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-546">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="1e489-547">Le sezioni e le chiavi vengono rese flat usando due punti (`:`) per mantenere la struttura originale:</span><span class="sxs-lookup"><span data-stu-id="1e489-547">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="1e489-548">section0:key0</span><span class="sxs-lookup"><span data-stu-id="1e489-548">section0:key0</span></span>
* <span data-ttu-id="1e489-549">section0:key1</span><span class="sxs-lookup"><span data-stu-id="1e489-549">section0:key1</span></span>
* <span data-ttu-id="1e489-550">section1:key0</span><span class="sxs-lookup"><span data-stu-id="1e489-550">section1:key0</span></span>
* <span data-ttu-id="1e489-551">section1:key1</span><span class="sxs-lookup"><span data-stu-id="1e489-551">section1:key1</span></span>

<span data-ttu-id="1e489-552">I metodi <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sono disponibili per isolare le sezioni e gli elementi figlio di una sezione nei dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="1e489-553">Questi metodi sono descritti più avanti in [GetSection, GetChildren ed Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="1e489-553">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="1e489-554">Convenzioni</span><span class="sxs-lookup"><span data-stu-id="1e489-554">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="1e489-555">Origini e provider</span><span class="sxs-lookup"><span data-stu-id="1e489-555">Sources and providers</span></span>

<span data-ttu-id="1e489-556">All'avvio dell'app, le origini di configurazione vengono lette nell'ordine con cui sono specificati i rispettivi provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-556">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="1e489-557">I provider di configurazione che implementano il rilevamento delle modifiche sono in grado di ricaricare la configurazione quando viene modificata un'impostazione sottostante.</span><span class="sxs-lookup"><span data-stu-id="1e489-557">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="1e489-558">Ad esempio, il provider di configurazione dei file (descritto più avanti in questo argomento) e il [provider di configurazione di Azure Key Vault](xref:security/key-vault-configuration) implementano il rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="1e489-558">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="1e489-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> è disponibile nel contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) dell'app.</span><span class="sxs-lookup"><span data-stu-id="1e489-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="1e489-560"><xref:Microsoft.Extensions.Configuration.IConfiguration>può essere inserito in <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> un <xref:Microsoft.AspNetCore.Mvc.Controller> Razor Pages o MVC per ottenere la configurazione per la classe.</span><span class="sxs-lookup"><span data-stu-id="1e489-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="1e489-561">Negli esempi seguenti, `_config` il campo viene utilizzato per accedere ai valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="1e489-561">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="1e489-562">I provider di configurazione non possono usare l'inserimento delle dipendenze, perché non è disponibile quando vengono configurati dall'host.</span><span class="sxs-lookup"><span data-stu-id="1e489-562">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="1e489-563">Chiavi</span><span class="sxs-lookup"><span data-stu-id="1e489-563">Keys</span></span>

<span data-ttu-id="1e489-564">Le chiavi di configurazione adottano le convenzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1e489-564">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="1e489-565">Per le chiavi non viene fatta distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="1e489-565">Keys are case-insensitive.</span></span> <span data-ttu-id="1e489-566">Ad esempio, `ConnectionString` e `connectionstring` vengono considerate chiavi equivalenti.</span><span class="sxs-lookup"><span data-stu-id="1e489-566">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="1e489-567">Se un valore per la stessa chiave viene impostato dallo stesso provider di configurazione o da provider diversi, viene usato l'ultimo valore impostato per la chiave.</span><span class="sxs-lookup"><span data-stu-id="1e489-567">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="1e489-568">Chiavi gerarchiche</span><span class="sxs-lookup"><span data-stu-id="1e489-568">Hierarchical keys</span></span>
  * <span data-ttu-id="1e489-569">Nell'ambito dell'API di configurazione, il separatore due punti (`:`) funziona in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="1e489-569">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="1e489-570">Nelle variabili di ambiente, un separatore due punti potrebbe non funzionare in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="1e489-570">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="1e489-571">Il doppio carattere di sottolineatura (`__`) è supportato da tutte le piattaforme e viene convertito automaticamente nei due punti.</span><span class="sxs-lookup"><span data-stu-id="1e489-571">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="1e489-572">In Azure Key Vault, le chiavi gerarchiche usano `--` (due trattini) come separatore.</span><span class="sxs-lookup"><span data-stu-id="1e489-572">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="1e489-573">Scrivi il codice per sostituire i trattini con i due punti quando i segreti vengono caricati nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1e489-573">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="1e489-574">Il <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supporta l'associazione di matrici agli oggetti usando gli indici delle matrici nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-574">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="1e489-575">L'associazione di matrici è descritta nella sezione [Associare una matrice a una classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="1e489-575">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="1e489-576">Valori</span><span class="sxs-lookup"><span data-stu-id="1e489-576">Values</span></span>

<span data-ttu-id="1e489-577">I valori di configurazione adottano le convenzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1e489-577">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="1e489-578">I valori sono stringhe.</span><span class="sxs-lookup"><span data-stu-id="1e489-578">Values are strings.</span></span>
* <span data-ttu-id="1e489-579">I valori null non possono essere archiviati nella configurazione o associati a oggetti.</span><span class="sxs-lookup"><span data-stu-id="1e489-579">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="1e489-580">Providers</span><span class="sxs-lookup"><span data-stu-id="1e489-580">Providers</span></span>

<span data-ttu-id="1e489-581">La tabella seguente mostra i provider di configurazione disponibili per le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e489-581">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="1e489-582">Provider</span><span class="sxs-lookup"><span data-stu-id="1e489-582">Provider</span></span> | <span data-ttu-id="1e489-583">Fornisce la configurazione da&hellip;</span><span class="sxs-lookup"><span data-stu-id="1e489-583">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="1e489-584">[Provider di configurazione di Azure Key Vault](xref:security/key-vault-configuration) (argomenti *Sicurezza*)</span><span class="sxs-lookup"><span data-stu-id="1e489-584">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="1e489-585">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="1e489-585">Azure Key Vault</span></span> |
| <span data-ttu-id="1e489-586">[Provider di Configurazione app](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Documentazione di Azure)</span><span class="sxs-lookup"><span data-stu-id="1e489-586">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="1e489-587">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="1e489-587">Azure App Configuration</span></span> |
| [<span data-ttu-id="1e489-588">Provider di configurazione della riga di comando</span><span class="sxs-lookup"><span data-stu-id="1e489-588">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="1e489-589">Parametri della riga di comando</span><span class="sxs-lookup"><span data-stu-id="1e489-589">Command-line parameters</span></span> |
| [<span data-ttu-id="1e489-590">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="1e489-590">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="1e489-591">Origine personalizzata</span><span class="sxs-lookup"><span data-stu-id="1e489-591">Custom source</span></span> |
| [<span data-ttu-id="1e489-592">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="1e489-592">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="1e489-593">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="1e489-593">Environment variables</span></span> |
| [<span data-ttu-id="1e489-594">Provider di configurazione file</span><span class="sxs-lookup"><span data-stu-id="1e489-594">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="1e489-595">File (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="1e489-595">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="1e489-596">Provider di configurazione KeyPerFile</span><span class="sxs-lookup"><span data-stu-id="1e489-596">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="1e489-597">File della directory</span><span class="sxs-lookup"><span data-stu-id="1e489-597">Directory files</span></span> |
| [<span data-ttu-id="1e489-598">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="1e489-598">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="1e489-599">Raccolte in memoria</span><span class="sxs-lookup"><span data-stu-id="1e489-599">In-memory collections</span></span> |
| <span data-ttu-id="1e489-600">[Segreti utente (Secret Manager)](xref:security/app-secrets) (argomenti *Sicurezza*)</span><span class="sxs-lookup"><span data-stu-id="1e489-600">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="1e489-601">File nella directory dei profili utente</span><span class="sxs-lookup"><span data-stu-id="1e489-601">File in the user profile directory</span></span> |

<span data-ttu-id="1e489-602">Le origini di configurazione vengono lette nell'ordine in cui vengono specificati i rispetti provider di configurazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="1e489-602">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="1e489-603">I provider di configurazione descritti in questo argomento sono descritti in ordine alfabetico, non nell'ordine in cui sono disposti dal codice.</span><span class="sxs-lookup"><span data-stu-id="1e489-603">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="1e489-604">Ordina i provider di configurazione nel codice in base alle priorità per le origini di configurazione sottostanti che l'app richiede.</span><span class="sxs-lookup"><span data-stu-id="1e489-604">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="1e489-605">Una sequenza tipica di provider di configurazione è:</span><span class="sxs-lookup"><span data-stu-id="1e489-605">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="1e489-606">File (*appsettings.json*, *appsettings. Ambiente: .json* `{Environment}` , dove è l'ambiente di hosting corrente dell'app)</span><span class="sxs-lookup"><span data-stu-id="1e489-606">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="1e489-607">Archivio chiavi di AzureAzure Key Vault</span><span class="sxs-lookup"><span data-stu-id="1e489-607">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="1e489-608">[Segreti utente (Secret Manager)](xref:security/app-secrets) (solo nell'ambiente di sviluppo)</span><span class="sxs-lookup"><span data-stu-id="1e489-608">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="1e489-609">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="1e489-609">Environment variables</span></span>
1. <span data-ttu-id="1e489-610">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="1e489-610">Command-line arguments</span></span>

<span data-ttu-id="1e489-611">È pratica comune posizionare il provider di configurazione della riga di comando per ultimo in una serie di provider per consentire agli argomenti della riga di comando di sostituire la configurazione impostata da altri provider.</span><span class="sxs-lookup"><span data-stu-id="1e489-611">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="1e489-612">La sequenza precedente di provider viene utilizzata quando `CreateDefaultBuilder`un nuovo generatore host viene inizializzato con .</span><span class="sxs-lookup"><span data-stu-id="1e489-612">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="1e489-613">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="1e489-613">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="1e489-614">Configurare il generatore di host con UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="1e489-614">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="1e489-615">Per configurare il generatore di host, chiamare <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> sul generatore di host con la configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-615">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="1e489-616">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="1e489-616">ConfigureAppConfiguration</span></span>

<span data-ttu-id="1e489-617">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare i provider di configurazione dell'app, oltre a quelli aggiunti automaticamente da `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="1e489-617">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="1e489-618">Sostituire la configurazione precedente con argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="1e489-618">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="1e489-619">Per fornire la configurazione dell'app che può essere sostituita con argomenti della riga di comando, chiamare `AddCommandLine` per ultimo:</span><span class="sxs-lookup"><span data-stu-id="1e489-619">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="1e489-620">Rimuovere i provider aggiunti da CreateDefaultBuilderRemove providers added by CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="1e489-620">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="1e489-621">Per rimuovere i `CreateDefaultBuilder`provider aggiunti da , chiamare prima [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) su [IConfigurationBuilder.Sources:](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources)</span><span class="sxs-lookup"><span data-stu-id="1e489-621">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="1e489-622">Utilizzare la configurazione durante l'avvio dell'app</span><span class="sxs-lookup"><span data-stu-id="1e489-622">Consume configuration during app startup</span></span>

<span data-ttu-id="1e489-623">La configurazione fornita all'app in `ConfigureAppConfiguration` è disponibile durante l'avvio dell'app, incluso `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1e489-623">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1e489-624">Per altre informazioni, vedere la sezione [Accedere alla configurazione durante l'avvio](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="1e489-624">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="1e489-625">Provider di configurazione della riga di comando</span><span class="sxs-lookup"><span data-stu-id="1e489-625">Command-line Configuration Provider</span></span>

<span data-ttu-id="1e489-626"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carica la configurazione da coppie chiave-valore di argomenti della riga di comando in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="1e489-626">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="1e489-627">Per attivare la configurazione della riga di comando, metodo di estensione <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> viene chiamato su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="1e489-627">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="1e489-628">`AddCommandLine` viene chiamato automaticamente quando viene chiamato il metodo `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="1e489-628">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="1e489-629">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="1e489-629">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="1e489-630">`CreateDefaultBuilder` carica anche:</span><span class="sxs-lookup"><span data-stu-id="1e489-630">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="1e489-631">Una configurazione facoltativa dai file *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="1e489-631">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="1e489-632">[Segreti utente (Secret Manager)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="1e489-632">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="1e489-633">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="1e489-633">Environment variables.</span></span>

<span data-ttu-id="1e489-634">`CreateDefaultBuilder` aggiunge il provider di configurazione della riga di comando per ultimo.</span><span class="sxs-lookup"><span data-stu-id="1e489-634">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="1e489-635">Gli argomenti della riga di comando passati in fase di esecuzione sostituiscono la configurazione impostata dagli altri provider.</span><span class="sxs-lookup"><span data-stu-id="1e489-635">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="1e489-636">`CreateDefaultBuilder` opera durante la creazione dell'host.</span><span class="sxs-lookup"><span data-stu-id="1e489-636">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="1e489-637">Pertanto, la configurazione della riga di comando attivata da `CreateDefaultBuilder` può influire sul modo in cui l'host viene configurato.</span><span class="sxs-lookup"><span data-stu-id="1e489-637">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="1e489-638">Per le app basate sui modelli di ASP.NET Core, `AddCommandLine` è già stato chiamato da `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1e489-638">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="1e489-639">Per aggiungere altri provider di configurazione e mantenere la possibilità di sostituire la configurazione di tali provider con argomenti della riga di comando, chiamare i provider aggiuntivi dell'app in `ConfigureAppConfiguration` e quindi chiamare `AddCommandLine` per ultimo.</span><span class="sxs-lookup"><span data-stu-id="1e489-639">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="1e489-640">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="1e489-640">**Example**</span></span>

<span data-ttu-id="1e489-641">L'app di esempio consente di sfruttare il metodo di servizio statico `CreateDefaultBuilder` per creare l'host, che include una chiamata a <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="1e489-641">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="1e489-642">Aprire un prompt dei comandi nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="1e489-642">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="1e489-643">Fornire un argomento della riga di comando per il comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="1e489-643">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="1e489-644">Dopo che l'app è in esecuzione, aprire un browser per l'app all'indirizzo `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="1e489-644">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="1e489-645">Notare che l'output contiene la coppia chiave-valore per l'argomento della riga di comando di configurazione fornito per `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="1e489-645">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="1e489-646">Argomenti</span><span class="sxs-lookup"><span data-stu-id="1e489-646">Arguments</span></span>

<span data-ttu-id="1e489-647">Il valore deve seguire un segno di uguale (`=`) o la chiave deve avere un prefisso (`--` o `/`) quando il valore segue uno spazio.</span><span class="sxs-lookup"><span data-stu-id="1e489-647">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="1e489-648">Il valore non è necessario se viene usato un segno di uguale, ad esempio `CommandLineKey=`.</span><span class="sxs-lookup"><span data-stu-id="1e489-648">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="1e489-649">Prefisso chiave</span><span class="sxs-lookup"><span data-stu-id="1e489-649">Key prefix</span></span>               | <span data-ttu-id="1e489-650">Esempio</span><span class="sxs-lookup"><span data-stu-id="1e489-650">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="1e489-651">Nessun prefisso</span><span class="sxs-lookup"><span data-stu-id="1e489-651">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="1e489-652">Due trattini (`--`)</span><span class="sxs-lookup"><span data-stu-id="1e489-652">Two dashes (`--`)</span></span>        | <span data-ttu-id="1e489-653">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="1e489-653">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="1e489-654">Barra (`/`)</span><span class="sxs-lookup"><span data-stu-id="1e489-654">Forward slash (`/`)</span></span>      | <span data-ttu-id="1e489-655">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="1e489-655">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="1e489-656">Nello stesso comando, non mischiare coppie chiave-valore di argomenti della riga di comando che usano un segno di uguale con coppie chiave-valore che usano uno spazio.</span><span class="sxs-lookup"><span data-stu-id="1e489-656">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="1e489-657">Comandi di esempio:</span><span class="sxs-lookup"><span data-stu-id="1e489-657">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="1e489-658">Mapping di sostituzione</span><span class="sxs-lookup"><span data-stu-id="1e489-658">Switch mappings</span></span>

<span data-ttu-id="1e489-659">I mapping di sostituzione consentono di usare la logica di sostituzione del nome della chiave.</span><span class="sxs-lookup"><span data-stu-id="1e489-659">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="1e489-660">Quando si compila <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>manualmente la configurazione con un <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> oggetto , fornire un dizionario di sostituzioni di switch al metodo .</span><span class="sxs-lookup"><span data-stu-id="1e489-660">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="1e489-661">Quando viene utilizzato il dizionario dei mapping di sostituzione, nel dizionario viene controllata la presenza di una chiave corrispondente alla chiave fornita da un argomento della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="1e489-661">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="1e489-662">Se la chiave della riga di comando viene trovata nel dizionario, il valore del dizionario (la sostituzione della chiave) viene passato nuovamente per impostare la coppia chiave-valore nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1e489-662">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="1e489-663">Un mapping di sostituzione è necessario per le chiavi della riga di comando con un trattino singolo (`-`) come prefisso.</span><span class="sxs-lookup"><span data-stu-id="1e489-663">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="1e489-664">Regole principali del dizionario dei mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="1e489-664">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="1e489-665">Le sostituzioni devono iniziare con un trattino (`-`) o un trattino doppio (`--`).</span><span class="sxs-lookup"><span data-stu-id="1e489-665">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="1e489-666">Il dizionario dei mapping di sostituzione non deve contenere chiavi duplicate.</span><span class="sxs-lookup"><span data-stu-id="1e489-666">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="1e489-667">Creare un dizionario dei mapping di sostituzione.</span><span class="sxs-lookup"><span data-stu-id="1e489-667">Create a switch mappings dictionary.</span></span> <span data-ttu-id="1e489-668">Nell'esempio seguente vengono creati due mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="1e489-668">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="1e489-669">Quando viene creato l'host, chiamare `AddCommandLine` con il dizionario dei mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="1e489-669">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="1e489-670">Per le app che usano i mapping di sostituzione, la chiamata a `CreateDefaultBuilder` non deve passare argomenti.</span><span class="sxs-lookup"><span data-stu-id="1e489-670">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="1e489-671">La chiamata `AddCommandLine` del metodo `CreateDefaultBuilder` non include sostituzioni mappate e non è possibile passare il dizionario dei mapping di sostituzione a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1e489-671">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="1e489-672">La soluzione consiste nel non passare gli argomenti a `CreateDefaultBuilder` consentendo invece al metodo `AddCommandLine` del metodo `ConfigurationBuilder` di elaborare entrambi gli argomenti e il dizionario dei mapping di sostituzione.</span><span class="sxs-lookup"><span data-stu-id="1e489-672">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="1e489-673">Il dizionario dei mapping di sostituzione creato contiene i dati visualizzati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="1e489-673">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="1e489-674">Chiave</span><span class="sxs-lookup"><span data-stu-id="1e489-674">Key</span></span>       | <span data-ttu-id="1e489-675">valore</span><span class="sxs-lookup"><span data-stu-id="1e489-675">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="1e489-676">Se le chiavi con mapping di sostituzione vengono usate all'avvio dell'app, la configurazione riceve il valore di configurazione per la chiave fornita dal dizionario:</span><span class="sxs-lookup"><span data-stu-id="1e489-676">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="1e489-677">Dopo aver eseguito il comando precedente, la configurazione contiene i valori mostrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="1e489-677">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="1e489-678">Chiave</span><span class="sxs-lookup"><span data-stu-id="1e489-678">Key</span></span>               | <span data-ttu-id="1e489-679">valore</span><span class="sxs-lookup"><span data-stu-id="1e489-679">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="1e489-680">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="1e489-680">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="1e489-681"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carica la configurazione da coppie chiave-valore di variabili di ambiente in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="1e489-681">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="1e489-682">Per attivare la configurazione delle variabili di ambiente, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="1e489-682">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="1e489-683">[Il servizio app di Azure](https://azure.microsoft.com/services/app-service/) consente di impostare le variabili di ambiente nel portale di Azure che possono eseguire l'override della configurazione dell'app usando il provider di configurazione delle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="1e489-683">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="1e489-684">Per altre informazioni, vedere [App di Azure: Eseguire l'override della configurazione delle app usando il portale di Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="1e489-684">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="1e489-685">`AddEnvironmentVariables` consente di caricare variabili di ambiente con prefisso `ASPNETCORE_` per la [configurazione host](#host-versus-app-configuration) quando viene inizializzato un nuovo generatore di host con l'[host Web](xref:fundamentals/host/web-host) e viene chiamato `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1e489-685">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="1e489-686">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="1e489-686">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="1e489-687">`CreateDefaultBuilder` carica anche:</span><span class="sxs-lookup"><span data-stu-id="1e489-687">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="1e489-688">Configurazione delle app dalle variabili di ambiente senza prefisso chiamando `AddEnvironmentVariables` senza prefisso.</span><span class="sxs-lookup"><span data-stu-id="1e489-688">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="1e489-689">Una configurazione facoltativa dai file *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="1e489-689">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="1e489-690">[Segreti utente (Secret Manager)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="1e489-690">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="1e489-691">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="1e489-691">Command-line arguments.</span></span>

<span data-ttu-id="1e489-692">Il provider di configurazione delle variabili di ambiente viene chiamato dopo aver stabilito la configurazione dai segreti utente e dai file *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="1e489-692">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="1e489-693">La chiamata del provider in questa posizione consente alle variabili di ambiente lette in fase di esecuzione di sostituire la configurazione impostata dai segreti utente e dai file *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="1e489-693">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="1e489-694">Per fornire la configurazione dell'app da variabili di `ConfigureAppConfiguration` ambiente `AddEnvironmentVariables` aggiuntive, chiama i provider aggiuntivi dell'app e chiama con il prefisso:</span><span class="sxs-lookup"><span data-stu-id="1e489-694">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="1e489-695">Chiamare `AddEnvironmentVariables` last per consentire alle variabili di ambiente con il prefisso specificato di eseguire l'override dei valori da altri provider.</span><span class="sxs-lookup"><span data-stu-id="1e489-695">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="1e489-696">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="1e489-696">**Example**</span></span>

<span data-ttu-id="1e489-697">L'app di esempio consente di sfruttare il metodo di servizio statico `CreateDefaultBuilder` per creare l'host, che include una chiamata a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="1e489-697">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="1e489-698">Eseguire l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="1e489-698">Run the sample app.</span></span> <span data-ttu-id="1e489-699">Aprire un browser per l'app all'indirizzo `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="1e489-699">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="1e489-700">Notare che l'output contiene la coppia chiave-valore per la variabile di ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="1e489-700">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="1e489-701">Il valore riflette l'ambiente in cui l'app è in esecuzione, in genere `Development` durante l'esecuzione locale.</span><span class="sxs-lookup"><span data-stu-id="1e489-701">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="1e489-702">Per limitare l'elenco delle variabili di ambiente restituito dall'app, l'app filtra le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="1e489-702">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="1e489-703">Vedere il file *Pages/Index.cshtml.cs* dell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="1e489-703">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="1e489-704">Per esporre tutte le variabili di ambiente `FilteredConfiguration` disponibili per l'app, modificare il in *Pages/Index.cshtml.cs* come segue:</span><span class="sxs-lookup"><span data-stu-id="1e489-704">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="1e489-705">Prefissi</span><span class="sxs-lookup"><span data-stu-id="1e489-705">Prefixes</span></span>

<span data-ttu-id="1e489-706">Le variabili di ambiente caricate nella configurazione dell'app `AddEnvironmentVariables` vengono filtrate quando si specifica un prefisso al metodo.</span><span class="sxs-lookup"><span data-stu-id="1e489-706">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="1e489-707">Ad esempio, per filtrare le variabili di ambiente in base al prefisso `CUSTOM_`, fornire il prefisso al provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="1e489-707">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="1e489-708">Il prefisso viene rimosso quando vengono create le coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-708">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="1e489-709">Quando viene creato il generatore di host, la configurazione dell'host viene fornita dalle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="1e489-709">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="1e489-710">Per altre informazioni sul prefisso usato per queste variabili di ambiente, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="1e489-710">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="1e489-711">**Prefissi della stringa di connessione**</span><span class="sxs-lookup"><span data-stu-id="1e489-711">**Connection string prefixes**</span></span>

<span data-ttu-id="1e489-712">L'API di configurazione prevede regole di elaborazione speciali per quattro variabili di ambiente di stringa di connessione coinvolte nella configurazione di stringhe di connessione di Azure per l'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="1e489-712">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="1e489-713">Le variabili di ambiente con i prefissi indicati nella tabella vengono caricate nell'app se non viene fornito alcun prefisso a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="1e489-713">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="1e489-714">Prefisso della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="1e489-714">Connection string prefix</span></span> | <span data-ttu-id="1e489-715">Provider</span><span class="sxs-lookup"><span data-stu-id="1e489-715">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="1e489-716">Provider personalizzato</span><span class="sxs-lookup"><span data-stu-id="1e489-716">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="1e489-717">MySQL</span><span class="sxs-lookup"><span data-stu-id="1e489-717">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="1e489-718">Database SQL di Azure</span><span class="sxs-lookup"><span data-stu-id="1e489-718">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="1e489-719">SQL Server</span><span class="sxs-lookup"><span data-stu-id="1e489-719">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="1e489-720">Quando una variabile di ambiente viene individuata e caricata nella configurazione con uno qualsiasi dei quattro prefissi indicati nella tabella:</span><span class="sxs-lookup"><span data-stu-id="1e489-720">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="1e489-721">La chiave di configurazione viene creata rimuovendo il prefisso della variabile di ambiente e aggiungendo una sezione per la chiave di configurazione (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="1e489-721">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="1e489-722">Viene creata una nuova coppia chiave-valore della configurazione che rappresenta il provider di connessione al database (ad eccezione di `CUSTOMCONNSTR_`, che non ha un provider dichiarato).</span><span class="sxs-lookup"><span data-stu-id="1e489-722">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="1e489-723">Chiave della variabile di ambiente</span><span class="sxs-lookup"><span data-stu-id="1e489-723">Environment variable key</span></span> | <span data-ttu-id="1e489-724">Chiave di configurazione convertita</span><span class="sxs-lookup"><span data-stu-id="1e489-724">Converted configuration key</span></span> | <span data-ttu-id="1e489-725">Voce di configurazione del provider</span><span class="sxs-lookup"><span data-stu-id="1e489-725">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="1e489-726">Voce di configurazione non creata.</span><span class="sxs-lookup"><span data-stu-id="1e489-726">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="1e489-727">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="1e489-727">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="1e489-728">Valore: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="1e489-728">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="1e489-729">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="1e489-729">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="1e489-730">Valore: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="1e489-730">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="1e489-731">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="1e489-731">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="1e489-732">Valore: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="1e489-732">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="1e489-733">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="1e489-733">**Example**</span></span>

<span data-ttu-id="1e489-734">Nel server viene creata una variabile di ambiente della stringa di connessione personalizzata:A custom connection string environment variable is created on the server:</span><span class="sxs-lookup"><span data-stu-id="1e489-734">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="1e489-735">Nome &ndash;`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="1e489-735">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="1e489-736">Valore &ndash;`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="1e489-736">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="1e489-737">Se `IConfiguration` viene inserito e assegnato `_config`a un campo denominato , leggere il valore:</span><span class="sxs-lookup"><span data-stu-id="1e489-737">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="1e489-738">Provider di configurazione dei file</span><span class="sxs-lookup"><span data-stu-id="1e489-738">File Configuration Provider</span></span>

<span data-ttu-id="1e489-739"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> è la classe base per il caricamento della configurazione dal file system.</span><span class="sxs-lookup"><span data-stu-id="1e489-739"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="1e489-740">I provider di configurazione seguenti sono dedicati per specifici tipi di file:</span><span class="sxs-lookup"><span data-stu-id="1e489-740">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="1e489-741">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="1e489-741">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="1e489-742">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="1e489-742">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="1e489-743">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="1e489-743">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="1e489-744">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="1e489-744">INI Configuration Provider</span></span>

<span data-ttu-id="1e489-745"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carica la configurazione da coppie chiave-valore di file INI in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="1e489-745">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="1e489-746">Per attivare la configurazione di file INI, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="1e489-746">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="1e489-747">I due punti possono essere usati come delimitatore di sezione nella configurazione di file INI.</span><span class="sxs-lookup"><span data-stu-id="1e489-747">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="1e489-748">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="1e489-748">Overloads permit specifying:</span></span>

* <span data-ttu-id="1e489-749">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="1e489-749">Whether the file is optional.</span></span>
* <span data-ttu-id="1e489-750">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="1e489-750">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="1e489-751">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider> usato per accedere al file.</span><span class="sxs-lookup"><span data-stu-id="1e489-751">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="1e489-752">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="1e489-752">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="1e489-753">Un esempio generico di un file di configurazione INI:</span><span class="sxs-lookup"><span data-stu-id="1e489-753">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="1e489-754">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="1e489-754">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="1e489-755">section0:key0</span><span class="sxs-lookup"><span data-stu-id="1e489-755">section0:key0</span></span>
* <span data-ttu-id="1e489-756">section0:key1</span><span class="sxs-lookup"><span data-stu-id="1e489-756">section0:key1</span></span>
* <span data-ttu-id="1e489-757">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="1e489-757">section1:subsection:key</span></span>
* <span data-ttu-id="1e489-758">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="1e489-758">section2:subsection0:key</span></span>
* <span data-ttu-id="1e489-759">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="1e489-759">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="1e489-760">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="1e489-760">JSON Configuration Provider</span></span>

<span data-ttu-id="1e489-761">Il <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carica la configurazione da coppie chiave-valore di file JSON in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="1e489-761">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="1e489-762">Per attivare la configurazione di file JSON, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="1e489-762">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="1e489-763">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="1e489-763">Overloads permit specifying:</span></span>

* <span data-ttu-id="1e489-764">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="1e489-764">Whether the file is optional.</span></span>
* <span data-ttu-id="1e489-765">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="1e489-765">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="1e489-766">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider> usato per accedere al file.</span><span class="sxs-lookup"><span data-stu-id="1e489-766">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="1e489-767">`AddJsonFile`viene chiamato automaticamente due volte quando un `CreateDefaultBuilder`nuovo generatore host viene inizializzato con .</span><span class="sxs-lookup"><span data-stu-id="1e489-767">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="1e489-768">Il metodo viene chiamato per caricare la configurazione da:</span><span class="sxs-lookup"><span data-stu-id="1e489-768">The method is called to load configuration from:</span></span>

* <span data-ttu-id="1e489-769">*appsettings.json* &ndash; Questo file viene letto per primo.</span><span class="sxs-lookup"><span data-stu-id="1e489-769">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="1e489-770">La versione dell'ambiente del file può sostituire i valori forniti dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1e489-770">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="1e489-771">*impostazioni dell'app. La* versione dell'ambiente del file viene caricata in base a [IHostingEnvironment.EnvironmentName .](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) &ndash;</span><span class="sxs-lookup"><span data-stu-id="1e489-771">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="1e489-772">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="1e489-772">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="1e489-773">`CreateDefaultBuilder` carica anche:</span><span class="sxs-lookup"><span data-stu-id="1e489-773">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="1e489-774">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="1e489-774">Environment variables.</span></span>
* <span data-ttu-id="1e489-775">[Segreti utente (Secret Manager)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="1e489-775">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="1e489-776">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="1e489-776">Command-line arguments.</span></span>

<span data-ttu-id="1e489-777">Il provider di configurazione JSON viene stabilito per primo.</span><span class="sxs-lookup"><span data-stu-id="1e489-777">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="1e489-778">I segreti utente, le variabili di ambiente e gli argomenti della riga di comando sostituiscono quindi la configurazione impostata dai file *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="1e489-778">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="1e489-779">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app per file diversi da *appsettings.json* e *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="1e489-779">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="1e489-780">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="1e489-780">**Example**</span></span>

<span data-ttu-id="1e489-781">L'app di esempio sfrutta `CreateDefaultBuilder` il metodo pratico statico per `AddJsonFile`compilare l'host, che include due chiamate a :</span><span class="sxs-lookup"><span data-stu-id="1e489-781">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="1e489-782">La prima `AddJsonFile` chiamata a carica la configurazione da *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="1e489-782">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="1e489-783">La seconda `AddJsonFile` chiamata per caricare la configurazione dalle *impostazioni dell'app. Ambiente: .json*.</span><span class="sxs-lookup"><span data-stu-id="1e489-783">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="1e489-784">Per *le impostazioni delle app. Development.json* nell'app di esempio, viene caricato il file seguente:Development.json in the sample app, the following file is loaded:</span><span class="sxs-lookup"><span data-stu-id="1e489-784">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="1e489-785">Eseguire l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="1e489-785">Run the sample app.</span></span> <span data-ttu-id="1e489-786">Aprire un browser per l'app all'indirizzo `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="1e489-786">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="1e489-787">L'output contiene coppie chiave-valore per la configurazione in base all'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="1e489-787">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="1e489-788">Il livello di `Logging:LogLevel:Default` registrazione `Debug` per la chiave è quando si esegue l'app nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="1e489-788">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="1e489-789">Eseguire di nuovo l'app di esempio nell'ambiente di produzione:Run the sample app again in the Production environment:</span><span class="sxs-lookup"><span data-stu-id="1e489-789">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="1e489-790">Aprire il file *Properties/launchSettings.json.*</span><span class="sxs-lookup"><span data-stu-id="1e489-790">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="1e489-791">Nel `ConfigurationSample` profilo modificare il valore `ASPNETCORE_ENVIRONMENT` della `Production`variabile di ambiente in .</span><span class="sxs-lookup"><span data-stu-id="1e489-791">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="1e489-792">Salvare il file ed `dotnet run` eseguire l'app con in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="1e489-792">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="1e489-793">Le impostazioni nelle *impostazioni dell'app. Development.json* non esegue più l'override delle impostazioni in *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1e489-793">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="1e489-794">Il livello di `Logging:LogLevel:Default` registrazione `Warning`per la chiave è .</span><span class="sxs-lookup"><span data-stu-id="1e489-794">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="1e489-795">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="1e489-795">XML Configuration Provider</span></span>

<span data-ttu-id="1e489-796">Il <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carica la configurazione da coppie chiave-valore di file XML in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="1e489-796">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="1e489-797">Per attivare la configurazione di file XML, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="1e489-797">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="1e489-798">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="1e489-798">Overloads permit specifying:</span></span>

* <span data-ttu-id="1e489-799">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="1e489-799">Whether the file is optional.</span></span>
* <span data-ttu-id="1e489-800">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="1e489-800">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="1e489-801">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider> usato per accedere al file.</span><span class="sxs-lookup"><span data-stu-id="1e489-801">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="1e489-802">Il nodo radice del file di configurazione viene ignorato quando vengono create le coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-802">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="1e489-803">Non specificare una definizione DTD (Document Type Definition) o uno spazio dei nomi nel file.</span><span class="sxs-lookup"><span data-stu-id="1e489-803">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="1e489-804">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="1e489-804">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="1e489-805">I file di configurazione XML possono usare nomi di elementi distinti per le sezioni ripetute:</span><span class="sxs-lookup"><span data-stu-id="1e489-805">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="1e489-806">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="1e489-806">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="1e489-807">section0:key0</span><span class="sxs-lookup"><span data-stu-id="1e489-807">section0:key0</span></span>
* <span data-ttu-id="1e489-808">section0:key1</span><span class="sxs-lookup"><span data-stu-id="1e489-808">section0:key1</span></span>
* <span data-ttu-id="1e489-809">section1:key0</span><span class="sxs-lookup"><span data-stu-id="1e489-809">section1:key0</span></span>
* <span data-ttu-id="1e489-810">section1:key1</span><span class="sxs-lookup"><span data-stu-id="1e489-810">section1:key1</span></span>

<span data-ttu-id="1e489-811">La ripetizione di elementi che usano lo stesso nome di elemento funziona se si usa l'attributo `name` per distinguere gli elementi:</span><span class="sxs-lookup"><span data-stu-id="1e489-811">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="1e489-812">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="1e489-812">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="1e489-813">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="1e489-813">section:section0:key:key0</span></span>
* <span data-ttu-id="1e489-814">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="1e489-814">section:section0:key:key1</span></span>
* <span data-ttu-id="1e489-815">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="1e489-815">section:section1:key:key0</span></span>
* <span data-ttu-id="1e489-816">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="1e489-816">section:section1:key:key1</span></span>

<span data-ttu-id="1e489-817">È possibile usare attributi per fornire valori:</span><span class="sxs-lookup"><span data-stu-id="1e489-817">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="1e489-818">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="1e489-818">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="1e489-819">key:attribute</span><span class="sxs-lookup"><span data-stu-id="1e489-819">key:attribute</span></span>
* <span data-ttu-id="1e489-820">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="1e489-820">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="1e489-821">Provider di configurazione KeyPerFile</span><span class="sxs-lookup"><span data-stu-id="1e489-821">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="1e489-822">Il <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa i file di una directory come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-822">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="1e489-823">La chiave è il nome del file.</span><span class="sxs-lookup"><span data-stu-id="1e489-823">The key is the file name.</span></span> <span data-ttu-id="1e489-824">Il valore contiene il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="1e489-824">The value contains the file's contents.</span></span> <span data-ttu-id="1e489-825">Il provider di configurazione KeyPerFile viene usato in scenari di hosting Docker.</span><span class="sxs-lookup"><span data-stu-id="1e489-825">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="1e489-826">Per attivare la configurazione chiave-per-file, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="1e489-826">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="1e489-827">Il `directoryPath` per i file deve essere un percorso assoluto.</span><span class="sxs-lookup"><span data-stu-id="1e489-827">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="1e489-828">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="1e489-828">Overloads permit specifying:</span></span>

* <span data-ttu-id="1e489-829">Un delegato `Action<KeyPerFileConfigurationSource>` che configura l'origine.</span><span class="sxs-lookup"><span data-stu-id="1e489-829">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="1e489-830">Se la directory è facoltativa e il percorso della directory.</span><span class="sxs-lookup"><span data-stu-id="1e489-830">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="1e489-831">Il doppio carattere di sottolineatura (`__`) viene usato come delimitatore per le chiavi di configurazione nei nomi dei file.</span><span class="sxs-lookup"><span data-stu-id="1e489-831">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="1e489-832">Ad esempio, il nome di file `Logging__LogLevel__System` produce la chiave di configurazione `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="1e489-832">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="1e489-833">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="1e489-833">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="1e489-834">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="1e489-834">Memory Configuration Provider</span></span>

<span data-ttu-id="1e489-835">Il <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una raccolta in memoria come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-835">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="1e489-836">Per attivare la configurazione della raccolta in memoria, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="1e489-836">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="1e489-837">Il provider di configurazione può essere inizializzato con un `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="1e489-837">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="1e489-838">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1e489-838">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="1e489-839">Nell'esempio seguente viene creato un dizionario di configurazione:</span><span class="sxs-lookup"><span data-stu-id="1e489-839">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="1e489-840">Il dizionario viene usato con una chiamata a `AddInMemoryCollection` per fornire la configurazione:</span><span class="sxs-lookup"><span data-stu-id="1e489-840">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="1e489-841">GetValue</span><span class="sxs-lookup"><span data-stu-id="1e489-841">GetValue</span></span>

<span data-ttu-id="1e489-842">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)estrae un singolo valore dalla configurazione con una chiave specificata e lo converte nel tipo non di raccolta specificato.</span><span class="sxs-lookup"><span data-stu-id="1e489-842">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="1e489-843">Un overload accetta un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="1e489-843">An overload accepts a default value.</span></span>

<span data-ttu-id="1e489-844">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="1e489-844">The following example:</span></span>

* <span data-ttu-id="1e489-845">Estrae il valore di stringa dalla configurazione con la chiave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="1e489-845">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="1e489-846">Se `NumberKey` non viene trovato nelle chiavi di configurazione, viene usato il valore predefinito di `99`.</span><span class="sxs-lookup"><span data-stu-id="1e489-846">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="1e489-847">Assegna al valore il tipo `int`.</span><span class="sxs-lookup"><span data-stu-id="1e489-847">Types the value as an `int`.</span></span>
* <span data-ttu-id="1e489-848">Memorizza il valore nella proprietà `NumberConfig` per l'uso da parte della pagina.</span><span class="sxs-lookup"><span data-stu-id="1e489-848">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="1e489-849">GetSection, GetChildren ed Exists</span><span class="sxs-lookup"><span data-stu-id="1e489-849">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="1e489-850">Per gli esempi che seguono, prendere in considerazione il file JSON seguente.</span><span class="sxs-lookup"><span data-stu-id="1e489-850">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="1e489-851">Vengono trovate quattro chiavi in due sezioni, una delle quali include una coppia di sottosezioni:</span><span class="sxs-lookup"><span data-stu-id="1e489-851">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="1e489-852">Quando il file viene letto nella configurazione, vengono create le chiavi gerarchiche univoche seguenti per contenere i valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="1e489-852">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="1e489-853">section0:key0</span><span class="sxs-lookup"><span data-stu-id="1e489-853">section0:key0</span></span>
* <span data-ttu-id="1e489-854">section0:key1</span><span class="sxs-lookup"><span data-stu-id="1e489-854">section0:key1</span></span>
* <span data-ttu-id="1e489-855">section1:key0</span><span class="sxs-lookup"><span data-stu-id="1e489-855">section1:key0</span></span>
* <span data-ttu-id="1e489-856">section1:key1</span><span class="sxs-lookup"><span data-stu-id="1e489-856">section1:key1</span></span>
* <span data-ttu-id="1e489-857">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="1e489-857">section2:subsection0:key0</span></span>
* <span data-ttu-id="1e489-858">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="1e489-858">section2:subsection0:key1</span></span>
* <span data-ttu-id="1e489-859">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="1e489-859">section2:subsection1:key0</span></span>
* <span data-ttu-id="1e489-860">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="1e489-860">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="1e489-861">GetSection</span><span class="sxs-lookup"><span data-stu-id="1e489-861">GetSection</span></span>

<span data-ttu-id="1e489-862">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) estrae una sottosezione della configurazione con la chiave di sottosezione specificata.</span><span class="sxs-lookup"><span data-stu-id="1e489-862">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="1e489-863">Per restituire una <xref:Microsoft.Extensions.Configuration.IConfigurationSection> che contiene solo le coppie chiave-valore in `section1`, chiamare `GetSection` e fornire il nome della sezione:</span><span class="sxs-lookup"><span data-stu-id="1e489-863">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="1e489-864">`configSection` non ha un valore, ma solo una chiave e un percorso.</span><span class="sxs-lookup"><span data-stu-id="1e489-864">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="1e489-865">In modo analogo, per ottenere i valori per le chiavi in `section2:subsection0`, chiamare `GetSection` e fornire il percorso della sezione:</span><span class="sxs-lookup"><span data-stu-id="1e489-865">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="1e489-866">`GetSection` non restituisce mai `null`.</span><span class="sxs-lookup"><span data-stu-id="1e489-866">`GetSection` never returns `null`.</span></span> <span data-ttu-id="1e489-867">Se non viene trovata una sezione corrispondente, viene restituita una `IConfigurationSection` vuota.</span><span class="sxs-lookup"><span data-stu-id="1e489-867">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="1e489-868">Quando `GetSection` restituisce una sezione corrispondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> non viene compilata.</span><span class="sxs-lookup"><span data-stu-id="1e489-868">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="1e489-869">Quando la sezione esiste, vengono restituiti <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.</span><span class="sxs-lookup"><span data-stu-id="1e489-869">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="1e489-870">GetChildren</span><span class="sxs-lookup"><span data-stu-id="1e489-870">GetChildren</span></span>

<span data-ttu-id="1e489-871">Una chiamata a [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) per `section2` ottiene una `IEnumerable<IConfigurationSection>` che include:</span><span class="sxs-lookup"><span data-stu-id="1e489-871">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="1e489-872">Exists</span><span class="sxs-lookup"><span data-stu-id="1e489-872">Exists</span></span>

<span data-ttu-id="1e489-873">Usare [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) per determinare se esiste una sezione di configurazione:</span><span class="sxs-lookup"><span data-stu-id="1e489-873">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="1e489-874">Considerati i dati di esempio, `sectionExists` è `false` perché non esiste una sezione `section2:subsection2` nei dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-874">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="1e489-875">Associazione a un oggetto grafico</span><span class="sxs-lookup"><span data-stu-id="1e489-875">Bind to an object graph</span></span>

<span data-ttu-id="1e489-876"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> è in grado di associare un intero oggetto grafico POCO.</span><span class="sxs-lookup"><span data-stu-id="1e489-876"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="1e489-877">Come per l'associazione di un oggetto semplice, vengono associate solo le proprietà di lettura/scrittura pubbliche.</span><span class="sxs-lookup"><span data-stu-id="1e489-877">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="1e489-878">L'esempio contiene un modello `TvShow` il cui oggetto grafico include `Metadata` e le classi `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="1e489-878">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="1e489-879">L'app di esempio ha un file *tvshow.xml* che contiene i dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="1e489-879">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="1e489-880">La configurazione viene associata all'intero oggetto grafico `TvShow` con il metodo `Bind`.</span><span class="sxs-lookup"><span data-stu-id="1e489-880">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="1e489-881">L'istanza associata viene assegnata a una proprietà per il rendering:</span><span class="sxs-lookup"><span data-stu-id="1e489-881">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="1e489-882">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)associa e restituisce il tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="1e489-882">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="1e489-883">`Get<T>` può essere più comodo che usare `Bind`.</span><span class="sxs-lookup"><span data-stu-id="1e489-883">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="1e489-884">Il codice seguente viene `Get<T>` illustrato come utilizzare con l'esempio precedente:The following code shows how to use with the preceding example:</span><span class="sxs-lookup"><span data-stu-id="1e489-884">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="1e489-885">Associare una matrice a una classe</span><span class="sxs-lookup"><span data-stu-id="1e489-885">Bind an array to a class</span></span>

<span data-ttu-id="1e489-886">*L'app di esempio dimostra i concetti spiegati in questa sezione.*</span><span class="sxs-lookup"><span data-stu-id="1e489-886">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="1e489-887">Il <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supporta l'associazione di matrici agli oggetti usando gli indici delle matrici nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-887">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="1e489-888">Qualsiasi formato di matrice che espone`:0:` `:1:`un &hellip; `:{n}:`segmento di chiave numerica ( , , ) è in grado di associare una matrice di matrici a una matrice di classi POCO.</span><span class="sxs-lookup"><span data-stu-id="1e489-888">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="1e489-889">L'associazione viene fornita per convenzione.</span><span class="sxs-lookup"><span data-stu-id="1e489-889">Binding is provided by convention.</span></span> <span data-ttu-id="1e489-890">I provider di configurazione personalizzati non devono implementare l'associazione di matrici.</span><span class="sxs-lookup"><span data-stu-id="1e489-890">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="1e489-891">**Elaborazione di matrici in memoria**</span><span class="sxs-lookup"><span data-stu-id="1e489-891">**In-memory array processing**</span></span>

<span data-ttu-id="1e489-892">Prendere in considerazione le chiavi di configurazione e i valori indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="1e489-892">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="1e489-893">Chiave</span><span class="sxs-lookup"><span data-stu-id="1e489-893">Key</span></span>             | <span data-ttu-id="1e489-894">valore</span><span class="sxs-lookup"><span data-stu-id="1e489-894">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="1e489-895">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="1e489-895">array:entries:0</span></span> | <span data-ttu-id="1e489-896">value0</span><span class="sxs-lookup"><span data-stu-id="1e489-896">value0</span></span> |
| <span data-ttu-id="1e489-897">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="1e489-897">array:entries:1</span></span> | <span data-ttu-id="1e489-898">value1</span><span class="sxs-lookup"><span data-stu-id="1e489-898">value1</span></span> |
| <span data-ttu-id="1e489-899">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="1e489-899">array:entries:2</span></span> | <span data-ttu-id="1e489-900">value2</span><span class="sxs-lookup"><span data-stu-id="1e489-900">value2</span></span> |
| <span data-ttu-id="1e489-901">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="1e489-901">array:entries:4</span></span> | <span data-ttu-id="1e489-902">value4</span><span class="sxs-lookup"><span data-stu-id="1e489-902">value4</span></span> |
| <span data-ttu-id="1e489-903">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="1e489-903">array:entries:5</span></span> | <span data-ttu-id="1e489-904">value5</span><span class="sxs-lookup"><span data-stu-id="1e489-904">value5</span></span> |

<span data-ttu-id="1e489-905">Queste chiavi e i valori vengono caricati nell'app di esempio usando il provider di configurazione della memoria:</span><span class="sxs-lookup"><span data-stu-id="1e489-905">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="1e489-906">La matrice ignora un valore per l'indice &num;3.</span><span class="sxs-lookup"><span data-stu-id="1e489-906">The array skips a value for index &num;3.</span></span> <span data-ttu-id="1e489-907">Lo strumento di associazione di configurazione non è in grado di associare valori null o di creare voci null negli oggetti associati, situazione che diventerà chiara tra poco quando viene illustrato il risultato dell'associazione di questa matrice a un oggetto.</span><span class="sxs-lookup"><span data-stu-id="1e489-907">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="1e489-908">Nell'app di esempio, è disponibile una classe POCO per i dati di configurazione associati:</span><span class="sxs-lookup"><span data-stu-id="1e489-908">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="1e489-909">I dati di configurazione sono associati all'oggetto:</span><span class="sxs-lookup"><span data-stu-id="1e489-909">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="1e489-910">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)si può anche usare la sintassi, che si traduce in codice più compatto:</span><span class="sxs-lookup"><span data-stu-id="1e489-910">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="1e489-911">L'oggetto associato, un'istanza di `ArrayExample`, riceve i dati di matrice dalla configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-911">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="1e489-912">Indice di `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="1e489-912">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="1e489-913">`ArrayExample.Entries` Valore</span><span class="sxs-lookup"><span data-stu-id="1e489-913">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="1e489-914">0</span><span class="sxs-lookup"><span data-stu-id="1e489-914">0</span></span>                            | <span data-ttu-id="1e489-915">value0</span><span class="sxs-lookup"><span data-stu-id="1e489-915">value0</span></span>                       |
| <span data-ttu-id="1e489-916">1</span><span class="sxs-lookup"><span data-stu-id="1e489-916">1</span></span>                            | <span data-ttu-id="1e489-917">value1</span><span class="sxs-lookup"><span data-stu-id="1e489-917">value1</span></span>                       |
| <span data-ttu-id="1e489-918">2</span><span class="sxs-lookup"><span data-stu-id="1e489-918">2</span></span>                            | <span data-ttu-id="1e489-919">value2</span><span class="sxs-lookup"><span data-stu-id="1e489-919">value2</span></span>                       |
| <span data-ttu-id="1e489-920">3</span><span class="sxs-lookup"><span data-stu-id="1e489-920">3</span></span>                            | <span data-ttu-id="1e489-921">value4</span><span class="sxs-lookup"><span data-stu-id="1e489-921">value4</span></span>                       |
| <span data-ttu-id="1e489-922">4</span><span class="sxs-lookup"><span data-stu-id="1e489-922">4</span></span>                            | <span data-ttu-id="1e489-923">value5</span><span class="sxs-lookup"><span data-stu-id="1e489-923">value5</span></span>                       |

<span data-ttu-id="1e489-924">L'indice &num;3 nell'oggetto associato contiene i dati di configurazione per la chiave di configurazione `array:4` e il relativo valore `value4`.</span><span class="sxs-lookup"><span data-stu-id="1e489-924">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="1e489-925">Quando i dati di configurazione contenenti una matrice vengono associati, gli indici di matrice nelle chiavi di configurazione vengono usati semplicemente per scorrere i dati di configurazione quando si crea l'oggetto.</span><span class="sxs-lookup"><span data-stu-id="1e489-925">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="1e489-926">Un valore null non può essere mantenuto nei dati di configurazione e una voce con valore null non viene creata in un oggetto associato quando una matrice nelle chiavi di configurazione ignora uno o più indici.</span><span class="sxs-lookup"><span data-stu-id="1e489-926">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="1e489-927">L'elemento di configurazione mancante per l'indice &num;3 può essere fornito prima dell'associazione all'istanza `ArrayExample` da qualsiasi provider di configurazione che produce la coppia chiave-valore corretta nella configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-927">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="1e489-928">Se il codice di esempio include un provider di configurazione JSON aggiuntivo con la coppia chiave-valore mancante, `ArrayExample.Entries` corrisponde alla matrice di configurazione completa:</span><span class="sxs-lookup"><span data-stu-id="1e489-928">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="1e489-929">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="1e489-929">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="1e489-930">In `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="1e489-930">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="1e489-931">La coppia chiave-valore mostrata nella tabella viene caricata nella configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-931">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="1e489-932">Chiave</span><span class="sxs-lookup"><span data-stu-id="1e489-932">Key</span></span>             | <span data-ttu-id="1e489-933">valore</span><span class="sxs-lookup"><span data-stu-id="1e489-933">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="1e489-934">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="1e489-934">array:entries:3</span></span> | <span data-ttu-id="1e489-935">value3</span><span class="sxs-lookup"><span data-stu-id="1e489-935">value3</span></span> |

<span data-ttu-id="1e489-936">Se l'istanza della classe `ArrayExample` viene associata dopo che il provider di configurazione JSON include la voce per l'indice &num;3, la matrice `ArrayExample.Entries` include il valore.</span><span class="sxs-lookup"><span data-stu-id="1e489-936">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="1e489-937">Indice di `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="1e489-937">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="1e489-938">`ArrayExample.Entries` Valore</span><span class="sxs-lookup"><span data-stu-id="1e489-938">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="1e489-939">0</span><span class="sxs-lookup"><span data-stu-id="1e489-939">0</span></span>                            | <span data-ttu-id="1e489-940">value0</span><span class="sxs-lookup"><span data-stu-id="1e489-940">value0</span></span>                       |
| <span data-ttu-id="1e489-941">1</span><span class="sxs-lookup"><span data-stu-id="1e489-941">1</span></span>                            | <span data-ttu-id="1e489-942">value1</span><span class="sxs-lookup"><span data-stu-id="1e489-942">value1</span></span>                       |
| <span data-ttu-id="1e489-943">2</span><span class="sxs-lookup"><span data-stu-id="1e489-943">2</span></span>                            | <span data-ttu-id="1e489-944">value2</span><span class="sxs-lookup"><span data-stu-id="1e489-944">value2</span></span>                       |
| <span data-ttu-id="1e489-945">3</span><span class="sxs-lookup"><span data-stu-id="1e489-945">3</span></span>                            | <span data-ttu-id="1e489-946">value3</span><span class="sxs-lookup"><span data-stu-id="1e489-946">value3</span></span>                       |
| <span data-ttu-id="1e489-947">4</span><span class="sxs-lookup"><span data-stu-id="1e489-947">4</span></span>                            | <span data-ttu-id="1e489-948">value4</span><span class="sxs-lookup"><span data-stu-id="1e489-948">value4</span></span>                       |
| <span data-ttu-id="1e489-949">5</span><span class="sxs-lookup"><span data-stu-id="1e489-949">5</span></span>                            | <span data-ttu-id="1e489-950">value5</span><span class="sxs-lookup"><span data-stu-id="1e489-950">value5</span></span>                       |

<span data-ttu-id="1e489-951">**Elaborazione di matrice JSON**</span><span class="sxs-lookup"><span data-stu-id="1e489-951">**JSON array processing**</span></span>

<span data-ttu-id="1e489-952">Se un file JSON contiene una matrice, vengono create chiavi di configurazione per gli elementi della matrice con un indice di sezione in base zero.</span><span class="sxs-lookup"><span data-stu-id="1e489-952">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="1e489-953">Nel file di configurazione seguente, `subsection` è una matrice:</span><span class="sxs-lookup"><span data-stu-id="1e489-953">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="1e489-954">Il provider di configurazione JSON legge i dati di configurazione nelle coppie chiave-valore seguenti:</span><span class="sxs-lookup"><span data-stu-id="1e489-954">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="1e489-955">Chiave</span><span class="sxs-lookup"><span data-stu-id="1e489-955">Key</span></span>                     | <span data-ttu-id="1e489-956">valore</span><span class="sxs-lookup"><span data-stu-id="1e489-956">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="1e489-957">json_array:key</span><span class="sxs-lookup"><span data-stu-id="1e489-957">json_array:key</span></span>          | <span data-ttu-id="1e489-958">valueA</span><span class="sxs-lookup"><span data-stu-id="1e489-958">valueA</span></span> |
| <span data-ttu-id="1e489-959">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="1e489-959">json_array:subsection:0</span></span> | <span data-ttu-id="1e489-960">valueB</span><span class="sxs-lookup"><span data-stu-id="1e489-960">valueB</span></span> |
| <span data-ttu-id="1e489-961">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="1e489-961">json_array:subsection:1</span></span> | <span data-ttu-id="1e489-962">valueC</span><span class="sxs-lookup"><span data-stu-id="1e489-962">valueC</span></span> |
| <span data-ttu-id="1e489-963">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="1e489-963">json_array:subsection:2</span></span> | <span data-ttu-id="1e489-964">valueD</span><span class="sxs-lookup"><span data-stu-id="1e489-964">valueD</span></span> |

<span data-ttu-id="1e489-965">Nell'app di esempio, la classe POCO seguente è disponibile per associare le coppie chiave-valore della configurazione:</span><span class="sxs-lookup"><span data-stu-id="1e489-965">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="1e489-966">Dopo l'associazione, `JsonArrayExample.Key` contiene il valore `valueA`.</span><span class="sxs-lookup"><span data-stu-id="1e489-966">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="1e489-967">I valori di sottosezione vengono archiviati nella proprietà matrice POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="1e489-967">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="1e489-968">Indice di `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="1e489-968">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="1e489-969">`JsonArrayExample.Subsection` Valore</span><span class="sxs-lookup"><span data-stu-id="1e489-969">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="1e489-970">0</span><span class="sxs-lookup"><span data-stu-id="1e489-970">0</span></span>                                   | <span data-ttu-id="1e489-971">valueB</span><span class="sxs-lookup"><span data-stu-id="1e489-971">valueB</span></span>                              |
| <span data-ttu-id="1e489-972">1</span><span class="sxs-lookup"><span data-stu-id="1e489-972">1</span></span>                                   | <span data-ttu-id="1e489-973">valueC</span><span class="sxs-lookup"><span data-stu-id="1e489-973">valueC</span></span>                              |
| <span data-ttu-id="1e489-974">2</span><span class="sxs-lookup"><span data-stu-id="1e489-974">2</span></span>                                   | <span data-ttu-id="1e489-975">valueD</span><span class="sxs-lookup"><span data-stu-id="1e489-975">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="1e489-976">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="1e489-976">Custom configuration provider</span></span>

<span data-ttu-id="1e489-977">L'app di esempio dimostra come creare un provider di configurazione di base che legge le coppie chiave-valore di configurazione da un database usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="1e489-977">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="1e489-978">Il provider ha le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="1e489-978">The provider has the following characteristics:</span></span>

* <span data-ttu-id="1e489-979">Il database in memoria di Entity Framework viene usato a scopo dimostrativo.</span><span class="sxs-lookup"><span data-stu-id="1e489-979">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="1e489-980">Per usare un database che richiede una stringa di connessione, implementare un `ConfigurationBuilder` secondario per fornire la stringa di connessione da un altro provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-980">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="1e489-981">Il provider legge una tabella di database in una configurazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="1e489-981">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="1e489-982">Il provider non esegue una query sul database per ogni chiave.</span><span class="sxs-lookup"><span data-stu-id="1e489-982">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="1e489-983">Il ricaricamento in caso di modifica non è implementato, quindi l'aggiornamento del database dopo l'avvio dell'app non ha alcun effetto sulla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1e489-983">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="1e489-984">Definire un'entità `EFConfigurationValue` per l'archiviazione dei valori di configurazione nel database.</span><span class="sxs-lookup"><span data-stu-id="1e489-984">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="1e489-985">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e489-985">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="1e489-986">Aggiungere `EFConfigurationContext` per archiviare i valori configurati e accedervi.</span><span class="sxs-lookup"><span data-stu-id="1e489-986">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="1e489-987">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e489-987">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="1e489-988">Creare una classe che implementi <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="1e489-988">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="1e489-989">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e489-989">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="1e489-990">Creare il provider di configurazione personalizzato ereditando da <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="1e489-990">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="1e489-991">Il provider di configurazione inizializza il database quando è vuoto.</span><span class="sxs-lookup"><span data-stu-id="1e489-991">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="1e489-992">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e489-992">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="1e489-993">Un metodo di estensione `AddEFConfiguration` consente di aggiungere l'origine di configurazione a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1e489-993">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="1e489-994">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e489-994">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="1e489-995">L'esempio di codice seguente mostra come usare il `EFConfigurationProvider` personalizzato in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e489-995">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="1e489-996">Accedere alla configurazione durante l'avvio</span><span class="sxs-lookup"><span data-stu-id="1e489-996">Access configuration during startup</span></span>

<span data-ttu-id="1e489-997">Inserire `IConfiguration` nel costruttore `Startup` per accedere ai valori di configurazione in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1e489-997">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1e489-998">Per accedere alla configurazione in `Startup.Configure`, inserire `IConfiguration` direttamente nel metodo o usare l'istanza dal costruttore:</span><span class="sxs-lookup"><span data-stu-id="1e489-998">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="1e489-999">Per un esempio di accesso alla configurazione usando metodi di servizio di avvio, vedere [Avvio dell'applicazione: Metodi pratici](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="1e489-999">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="1e489-1000">Accedere alla configurazione in una pagina Razor Pages o in una visualizzazione MVC</span><span class="sxs-lookup"><span data-stu-id="1e489-1000">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="1e489-1001">Per accedere alle impostazioni di configurazione in una pagina Razor Pages o in una visualizzazione MVC, aggiungere un [direttiva using](xref:mvc/views/razor#using) ([Informazioni di riferimento su C#: direttiva using](/dotnet/csharp/language-reference/keywords/using-directive)) per lo [spazio dei nomi Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) e inserire <xref:Microsoft.Extensions.Configuration.IConfiguration> nella pagina o nella visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="1e489-1001">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="1e489-1002">In una pagina Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="1e489-1002">In a Razor Pages page:</span></span>

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

<span data-ttu-id="1e489-1003">In una visualizzazione MVC:</span><span class="sxs-lookup"><span data-stu-id="1e489-1003">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="1e489-1004">Aggiungere la configurazione da un assembly esterno</span><span class="sxs-lookup"><span data-stu-id="1e489-1004">Add configuration from an external assembly</span></span>

<span data-ttu-id="1e489-1005">Un'implementazione <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> consente l'aggiunta di miglioramenti a un'app all'avvio, da un assembly esterno alla classe `Startup` dell'app.</span><span class="sxs-lookup"><span data-stu-id="1e489-1005">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="1e489-1006">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="1e489-1006">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1e489-1007">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="1e489-1007">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
