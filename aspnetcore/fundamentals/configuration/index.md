---
title: Configurazione in ASP.NET Core
author: rick-anderson
description: Informazioni su come usare l'API di configurazione per configurare un'app ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/23/2020
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
uid: fundamentals/configuration/index
ms.openlocfilehash: c04dcc65f7518d2d8b32cdce7a7fbb756dd8ec3a
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417539"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="a8c2d-103">Configurazione in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8c2d-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="a8c2d-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="a8c2d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a8c2d-105">La configurazione in ASP.NET Core viene eseguita utilizzando uno o più [provider di configurazione](#cp).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="a8c2d-106">I provider di configurazione leggono i dati di configurazione da coppie chiave-valore usando un'ampia gamma di origini di configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="a8c2d-107">File di impostazioni, ad esempio *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="a8c2d-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="a8c2d-108">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="a8c2d-108">Environment variables</span></span>
* <span data-ttu-id="a8c2d-109">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="a8c2d-109">Azure Key Vault</span></span>
* <span data-ttu-id="a8c2d-110">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="a8c2d-110">Azure App Configuration</span></span>
* <span data-ttu-id="a8c2d-111">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="a8c2d-111">Command-line arguments</span></span>
* <span data-ttu-id="a8c2d-112">Provider personalizzati, installati o creati</span><span class="sxs-lookup"><span data-stu-id="a8c2d-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="a8c2d-113">File della directory</span><span class="sxs-lookup"><span data-stu-id="a8c2d-113">Directory files</span></span>
* <span data-ttu-id="a8c2d-114">Oggetti .NET in memoria</span><span class="sxs-lookup"><span data-stu-id="a8c2d-114">In-memory .NET objects</span></span>

<span data-ttu-id="a8c2d-115">In questo argomento vengono fornite informazioni sulla configurazione di in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="a8c2d-116">Per informazioni sull'uso della configurazione nelle app console, vedere [configurazione di .NET](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="a8c2d-117">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a8c2d-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="a8c2d-118">Configurazione predefinita</span><span class="sxs-lookup"><span data-stu-id="a8c2d-118">Default configuration</span></span>

<span data-ttu-id="a8c2d-119">ASP.NET Core app Web create con [DotNet New](/dotnet/core/tools/dotnet-new) o Visual Studio generano il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="a8c2d-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fornisce la configurazione predefinita per l'app nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="a8c2d-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : aggiunge un oggetto esistente `IConfiguration` come origine.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="a8c2d-122">Nel caso di configurazione predefinita, aggiunge la configurazione [host](#hvac) e la imposta come prima origine per la configurazione dell' _app_ .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="a8c2d-123">[appsettings.json](#appsettingsjson) uso del [provider di configurazione JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="a8c2d-124">*appSettings.* `Environment` *. JSON* con il [provider di configurazione JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="a8c2d-125">Ad esempio, *appSettings*. ***Produzione \* \* _._json* e *appSettings*. \* \* \* sviluppo** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="a8c2d-126">[Segreti dell'app](xref:security/app-secrets) quando l'app viene eseguita nell' `Development` ambiente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="a8c2d-127">Variabili di ambiente che usano il [provider di configurazione delle variabili di ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="a8c2d-128">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="a8c2d-129">I provider di configurazione aggiunti successivamente sostituiscono le impostazioni di chiave precedenti.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="a8c2d-130">Se, ad esempio, `MyKey` è impostato in *appsettings.json* e nell'ambiente, viene utilizzato il valore dell'ambiente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="a8c2d-131">Utilizzando i provider di configurazione predefiniti, il  [provider di configurazione della riga di comando](#clcp) esegue l'override di tutti gli altri provider.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="a8c2d-132">Per ulteriori informazioni su `CreateDefaultBuilder` , vedere [impostazioni predefinite del generatore](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="a8c2d-133">Il codice seguente Visualizza i provider di configurazione abilitati nell'ordine in cui sono stati aggiunti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="a8c2d-134">Si consideri il seguente *appsettings.json* file:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="a8c2d-135">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="a8c2d-136">Il valore predefinito <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carica la configurazione nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="a8c2d-137">*appSettings.* `Environment` *. JSON* : ad esempio, *appSettings*. \*\**Produzione \* \* _._json* e *appSettings*. \*\*\*\* \* sviluppo _._json \* file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="a8c2d-138">La versione dell'ambiente del file viene caricata in base a [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="a8c2d-139">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="a8c2d-140">*appSettings*. `Environment` . i valori *JSON* eseguono l'override delle chiavi in *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="a8c2d-141">Ad esempio, per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-141">For example, by default:</span></span>

* <span data-ttu-id="a8c2d-142">Durante lo sviluppo, la configurazione *appSettings*. \***Development** _._json \* sovrascrive i valori presenti in *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="a8c2d-143">In produzione, la configurazione *appSettings*. \***Production** _._json \* sovrascrive i valori presenti in *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="a8c2d-144">Ad esempio, quando si distribuisce l'app in Azure.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="a8c2d-145">Associare i dati di configurazione gerarchici usando il modello di opzioni</span><span class="sxs-lookup"><span data-stu-id="a8c2d-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="a8c2d-146">Utilizzando la configurazione [predefinita](#default) , *appsettings.json* e *appSettings.* `Environment` i file con *estensione JSON* sono abilitati con [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="a8c2d-147">Modifiche apportate a *appsettings.json* e *appSettings.* `Environment` file con *estensione JSON* \***dopo** l'avvio di _ l'app viene letta dal [provider di configurazione JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file \***after** _ the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="a8c2d-148">Per informazioni sull'aggiunta di altri file di configurazione JSON, vedere [provider di configurazione JSON](#jcp) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="a8c2d-149">Combinazione della raccolta di servizi</span><span class="sxs-lookup"><span data-stu-id="a8c2d-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="a8c2d-150">Gestione della sicurezza e del segreto</span><span class="sxs-lookup"><span data-stu-id="a8c2d-150">Security and secret manager</span></span>

<span data-ttu-id="a8c2d-151">Linee guida sui dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-151">Configuration data guidelines:</span></span>

<span data-ttu-id="a8c2d-152">_ Non archiviare mai le password o altri dati sensibili nel codice del provider di configurazione o nei file di configurazione di testo normale.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-152">_ Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="a8c2d-153">Il [gestore del segreto](xref:security/app-secrets) può essere usato per archiviare i segreti in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-153">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="a8c2d-154">Non usare i segreti di produzione in ambienti di sviluppo o di test.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="a8c2d-155">Specificare i segreti all'esterno del progetto in modo che non possano essere inavvertitamente inviati a un repository del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="a8c2d-156">Per [impostazione predefinita](#default), [Secret Manager](xref:security/app-secrets) legge le impostazioni di configurazione dopo *appsettings.json* e *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-156">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="a8c2d-157">Per ulteriori informazioni sull'archiviazione di password o altri dati sensibili:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-157">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="a8c2d-158"><xref:security/app-secrets>: Include consigli sull'uso delle variabili di ambiente per archiviare dati riservati.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-158"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="a8c2d-159">Il gestore dei segreti USA il [provider di configurazione file](#fcp) per archiviare i segreti utente in un file JSON nel sistema locale.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-159">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="a8c2d-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) archivia in modo sicuro i segreti delle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="a8c2d-161">Per altre informazioni, vedere <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-161">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="a8c2d-162">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="a8c2d-162">Environment variables</span></span>

<span data-ttu-id="a8c2d-163">Utilizzando la configurazione [predefinita](#default) , <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carica la configurazione dalle coppie chiave-valore della variabile di ambiente dopo la lettura *appsettings.json* , *appSettings.* `Environment` *. JSON* e [gestione segreta](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-163">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="a8c2d-164">Pertanto, i valori di chiave letti dall'ambiente eseguono l'override dei valori letti da *appsettings.json* , *appSettings.* `Environment` *. JSON* e gestione segreta.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-164">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="a8c2d-165">I `set` comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-165">The following `set` commands:</span></span>

* <span data-ttu-id="a8c2d-166">Impostare le chiavi e i valori di ambiente dell' [esempio precedente](#appsettingsjson) in Windows.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-166">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="a8c2d-167">Testare le impostazioni quando si usa il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-167">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="a8c2d-168">Il `dotnet run` comando deve essere eseguito nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-168">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="a8c2d-169">Le impostazioni di ambiente precedenti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-169">The preceding environment settings:</span></span>

* <span data-ttu-id="a8c2d-170">Vengono impostati solo nei processi avviati dalla finestra di comando in cui sono stati impostati.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-170">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="a8c2d-171">Non verrà letto dai browser avviati con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-171">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="a8c2d-172">Per impostare le chiavi e i valori di ambiente in Windows, è possibile usare i comandi [Setx](/windows-server/administration/windows-commands/setx) seguenti.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-172">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="a8c2d-173">Diversamente da `set` , `setx` le impostazioni sono rese permanente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-173">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="a8c2d-174">`/M` imposta la variabile nell'ambiente di sistema.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-174">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="a8c2d-175">Se l' `/M` opzione non viene usata, viene impostata una variabile di ambiente utente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-175">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="a8c2d-176">Per verificare che i comandi precedenti eseguano l'override di *appsettings.json* e *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-176">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="a8c2d-177">Con Visual Studio: chiudere e riavviare Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-177">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="a8c2d-178">Con l'interfaccia della riga di comando: avviare una nuova finestra di comando e immettere `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-178">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="a8c2d-179">Chiamare <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> con una stringa per specificare un prefisso per le variabili di ambiente:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-179">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="a8c2d-180">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-180">In the preceding code:</span></span>

* <span data-ttu-id="a8c2d-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` viene aggiunto dopo i [provider di configurazione predefiniti](#default).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="a8c2d-182">Per un esempio di ordinamento dei provider di configurazione, vedere [provider di configurazione JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-182">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="a8c2d-183">Le variabili di ambiente impostate con il `MyCustomPrefix_` prefisso sostituiscono i [provider di configurazione predefiniti](#default).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-183">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="a8c2d-184">Sono incluse le variabili di ambiente senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-184">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="a8c2d-185">Il prefisso viene rimosso quando vengono lette le coppie chiave-valore di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-185">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="a8c2d-186">I comandi seguenti verificano il prefisso personalizzato:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-186">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="a8c2d-187">La [configurazione predefinita](#default) carica le variabili di ambiente e gli argomenti della riga di comando preceduti da `DOTNET_` e `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-187">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="a8c2d-188">I `DOTNET_` `ASPNETCORE_` prefissi e vengono usati da ASP.NET Core per la [configurazione dell'host e dell'app](xref:fundamentals/host/generic-host#host-configuration), ma non per la configurazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-188">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="a8c2d-189">Per ulteriori informazioni sulla configurazione dell'host e dell'app, vedere [.NET Generic Host](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-189">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="a8c2d-190">In [app Azure servizio](https://azure.microsoft.com/services/app-service/)selezionare **impostazione nuova applicazione** nella pagina **Impostazioni > configurazione** .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-190">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="a8c2d-191">App Azure impostazioni dell'applicazione del servizio sono:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-191">Azure App Service application settings are:</span></span>

* <span data-ttu-id="a8c2d-192">Crittografati a riposo e trasmessi su un canale crittografato.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-192">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="a8c2d-193">Esposto come variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-193">Exposed as environment variables.</span></span>

<span data-ttu-id="a8c2d-194">Per altre informazioni, vedere [App di Azure: Eseguire l'override della configurazione delle app usando il portale di Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-194">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="a8c2d-195">Per informazioni sulle stringhe di connessione del database di Azure, vedere [prefissi della stringa di connessione](#constr) .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-195">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="a8c2d-196">Denominazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="a8c2d-196">Naming of environment variables</span></span>

<span data-ttu-id="a8c2d-197">I nomi delle variabili di ambiente riflettono la struttura di un *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-197">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="a8c2d-198">Ogni elemento della gerarchia è separato da un doppio carattere di sottolineatura (preferibile) o da due punti.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-198">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="a8c2d-199">Quando la struttura dell'elemento include una matrice, l'indice della matrice deve essere trattato come un nome di elemento aggiuntivo in questo percorso.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-199">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="a8c2d-200">Si consideri il *appsettings.json* file seguente e i valori equivalenti rappresentati come variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-200">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

**appsettings.json**

```json
{
    "SmtpServer": "smtp.example.com",
    "Logging": [
        {
            "Name": "ToEmail",
            "Level": "Critical",
            "Args": {
                "FromAddress": "MySystem@example.com",
                "ToAddress": "SRE@example.com"
            }
        },
        {
            "Name": "ToConsole",
            "Level": "Information"
        }
    ]
}
```

<span data-ttu-id="a8c2d-201">**variabili di ambiente**</span><span class="sxs-lookup"><span data-stu-id="a8c2d-201">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="a8c2d-202">Variabili di ambiente impostate in launchSettings.js</span><span class="sxs-lookup"><span data-stu-id="a8c2d-202">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="a8c2d-203">Le variabili di ambiente impostate in *launchSettings.jssu* sostituiscono quelle impostate nell'ambiente di sistema.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-203">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="a8c2d-204">Riga di comando</span><span class="sxs-lookup"><span data-stu-id="a8c2d-204">Command-line</span></span>

<span data-ttu-id="a8c2d-205">Utilizzando la configurazione [predefinita](#default) , <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carica la configurazione dalle coppie chiave-valore dell'argomento della riga di comando dopo le origini di configurazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-205">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="a8c2d-206">*appsettings.json* e *appSettings*. `Environment` . file *JSON* .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-206">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="a8c2d-207">[Segreti dell'app (gestione segreto)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-207">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="a8c2d-208">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-208">Environment variables.</span></span>

<span data-ttu-id="a8c2d-209">Per [impostazione predefinita](#default), i valori di configurazione impostati nei valori di configurazione di override della riga di comando impostati con tutti gli altri provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-209">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="a8c2d-210">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="a8c2d-210">Command-line arguments</span></span>

<span data-ttu-id="a8c2d-211">Il comando seguente imposta le chiavi e i valori usando `=` :</span><span class="sxs-lookup"><span data-stu-id="a8c2d-211">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="a8c2d-212">Il comando seguente imposta le chiavi e i valori usando `/` :</span><span class="sxs-lookup"><span data-stu-id="a8c2d-212">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="a8c2d-213">Il comando seguente imposta le chiavi e i valori usando `--` :</span><span class="sxs-lookup"><span data-stu-id="a8c2d-213">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="a8c2d-214">Valore chiave:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-214">The key value:</span></span>

* <span data-ttu-id="a8c2d-215">Deve seguire `=` oppure la chiave deve avere un prefisso `--` o `/` quando il valore segue uno spazio.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-215">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="a8c2d-216">Non è obbligatorio se `=` si usa.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-216">Isn't required if `=` is used.</span></span> <span data-ttu-id="a8c2d-217">Ad esempio: `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-217">For example, `MySetting=`.</span></span>

<span data-ttu-id="a8c2d-218">All'interno dello stesso comando, non combinare coppie chiave-valore dell'argomento della riga di comando che usano `=` con coppie chiave-valore che usano uno spazio.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-218">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="a8c2d-219">Mapping di sostituzione</span><span class="sxs-lookup"><span data-stu-id="a8c2d-219">Switch mappings</span></span>

<span data-ttu-id="a8c2d-220">I mapping switch consentono la logica di sostituzione del nome **chiave** .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-220">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="a8c2d-221">Fornire un dizionario di sostituzioni switch al <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-221">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="a8c2d-222">Quando viene utilizzato il dizionario dei mapping di sostituzione, nel dizionario viene controllata la presenza di una chiave corrispondente alla chiave fornita da un argomento della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-222">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="a8c2d-223">Se la chiave della riga di comando viene trovata nel dizionario, il valore del dizionario viene passato di nuovo per impostare la coppia chiave-valore nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-223">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="a8c2d-224">Un mapping di sostituzione è necessario per le chiavi della riga di comando con un trattino singolo (`-`) come prefisso.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-224">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="a8c2d-225">Regole principali del dizionario dei mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-225">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="a8c2d-226">Le opzioni devono iniziare con `-` o `--` .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-226">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="a8c2d-227">Il dizionario dei mapping di sostituzione non deve contenere chiavi duplicate.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-227">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="a8c2d-228">Per usare un dizionario dei mapping switch, passarlo alla chiamata a `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="a8c2d-228">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="a8c2d-229">Il codice seguente mostra i valori chiave per le chiavi sostituite:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-229">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="a8c2d-230">Il comando seguente funziona per testare la sostituzione della chiave:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-230">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="a8c2d-231">Per le app che usano i mapping di sostituzione, la chiamata a `CreateDefaultBuilder` non deve passare argomenti.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-231">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="a8c2d-232">La `CreateDefaultBuilder` chiamata al metodo `AddCommandLine` non include opzioni mappate e non è possibile passare il dizionario di mapping switch a `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-232">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a8c2d-233">La soluzione non passa gli argomenti a `CreateDefaultBuilder` ma per consentire al metodo del `ConfigurationBuilder` metodo `AddCommandLine` di elaborare sia gli argomenti sia il dizionario di mapping delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-233">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="a8c2d-234">Dati di configurazione gerarchici</span><span class="sxs-lookup"><span data-stu-id="a8c2d-234">Hierarchical configuration data</span></span>

<span data-ttu-id="a8c2d-235">L'API di configurazione legge i dati di configurazione gerarchici rendendo flat i dati gerarchici con l'uso di un delimitatore nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-235">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="a8c2d-236">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il seguente  *appsettings.json* file:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-236">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="a8c2d-237">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-237">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="a8c2d-238">Il modo migliore per leggere i dati di configurazione gerarchici consiste nell'usare il modello di opzioni.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-238">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="a8c2d-239">Per altre informazioni, vedere [associare dati di configurazione gerarchici](#optpat) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-239">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="a8c2d-240">I metodi <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sono disponibili per isolare le sezioni e gli elementi figlio di una sezione nei dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-240"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="a8c2d-241">Questi metodi sono descritti più avanti in [GetSection, GetChildren ed Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-241">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="a8c2d-242">Chiavi e valori di configurazione</span><span class="sxs-lookup"><span data-stu-id="a8c2d-242">Configuration keys and values</span></span>

<span data-ttu-id="a8c2d-243">Chiavi di configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-243">Configuration keys:</span></span>

* <span data-ttu-id="a8c2d-244">Non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-244">Are case-insensitive.</span></span> <span data-ttu-id="a8c2d-245">Ad esempio, `ConnectionString` e `connectionstring` vengono considerate chiavi equivalenti.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-245">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="a8c2d-246">Se una chiave e un valore vengono impostati in più provider di configurazione, viene utilizzato il valore dell'ultimo provider aggiunto.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-246">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="a8c2d-247">Per ulteriori informazioni, vedere [configurazione predefinita](#default).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-247">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="a8c2d-248">Chiavi gerarchiche</span><span class="sxs-lookup"><span data-stu-id="a8c2d-248">Hierarchical keys</span></span>
  * <span data-ttu-id="a8c2d-249">Nell'ambito dell'API di configurazione, il separatore due punti (`:`) funziona in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-249">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="a8c2d-250">Nelle variabili di ambiente, un separatore due punti potrebbe non funzionare in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-250">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="a8c2d-251">Un doppio carattere di sottolineatura, `__` , è supportato da tutte le piattaforme e viene convertito automaticamente in due punti `:` .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-251">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="a8c2d-252">In Azure Key Vault, le chiavi gerarchiche utilizzano `--` come separatore.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-252">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="a8c2d-253">Il [provider di configurazione Azure Key Vault](xref:security/key-vault-configuration) sostituisce automaticamente `--` con un `:` quando i segreti vengono caricati nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-253">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="a8c2d-254">Il <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supporta l'associazione di matrici agli oggetti usando gli indici delle matrici nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-254">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="a8c2d-255">L'associazione di matrici è descritta nella sezione [Associare una matrice a una classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-255">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="a8c2d-256">Valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-256">Configuration values:</span></span>

* <span data-ttu-id="a8c2d-257">Sono stringhe.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-257">Are strings.</span></span>
* <span data-ttu-id="a8c2d-258">I valori null non possono essere archiviati nella configurazione o associati a oggetti.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-258">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="a8c2d-259">Provider di configurazione</span><span class="sxs-lookup"><span data-stu-id="a8c2d-259">Configuration providers</span></span>

<span data-ttu-id="a8c2d-260">La tabella seguente mostra i provider di configurazione disponibili per le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-260">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="a8c2d-261">Provider</span><span class="sxs-lookup"><span data-stu-id="a8c2d-261">Provider</span></span> | <span data-ttu-id="a8c2d-262">Fornisce la configurazione da</span><span class="sxs-lookup"><span data-stu-id="a8c2d-262">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="a8c2d-263">Provider di configurazione Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a8c2d-263">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="a8c2d-264">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="a8c2d-264">Azure Key Vault</span></span> |
| [<span data-ttu-id="a8c2d-265">Provider di configurazione app Azure</span><span class="sxs-lookup"><span data-stu-id="a8c2d-265">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="a8c2d-266">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="a8c2d-266">Azure App Configuration</span></span> |
| [<span data-ttu-id="a8c2d-267">Provider di configurazione della riga di comando</span><span class="sxs-lookup"><span data-stu-id="a8c2d-267">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="a8c2d-268">Parametri della riga di comando</span><span class="sxs-lookup"><span data-stu-id="a8c2d-268">Command-line parameters</span></span> |
| [<span data-ttu-id="a8c2d-269">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="a8c2d-269">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="a8c2d-270">Origine personalizzata</span><span class="sxs-lookup"><span data-stu-id="a8c2d-270">Custom source</span></span> |
| [<span data-ttu-id="a8c2d-271">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="a8c2d-271">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="a8c2d-272">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="a8c2d-272">Environment variables</span></span> |
| [<span data-ttu-id="a8c2d-273">Provider di configurazione file</span><span class="sxs-lookup"><span data-stu-id="a8c2d-273">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="a8c2d-274">File INI, JSON e XML</span><span class="sxs-lookup"><span data-stu-id="a8c2d-274">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="a8c2d-275">Provider di configurazione chiave per file</span><span class="sxs-lookup"><span data-stu-id="a8c2d-275">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="a8c2d-276">File della directory</span><span class="sxs-lookup"><span data-stu-id="a8c2d-276">Directory files</span></span> |
| [<span data-ttu-id="a8c2d-277">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="a8c2d-277">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="a8c2d-278">Raccolte in memoria</span><span class="sxs-lookup"><span data-stu-id="a8c2d-278">In-memory collections</span></span> |
| [<span data-ttu-id="a8c2d-279">Gestione segreta</span><span class="sxs-lookup"><span data-stu-id="a8c2d-279">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="a8c2d-280">File nella directory dei profili utente</span><span class="sxs-lookup"><span data-stu-id="a8c2d-280">File in the user profile directory</span></span> |

<span data-ttu-id="a8c2d-281">Le origini di configurazione vengono lette nell'ordine in cui sono specificati i provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-281">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="a8c2d-282">Ordinare i provider di configurazione nel codice in base alle priorità per le origini di configurazione sottostanti richieste dall'app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-282">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="a8c2d-283">Una sequenza tipica di provider di configurazione è:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-283">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="a8c2d-284">*appSettings*. `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="a8c2d-284">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="a8c2d-285">Gestione segreta</span><span class="sxs-lookup"><span data-stu-id="a8c2d-285">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="a8c2d-286">Variabili di ambiente che usano il [provider di configurazione delle variabili di ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-286">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="a8c2d-287">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-287">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="a8c2d-288">Una procedura comune consiste nell'aggiungere il provider di configurazione della riga di comando per ultimo in una serie di provider per consentire agli argomenti della riga di comando di eseguire l'override del set di configurazione da parte degli altri provider.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-288">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="a8c2d-289">La sequenza di provider precedente viene utilizzata nella [configurazione predefinita](#default).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-289">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="a8c2d-290">Prefissi della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="a8c2d-290">Connection string prefixes</span></span>

<span data-ttu-id="a8c2d-291">L'API di configurazione dispone di regole di elaborazione speciali per quattro variabili di ambiente della stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-291">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="a8c2d-292">Queste stringhe di connessione sono necessarie per configurare le stringhe di connessione di Azure per l'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-292">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="a8c2d-293">Le variabili di ambiente con i prefissi visualizzati nella tabella vengono caricate nell'app con la [configurazione predefinita](#default) o quando non viene fornito alcun prefisso a `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-293">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="a8c2d-294">Prefisso della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="a8c2d-294">Connection string prefix</span></span> | <span data-ttu-id="a8c2d-295">Provider</span><span class="sxs-lookup"><span data-stu-id="a8c2d-295">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="a8c2d-296">Provider personalizzato</span><span class="sxs-lookup"><span data-stu-id="a8c2d-296">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="a8c2d-297">MySQL</span><span class="sxs-lookup"><span data-stu-id="a8c2d-297">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="a8c2d-298">Database SQL di Azure</span><span class="sxs-lookup"><span data-stu-id="a8c2d-298">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="a8c2d-299">SQL Server</span><span class="sxs-lookup"><span data-stu-id="a8c2d-299">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="a8c2d-300">Quando una variabile di ambiente viene individuata e caricata nella configurazione con uno qualsiasi dei quattro prefissi indicati nella tabella:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-300">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="a8c2d-301">La chiave di configurazione viene creata rimuovendo il prefisso della variabile di ambiente e aggiungendo una sezione per la chiave di configurazione (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-301">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="a8c2d-302">Viene creata una nuova coppia chiave-valore della configurazione che rappresenta il provider di connessione al database (ad eccezione di `CUSTOMCONNSTR_`, che non ha un provider dichiarato).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-302">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="a8c2d-303">Chiave della variabile di ambiente</span><span class="sxs-lookup"><span data-stu-id="a8c2d-303">Environment variable key</span></span> | <span data-ttu-id="a8c2d-304">Chiave di configurazione convertita</span><span class="sxs-lookup"><span data-stu-id="a8c2d-304">Converted configuration key</span></span> | <span data-ttu-id="a8c2d-305">Voce di configurazione del provider</span><span class="sxs-lookup"><span data-stu-id="a8c2d-305">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a8c2d-306">Voce di configurazione non creata.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-306">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a8c2d-307">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-307">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="a8c2d-308">Valore: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="a8c2d-308">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a8c2d-309">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-309">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="a8c2d-310">Valore: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="a8c2d-310">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a8c2d-311">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-311">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="a8c2d-312">Valore: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="a8c2d-312">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="a8c2d-313">Provider di configurazione file</span><span class="sxs-lookup"><span data-stu-id="a8c2d-313">File configuration provider</span></span>

<span data-ttu-id="a8c2d-314"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> è la classe base per il caricamento della configurazione dal file system.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-314"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="a8c2d-315">I provider di configurazione seguenti derivano da `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="a8c2d-315">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="a8c2d-316">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="a8c2d-316">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="a8c2d-317">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="a8c2d-317">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="a8c2d-318">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="a8c2d-318">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="a8c2d-319">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="a8c2d-319">INI configuration provider</span></span>

<span data-ttu-id="a8c2d-320"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carica la configurazione da coppie chiave-valore di file INI in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-320">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="a8c2d-321">Il codice seguente cancella tutti i provider di configurazione e aggiunge diversi provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-321">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="a8c2d-322">Nel codice precedente, le impostazioni nel *MyIniConfig.ini* e in  *MyIniConfig*. `Environment` i file *ini* vengono sottoposti a override dalle impostazioni nel:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-322">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="a8c2d-323">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="a8c2d-323">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="a8c2d-324">[Provider di configurazione della riga di comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-324">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="a8c2d-325">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il file di *MyIniConfig.ini* seguente:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-325">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="a8c2d-326">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-326">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="a8c2d-327">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="a8c2d-327">JSON configuration provider</span></span>

<span data-ttu-id="a8c2d-328"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Carica la configurazione dalle coppie chiave-valore del file JSON.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-328">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="a8c2d-329">Gli overload possono specificare:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-329">Overloads can specify:</span></span>

* <span data-ttu-id="a8c2d-330">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-330">Whether the file is optional.</span></span>
* <span data-ttu-id="a8c2d-331">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-331">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="a8c2d-332">Osservare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-332">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="a8c2d-333">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-333">The preceding code:</span></span>

* <span data-ttu-id="a8c2d-334">Configura il provider di configurazione JSON per caricare il *MyConfig.jsnel* file con le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-334">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="a8c2d-335">`optional: true`: Il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-335">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="a8c2d-336">`reloadOnChange: true` : Il file viene ricaricato quando vengono salvate le modifiche.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-336">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="a8c2d-337">Legge i [provider di configurazione predefiniti](#default) prima del *MyConfig.jssul* file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-337">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="a8c2d-338">Impostazioni nell'impostazione *MyConfig.js* di sostituzione dei file nei provider di configurazione predefiniti, tra cui il [provider di configurazione delle variabili di ambiente](#evcp) e il provider di configurazione della riga di [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-338">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="a8c2d-339">In genere **non** si vuole che un file JSON personalizzato che esegue l'override dei valori impostati nel [provider di configurazione delle variabili di ambiente](#evcp) e nel provider di configurazione della riga di [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-339">You typically \***don't** _ want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="a8c2d-340">Il codice seguente cancella tutti i provider di configurazione e aggiunge diversi provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-340">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="a8c2d-341">Nel codice precedente, le impostazioni nel _MyConfig.jsin \* e in  *config*. `Environment` . file *JSON* :</span><span class="sxs-lookup"><span data-stu-id="a8c2d-341">In the preceding code, settings in the _MyConfig.json\* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="a8c2d-342">Eseguire l'override delle impostazioni in *appsettings.json* e *appSettings*. `Environment` . file *JSON* .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-342">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="a8c2d-343">Viene sottoposto a override dalle impostazioni del [provider di configurazione delle variabili di ambiente](#evcp) e del provider di configurazione della riga di [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-343">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="a8c2d-344">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene i  *MyConfig.jsseguenti nel* file:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-344">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="a8c2d-345">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-345">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="a8c2d-346">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="a8c2d-346">XML configuration provider</span></span>

<span data-ttu-id="a8c2d-347">Il <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carica la configurazione da coppie chiave-valore di file XML in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-347">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="a8c2d-348">Il codice seguente cancella tutti i provider di configurazione e aggiunge diversi provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-348">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="a8c2d-349">Nel codice precedente, le impostazioni nel *MyXMLFile.xml* e in  *MyXMLFile*. `Environment` i file *XML* vengono sottoposti a override dalle impostazioni in:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-349">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="a8c2d-350">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="a8c2d-350">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="a8c2d-351">[Provider di configurazione della riga di comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-351">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="a8c2d-352">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il file di *MyXMLFile.xml* seguente:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-352">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="a8c2d-353">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-353">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="a8c2d-354">La ripetizione di elementi che usano lo stesso nome di elemento funziona se si usa l'attributo `name` per distinguere gli elementi:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-354">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="a8c2d-355">Il codice seguente legge il file di configurazione precedente e visualizza le chiavi e i valori:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-355">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="a8c2d-356">È possibile usare attributi per fornire valori:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-356">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="a8c2d-357">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-357">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a8c2d-358">key:attribute</span><span class="sxs-lookup"><span data-stu-id="a8c2d-358">key:attribute</span></span>
* <span data-ttu-id="a8c2d-359">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="a8c2d-359">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="a8c2d-360">Provider di configurazione chiave per file</span><span class="sxs-lookup"><span data-stu-id="a8c2d-360">Key-per-file configuration provider</span></span>

<span data-ttu-id="a8c2d-361">Il <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa i file di una directory come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-361">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="a8c2d-362">La chiave è il nome del file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-362">The key is the file name.</span></span> <span data-ttu-id="a8c2d-363">Il valore contiene il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-363">The value contains the file's contents.</span></span> <span data-ttu-id="a8c2d-364">Il provider di configurazione chiave per file viene usato negli scenari di hosting di Docker.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-364">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="a8c2d-365">Per attivare la configurazione chiave-per-file, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-365">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="a8c2d-366">Il `directoryPath` per i file deve essere un percorso assoluto.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-366">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="a8c2d-367">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-367">Overloads permit specifying:</span></span>

* <span data-ttu-id="a8c2d-368">Un delegato `Action<KeyPerFileConfigurationSource>` che configura l'origine.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-368">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="a8c2d-369">Se la directory è facoltativa e il percorso della directory.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-369">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="a8c2d-370">Il doppio carattere di sottolineatura (`__`) viene usato come delimitatore per le chiavi di configurazione nei nomi dei file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-370">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="a8c2d-371">Ad esempio, il nome di file `Logging__LogLevel__System` produce la chiave di configurazione `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-371">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="a8c2d-372">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-372">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="a8c2d-373">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="a8c2d-373">Memory configuration provider</span></span>

<span data-ttu-id="a8c2d-374">Il <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una raccolta in memoria come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-374">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="a8c2d-375">Il codice seguente aggiunge una raccolta di memoria al sistema di configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-375">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="a8c2d-376">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza le impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-376">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="a8c2d-377">Nel codice precedente, `config.AddInMemoryCollection(Dict)` viene aggiunto dopo i [provider di configurazione predefiniti](#default).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-377">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="a8c2d-378">Per un esempio di ordinamento dei provider di configurazione, vedere [provider di configurazione JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-378">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="a8c2d-379">Vedere [associare una matrice](#boa) per un altro esempio di utilizzo di `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-379">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="a8c2d-380">GetValue</span><span class="sxs-lookup"><span data-stu-id="a8c2d-380">GetValue</span></span>

<span data-ttu-id="a8c2d-381">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) estrae un singolo valore dalla configurazione con una chiave specificata e lo converte nel tipo specificato:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-381">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="a8c2d-382">Nel codice precedente, se `NumberKey` non è stato trovato nella configurazione, viene usato il valore predefinito di `99` .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-382">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="a8c2d-383">GetSection, GetChildren ed Exists</span><span class="sxs-lookup"><span data-stu-id="a8c2d-383">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="a8c2d-384">Per gli esempi seguenti, prendere in considerazione i seguenti *MySubsection.jsnel* file:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-384">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="a8c2d-385">Il codice seguente aggiunge *MySubsection.js* ai provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-385">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="a8c2d-386">GetSection</span><span class="sxs-lookup"><span data-stu-id="a8c2d-386">GetSection</span></span>

<span data-ttu-id="a8c2d-387">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) restituisce una sottosezione di configurazione con la chiave della sottosezione specificata.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-387">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="a8c2d-388">Il codice seguente restituisce i valori per `section1` :</span><span class="sxs-lookup"><span data-stu-id="a8c2d-388">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="a8c2d-389">Il codice seguente restituisce i valori per `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="a8c2d-389">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="a8c2d-390">`GetSection` non restituisce mai `null`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-390">`GetSection` never returns `null`.</span></span> <span data-ttu-id="a8c2d-391">Se non viene trovata una sezione corrispondente, viene restituita una `IConfigurationSection` vuota.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-391">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="a8c2d-392">Quando `GetSection` restituisce una sezione corrispondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> non viene compilata.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-392">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="a8c2d-393">Quando la sezione esiste, vengono restituiti <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-393">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="a8c2d-394">GetChildren ed EXISTS</span><span class="sxs-lookup"><span data-stu-id="a8c2d-394">GetChildren and Exists</span></span>

<span data-ttu-id="a8c2d-395">Il codice seguente chiama [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) e restituisce i valori per `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="a8c2d-395">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="a8c2d-396">Il codice precedente chiama [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) per verificare che la sezione esista:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-396">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="a8c2d-397">Associare una matrice</span><span class="sxs-lookup"><span data-stu-id="a8c2d-397">Bind an array</span></span>

<span data-ttu-id="a8c2d-398">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supporta l'associazione di matrici a oggetti usando gli indici di matrice nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-398">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="a8c2d-399">Qualsiasi formato di matrice che espone un segmento di chiave numerica è in grado di associare array a una matrice di classi [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-399">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="a8c2d-400">Prendere *in considerazioneMyArray.js* dal [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="a8c2d-400">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="a8c2d-401">Il codice seguente aggiunge *MyArray.js* ai provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-401">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="a8c2d-402">Il codice seguente legge la configurazione e Visualizza i valori:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-402">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="a8c2d-403">Il codice precedente restituisce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-403">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="a8c2d-404">Nell'output precedente index 3 ha un valore `value40` corrispondente a `"4": "value40",` in *MyArray.json*.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-404">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="a8c2d-405">Gli indici di matrice associati sono continui e non sono associati all'indice della chiave di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-405">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="a8c2d-406">Il binder di configurazione non è in grado di associare valori null o di creare voci null negli oggetti associati</span><span class="sxs-lookup"><span data-stu-id="a8c2d-406">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="a8c2d-407">Il codice seguente carica la `array:entries` configurazione con il <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-407">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="a8c2d-408">Il codice seguente legge la configurazione in `arrayDict` `Dictionary` e Visualizza i valori:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-408">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="a8c2d-409">Il codice precedente restituisce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-409">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="a8c2d-410">L'indice &num;3 nell'oggetto associato contiene i dati di configurazione per la chiave di configurazione `array:4` e il relativo valore `value4`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-410">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="a8c2d-411">Quando i dati di configurazione contenenti una matrice sono associati, gli indici di matrice nelle chiavi di configurazione vengono usati per scorrere i dati di configurazione durante la creazione dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-411">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="a8c2d-412">Un valore null non può essere mantenuto nei dati di configurazione e una voce con valore null non viene creata in un oggetto associato quando una matrice nelle chiavi di configurazione ignora uno o più indici.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-412">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="a8c2d-413">È possibile specificare l'elemento di configurazione mancante per &num; l'indice 3 prima di eseguire il binding all' `ArrayExample` istanza da qualsiasi provider di configurazione che legga la &num; coppia chiave/valore dell'indice 3.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-413">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="a8c2d-414">Prendere in considerazione i seguenti *Value3.jssul* file dal Download di esempio:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-414">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="a8c2d-415">Il codice seguente include la configurazione per *Value3.jsin* e `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="a8c2d-415">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="a8c2d-416">Il codice seguente legge la configurazione precedente e Visualizza i valori:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-416">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="a8c2d-417">Il codice precedente restituisce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-417">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="a8c2d-418">I provider di configurazione personalizzati non devono implementare l'associazione di matrici.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-418">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="a8c2d-419">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="a8c2d-419">Custom configuration provider</span></span>

<span data-ttu-id="a8c2d-420">L'app di esempio dimostra come creare un provider di configurazione di base che legge le coppie chiave-valore di configurazione da un database usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-420">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="a8c2d-421">Il provider ha le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-421">The provider has the following characteristics:</span></span>

* <span data-ttu-id="a8c2d-422">Il database in memoria di Entity Framework viene usato a scopo dimostrativo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-422">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="a8c2d-423">Per usare un database che richiede una stringa di connessione, implementare un `ConfigurationBuilder` secondario per fornire la stringa di connessione da un altro provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-423">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="a8c2d-424">Il provider legge una tabella di database in una configurazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-424">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="a8c2d-425">Il provider non esegue una query sul database per ogni chiave.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-425">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="a8c2d-426">Il ricaricamento in caso di modifica non è implementato, quindi l'aggiornamento del database dopo l'avvio dell'app non ha alcun effetto sulla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-426">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="a8c2d-427">Definire un'entità `EFConfigurationValue` per l'archiviazione dei valori di configurazione nel database.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-427">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="a8c2d-428">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-428">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="a8c2d-429">Aggiungere `EFConfigurationContext` per archiviare i valori configurati e accedervi.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-429">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="a8c2d-430">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-430">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="a8c2d-431">Creare una classe che implementi <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-431">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="a8c2d-432">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-432">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="a8c2d-433">Creare il provider di configurazione personalizzato ereditando da <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-433">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="a8c2d-434">Il provider di configurazione inizializza il database quando è vuoto.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-434">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="a8c2d-435">Poiché le [chiavi di configurazione non fanno distinzione tra maiuscole](#keys)e minuscole, il dizionario utilizzato per inizializzare il database viene creato con l'operatore di confronto senza distinzione tra maiuscole e minuscole ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-435">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="a8c2d-436">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-436">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="a8c2d-437">Un metodo di estensione `AddEFConfiguration` consente di aggiungere l'origine di configurazione a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-437">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="a8c2d-438">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-438">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="a8c2d-439">L'esempio di codice seguente mostra come usare il `EFConfigurationProvider` personalizzato in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-439">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="a8c2d-440">Accedi alla configurazione all'avvio</span><span class="sxs-lookup"><span data-stu-id="a8c2d-440">Access configuration in Startup</span></span>

<span data-ttu-id="a8c2d-441">Il codice seguente consente di visualizzare i dati di configurazione nei `Startup` metodi:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-441">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="a8c2d-442">Per un esempio di accesso alla configurazione usando metodi di servizio di avvio, vedere [Avvio dell'applicazione: Metodi pratici](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-442">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="a8c2d-443">Accedi alla configurazione nelle Razor pagine</span><span class="sxs-lookup"><span data-stu-id="a8c2d-443">Access configuration in Razor Pages</span></span>

<span data-ttu-id="a8c2d-444">Il codice seguente consente di visualizzare i dati di configurazione in una Razor pagina:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-444">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="a8c2d-445">Nel codice seguente, `MyOptions` viene aggiunto al contenitore del servizio con <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associato alla configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-445">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="a8c2d-446">Il markup seguente utilizza la [`@inject`](xref:mvc/views/razor#inject) Razor direttiva per risolvere e visualizzare i valori delle opzioni:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-446">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="a8c2d-447">Accedere alla configurazione in un file di visualizzazione MVC</span><span class="sxs-lookup"><span data-stu-id="a8c2d-447">Access configuration in a MVC view file</span></span>

<span data-ttu-id="a8c2d-448">Il codice seguente consente di visualizzare i dati di configurazione in una visualizzazione MVC:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-448">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="a8c2d-449">Configurare le opzioni con un delegato</span><span class="sxs-lookup"><span data-stu-id="a8c2d-449">Configure options with a delegate</span></span>

<span data-ttu-id="a8c2d-450">Le opzioni configurate in un delegato eseguono l'override dei valori impostati nei provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-450">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="a8c2d-451">La configurazione delle opzioni con un delegato è illustrata nell'esempio 2 nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-451">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="a8c2d-452">Nel codice seguente <xref:Microsoft.Extensions.Options.IConfigureOptions%601> viene aggiunto un servizio al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-452">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="a8c2d-453">Usa un delegato per configurare i valori per `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="a8c2d-453">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="a8c2d-454">Il codice seguente consente di visualizzare i valori delle opzioni:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-454">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="a8c2d-455">Nell'esempio precedente, i valori di `Option1` e `Option2` vengono specificati in *appsettings.json* e quindi sottoposti a override dal delegato configurato.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-455">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="a8c2d-456">Host e configurazione delle app</span><span class="sxs-lookup"><span data-stu-id="a8c2d-456">Host versus app configuration</span></span>

<span data-ttu-id="a8c2d-457">Prima che l'app venga configurata e avviata, viene configurato e avviato un *host*.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-457">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="a8c2d-458">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-458">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="a8c2d-459">Sia l'app che l'host vengono configurati tramite i provider di configurazione descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-459">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="a8c2d-460">Anche le coppie chiave-valore di configurazione dell'host sono incluse nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-460">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="a8c2d-461">Per altre informazioni su come vengono usati i provider di configurazione quando viene creato l'host e sugli effetti delle origini di configurazione sull'host di configurazione, vedere <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-461">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="a8c2d-462">Configurazione host predefinita</span><span class="sxs-lookup"><span data-stu-id="a8c2d-462">Default host configuration</span></span>

<span data-ttu-id="a8c2d-463">Per informazioni dettagliate sulla configurazione predefinita quando viene usato l'[host Web](xref:fundamentals/host/web-host), vedere la [versione di questo argomento per ASP.NET Core 2.2](?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-463">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="a8c2d-464">La configurazione dell'host viene specificata da:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-464">Host configuration is provided from:</span></span>
  * <span data-ttu-id="a8c2d-465">Variabili di ambiente con prefisso `DOTNET_` (ad esempio, `DOTNET_ENVIRONMENT` ) utilizzando il [provider di configurazione delle variabili di ambiente](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-465">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="a8c2d-466">Il prefisso (`DOTNET_`) viene rimosso al caricamento delle coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-466">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="a8c2d-467">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-467">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="a8c2d-468">La configurazione predefinita dell'host Web viene stabilita (`ConfigureWebHostDefaults`) nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-468">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="a8c2d-469">Kestrel viene usato come server Web e configurato con i provider di configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-469">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="a8c2d-470">Aggiungere il middleware di filtro host.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-470">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="a8c2d-471">Aggiungere il middleware delle intestazioni inoltrate se la variabile di ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è impostata su `true`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-471">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="a8c2d-472">Abilitare l'integrazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-472">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="a8c2d-473">Altra configurazione</span><span class="sxs-lookup"><span data-stu-id="a8c2d-473">Other configuration</span></span>

<span data-ttu-id="a8c2d-474">Questo argomento riguarda solo la *configurazione dell'app*.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-474">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="a8c2d-475">Altri aspetti dell'esecuzione e dell'hosting di app ASP.NET Core sono configurati usando i file di configurazione non trattati in questo argomento:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-475">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="a8c2d-476">*launch.js* / *launchSettings.jssu* sono i file di configurazione degli strumenti per l'ambiente di sviluppo, descritti di seguito:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-476">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="a8c2d-477">In <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-477">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="a8c2d-478">Nella documentazione in cui vengono usati i file per configurare ASP.NET Core app per scenari di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-478">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="a8c2d-479">*web.config* è un file di configurazione del server, descritto negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-479">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="a8c2d-480">Le variabili di ambiente impostate in *launchSettings.jssu* sostituiscono quelle impostate nell'ambiente di sistema.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-480">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="a8c2d-481">Per ulteriori informazioni sulla migrazione della configurazione dell'app da versioni precedenti di ASP.NET, vedere <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-481">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="a8c2d-482">Aggiungere la configurazione da un assembly esterno</span><span class="sxs-lookup"><span data-stu-id="a8c2d-482">Add configuration from an external assembly</span></span>

<span data-ttu-id="a8c2d-483">Un'implementazione <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> consente l'aggiunta di miglioramenti a un'app all'avvio, da un assembly esterno alla classe `Startup` dell'app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-483">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="a8c2d-484">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-484">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a8c2d-485">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a8c2d-485">Additional resources</span></span>

* [<span data-ttu-id="a8c2d-486">Codice sorgente configurazione</span><span class="sxs-lookup"><span data-stu-id="a8c2d-486">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a8c2d-487">La configurazione delle app in ASP.NET Core si basa su coppie chiave-valore stabilite dai *provider di configurazione*.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-487">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="a8c2d-488">I provider di configurazione leggono i dati di configurazione in coppie chiave-valore da un'ampia gamma di origini di configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-488">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="a8c2d-489">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="a8c2d-489">Azure Key Vault</span></span>
* <span data-ttu-id="a8c2d-490">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="a8c2d-490">Azure App Configuration</span></span>
* <span data-ttu-id="a8c2d-491">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="a8c2d-491">Command-line arguments</span></span>
* <span data-ttu-id="a8c2d-492">Provider personalizzati (installati o creati)</span><span class="sxs-lookup"><span data-stu-id="a8c2d-492">Custom providers (installed or created)</span></span>
* <span data-ttu-id="a8c2d-493">File della directory</span><span class="sxs-lookup"><span data-stu-id="a8c2d-493">Directory files</span></span>
* <span data-ttu-id="a8c2d-494">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="a8c2d-494">Environment variables</span></span>
* <span data-ttu-id="a8c2d-495">Oggetti .NET in memoria</span><span class="sxs-lookup"><span data-stu-id="a8c2d-495">In-memory .NET objects</span></span>
* <span data-ttu-id="a8c2d-496">File di impostazioni</span><span class="sxs-lookup"><span data-stu-id="a8c2d-496">Settings files</span></span>

<span data-ttu-id="a8c2d-497">I pacchetti di configurazione per gli scenari di provider di configurazione comuni ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sono inclusi nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-497">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a8c2d-498">Gli esempi di codice che seguono e quelli inclusi nell'app di esempio usano lo spazio dei nomi <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-498">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="a8c2d-499">Il *modello di opzioni* è un'estensione dei concetti di configurazione descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-499">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="a8c2d-500">Le opzioni usano le classi per rappresentare i gruppi di impostazioni correlate.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-500">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="a8c2d-501">Per altre informazioni, vedere <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-501">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="a8c2d-502">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a8c2d-502">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="a8c2d-503">Host e configurazione delle app</span><span class="sxs-lookup"><span data-stu-id="a8c2d-503">Host versus app configuration</span></span>

<span data-ttu-id="a8c2d-504">Prima che l'app venga configurata e avviata, viene configurato e avviato un *host*.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-504">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="a8c2d-505">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-505">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="a8c2d-506">Sia l'app che l'host vengono configurati tramite i provider di configurazione descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-506">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="a8c2d-507">Anche le coppie chiave-valore di configurazione dell'host sono incluse nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-507">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="a8c2d-508">Per altre informazioni su come vengono usati i provider di configurazione quando viene creato l'host e sugli effetti delle origini di configurazione sull'host di configurazione, vedere <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-508">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="a8c2d-509">Altra configurazione</span><span class="sxs-lookup"><span data-stu-id="a8c2d-509">Other configuration</span></span>

<span data-ttu-id="a8c2d-510">Questo argomento riguarda solo la *configurazione dell'app*.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-510">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="a8c2d-511">Altri aspetti dell'esecuzione e dell'hosting di app ASP.NET Core sono configurati usando i file di configurazione non trattati in questo argomento:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-511">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="a8c2d-512">*launch.js* / *launchSettings.jssu* sono i file di configurazione degli strumenti per l'ambiente di sviluppo, descritti di seguito:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-512">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="a8c2d-513">In <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-513">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="a8c2d-514">Nella documentazione in cui vengono usati i file per configurare ASP.NET Core app per scenari di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-514">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="a8c2d-515">*web.config* è un file di configurazione del server, descritto negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-515">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="a8c2d-516">Per ulteriori informazioni sulla migrazione della configurazione dell'app da versioni precedenti di ASP.NET, vedere <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-516">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="a8c2d-517">Configurazione predefinita</span><span class="sxs-lookup"><span data-stu-id="a8c2d-517">Default configuration</span></span>

<span data-ttu-id="a8c2d-518">Le app basate sui modelli [dotnet new](/dotnet/core/tools/dotnet-new) di ASP.NET Core chiamano <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> durante la compilazione di un host.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-518">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="a8c2d-519">`CreateDefaultBuilder` fornisce la configurazione predefinita per l'app nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-519">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="a8c2d-520">La configurazione seguente si applica alle app che usano l'[host Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-520">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="a8c2d-521">Per informazioni dettagliate sulla configurazione predefinita quando viene usato l'[host generico](xref:fundamentals/host/generic-host), vedere la [versione più recente di questo argomento](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-521">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="a8c2d-522">La configurazione dell'host viene specificata da:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-522">Host configuration is provided from:</span></span>
  * <span data-ttu-id="a8c2d-523">Variabili di ambiente con prefisso `ASPNETCORE_` (ad esempio `ASPNETCORE_ENVIRONMENT`) che usano il [provider di configurazione delle variabili di ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-523">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="a8c2d-524">Il prefisso (`ASPNETCORE_`) viene rimosso al caricamento delle coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-524">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="a8c2d-525">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="a8c2d-526">La configurazione dell'app viene fornita da:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-526">App configuration is provided from:</span></span>
  * <span data-ttu-id="a8c2d-527">*appsettings.json* utilizzando il [provider di configurazione file](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-527">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="a8c2d-528">*appsettings.{Ambiente}.json* mediante il [provider di configurazione dei file](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-528">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="a8c2d-529">[Strumento di gestione dei segreti](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development` usando l'assembly di ingresso.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-529">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="a8c2d-530">Variabili di ambiente che usano il [provider di configurazione delle variabili di ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-530">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="a8c2d-531">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-531">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="a8c2d-532">Security</span><span class="sxs-lookup"><span data-stu-id="a8c2d-532">Security</span></span>

<span data-ttu-id="a8c2d-533">Per proteggere i dati di configurazione sensibili, adottare le pratiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-533">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="a8c2d-534">Non archiviare mai la password o altri dati sensibili nel codice del provider di configurazione o in file di configurazione di testo normale.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-534">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="a8c2d-535">Non usare i segreti di produzione in ambienti di sviluppo o di test.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-535">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="a8c2d-536">Specificare i segreti all'esterno del progetto in modo che non possano essere inavvertitamente inviati a un repository del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-536">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="a8c2d-537">Per altre informazioni, vedere i seguenti argomenti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-537">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="a8c2d-538"><xref:security/app-secrets>: Include consigli sull'uso delle variabili di ambiente per archiviare dati riservati.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-538"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="a8c2d-539">Secret Manager usa il provider di configurazione dei file per archiviare i segreti utente in un file JSON nel sistema locale.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-539">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="a8c2d-540">Il provider di configurazione dei file è descritto più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-540">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="a8c2d-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) archivia in modo sicuro i segreti delle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="a8c2d-542">Per altre informazioni, vedere <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-542">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="a8c2d-543">Dati di configurazione gerarchici</span><span class="sxs-lookup"><span data-stu-id="a8c2d-543">Hierarchical configuration data</span></span>

<span data-ttu-id="a8c2d-544">L'API di configurazione è in grado di mantenere i dati di configurazione gerarchici rendendoli flat grazie all'uso di un delimitatore nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-544">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="a8c2d-545">Nel file JSON seguente, esistono quattro chiavi in una gerarchia strutturata di due sezioni:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-545">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="a8c2d-546">Quando il file viene letto nella configurazione, vengono create chiavi univoche per mantenere la struttura di dati gerarchici originale dell'origine di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-546">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="a8c2d-547">Le sezioni e le chiavi vengono rese flat usando due punti (`:`) per mantenere la struttura originale:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-547">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="a8c2d-548">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-548">section0:key0</span></span>
* <span data-ttu-id="a8c2d-549">section0:key1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-549">section0:key1</span></span>
* <span data-ttu-id="a8c2d-550">section1:key0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-550">section1:key0</span></span>
* <span data-ttu-id="a8c2d-551">section1:key1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-551">section1:key1</span></span>

<span data-ttu-id="a8c2d-552">I metodi <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sono disponibili per isolare le sezioni e gli elementi figlio di una sezione nei dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="a8c2d-553">Questi metodi sono descritti più avanti in [GetSection, GetChildren ed Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-553">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="a8c2d-554">Convenzioni</span><span class="sxs-lookup"><span data-stu-id="a8c2d-554">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="a8c2d-555">Origini e provider</span><span class="sxs-lookup"><span data-stu-id="a8c2d-555">Sources and providers</span></span>

<span data-ttu-id="a8c2d-556">All'avvio dell'app, le origini di configurazione vengono lette nell'ordine con cui sono specificati i rispettivi provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-556">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="a8c2d-557">I provider di configurazione che implementano il rilevamento delle modifiche sono in grado di ricaricare la configurazione quando viene modificata un'impostazione sottostante.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-557">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="a8c2d-558">Ad esempio, il provider di configurazione dei file (descritto più avanti in questo argomento) e il [provider di configurazione di Azure Key Vault](xref:security/key-vault-configuration) implementano il rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-558">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="a8c2d-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> è disponibile nel contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) dell'app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="a8c2d-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> può essere inserito in una Razor pagina <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> o MVC <xref:Microsoft.AspNetCore.Mvc.Controller> per ottenere la configurazione per la classe.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="a8c2d-561">Negli esempi seguenti il `_config` campo viene usato per accedere ai valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-561">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="a8c2d-562">I provider di configurazione non possono usare l'inserimento delle dipendenze, perché non è disponibile quando vengono configurati dall'host.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-562">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="a8c2d-563">Chiavi</span><span class="sxs-lookup"><span data-stu-id="a8c2d-563">Keys</span></span>

<span data-ttu-id="a8c2d-564">Le chiavi di configurazione adottano le convenzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-564">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="a8c2d-565">Per le chiavi non viene fatta distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-565">Keys are case-insensitive.</span></span> <span data-ttu-id="a8c2d-566">Ad esempio, `ConnectionString` e `connectionstring` vengono considerate chiavi equivalenti.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-566">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="a8c2d-567">Se un valore per la stessa chiave viene impostato dallo stesso provider di configurazione o da provider diversi, viene usato l'ultimo valore impostato per la chiave.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-567">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="a8c2d-568">Per altre informazioni sulle chiavi JSON duplicate, vedere [questo problema di GitHub](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-568">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="a8c2d-569">Chiavi gerarchiche</span><span class="sxs-lookup"><span data-stu-id="a8c2d-569">Hierarchical keys</span></span>
  * <span data-ttu-id="a8c2d-570">Nell'ambito dell'API di configurazione, il separatore due punti (`:`) funziona in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-570">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="a8c2d-571">Nelle variabili di ambiente, un separatore due punti potrebbe non funzionare in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-571">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="a8c2d-572">Il doppio carattere di sottolineatura (`__`) è supportato da tutte le piattaforme e viene convertito automaticamente nei due punti.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-572">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="a8c2d-573">In Azure Key Vault, le chiavi gerarchiche usano `--` (due trattini) come separatore.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-573">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="a8c2d-574">Scrivere il codice per sostituire i trattini con i due punti quando i segreti vengono caricati nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-574">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="a8c2d-575">Il <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supporta l'associazione di matrici agli oggetti usando gli indici delle matrici nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-575">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="a8c2d-576">L'associazione di matrici è descritta nella sezione [Associare una matrice a una classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-576">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="a8c2d-577">Valori</span><span class="sxs-lookup"><span data-stu-id="a8c2d-577">Values</span></span>

<span data-ttu-id="a8c2d-578">I valori di configurazione adottano le convenzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-578">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="a8c2d-579">I valori sono stringhe.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-579">Values are strings.</span></span>
* <span data-ttu-id="a8c2d-580">I valori null non possono essere archiviati nella configurazione o associati a oggetti.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-580">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="a8c2d-581">Provider</span><span class="sxs-lookup"><span data-stu-id="a8c2d-581">Providers</span></span>

<span data-ttu-id="a8c2d-582">La tabella seguente mostra i provider di configurazione disponibili per le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-582">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="a8c2d-583">Provider</span><span class="sxs-lookup"><span data-stu-id="a8c2d-583">Provider</span></span> | <span data-ttu-id="a8c2d-584">Fornisce la configurazione da&hellip;</span><span class="sxs-lookup"><span data-stu-id="a8c2d-584">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="a8c2d-585">[Provider di configurazione di Azure Key Vault](xref:security/key-vault-configuration) (argomenti *Sicurezza*)</span><span class="sxs-lookup"><span data-stu-id="a8c2d-585">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="a8c2d-586">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="a8c2d-586">Azure Key Vault</span></span> |
| <span data-ttu-id="a8c2d-587">[Provider di Configurazione app](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Documentazione di Azure)</span><span class="sxs-lookup"><span data-stu-id="a8c2d-587">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="a8c2d-588">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="a8c2d-588">Azure App Configuration</span></span> |
| [<span data-ttu-id="a8c2d-589">Provider di configurazione della riga di comando</span><span class="sxs-lookup"><span data-stu-id="a8c2d-589">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="a8c2d-590">Parametri della riga di comando</span><span class="sxs-lookup"><span data-stu-id="a8c2d-590">Command-line parameters</span></span> |
| [<span data-ttu-id="a8c2d-591">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="a8c2d-591">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="a8c2d-592">Origine personalizzata</span><span class="sxs-lookup"><span data-stu-id="a8c2d-592">Custom source</span></span> |
| [<span data-ttu-id="a8c2d-593">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="a8c2d-593">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="a8c2d-594">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="a8c2d-594">Environment variables</span></span> |
| [<span data-ttu-id="a8c2d-595">Provider di configurazione file</span><span class="sxs-lookup"><span data-stu-id="a8c2d-595">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="a8c2d-596">File (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="a8c2d-596">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="a8c2d-597">Provider di configurazione KeyPerFile</span><span class="sxs-lookup"><span data-stu-id="a8c2d-597">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="a8c2d-598">File della directory</span><span class="sxs-lookup"><span data-stu-id="a8c2d-598">Directory files</span></span> |
| [<span data-ttu-id="a8c2d-599">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="a8c2d-599">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="a8c2d-600">Raccolte in memoria</span><span class="sxs-lookup"><span data-stu-id="a8c2d-600">In-memory collections</span></span> |
| <span data-ttu-id="a8c2d-601">[Segreti utente (Secret Manager)](xref:security/app-secrets) (argomenti *Sicurezza*)</span><span class="sxs-lookup"><span data-stu-id="a8c2d-601">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="a8c2d-602">File nella directory dei profili utente</span><span class="sxs-lookup"><span data-stu-id="a8c2d-602">File in the user profile directory</span></span> |

<span data-ttu-id="a8c2d-603">Le origini di configurazione vengono lette nell'ordine in cui vengono specificati i rispetti provider di configurazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-603">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="a8c2d-604">I provider di configurazione descritti in questo argomento sono descritti in ordine alfabetico, non nell'ordine in cui il codice li dispone.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-604">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="a8c2d-605">Ordinare i provider di configurazione nel codice in base alle priorità per le origini di configurazione sottostanti richieste dall'app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-605">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="a8c2d-606">Una sequenza tipica di provider di configurazione è:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-606">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="a8c2d-607">File ( *appsettings.json* , *appSettings. { Environment}. JSON*, dove `{Environment}` è l'ambiente host corrente dell'app)</span><span class="sxs-lookup"><span data-stu-id="a8c2d-607">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="a8c2d-608">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a8c2d-608">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="a8c2d-609">[Segreti utente (Secret Manager)](xref:security/app-secrets) (solo nell'ambiente di sviluppo)</span><span class="sxs-lookup"><span data-stu-id="a8c2d-609">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="a8c2d-610">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="a8c2d-610">Environment variables</span></span>
1. <span data-ttu-id="a8c2d-611">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="a8c2d-611">Command-line arguments</span></span>

<span data-ttu-id="a8c2d-612">È pratica comune posizionare il provider di configurazione della riga di comando per ultimo in una serie di provider per consentire agli argomenti della riga di comando di sostituire la configurazione impostata da altri provider.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-612">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="a8c2d-613">La sequenza di provider precedente viene utilizzata quando viene inizializzato un nuovo generatore host con `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-613">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a8c2d-614">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-614">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="a8c2d-615">Configurare il generatore di host con UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="a8c2d-615">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="a8c2d-616">Per configurare il generatore di host, chiamare <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> sul generatore di host con la configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-616">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="a8c2d-617">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="a8c2d-617">ConfigureAppConfiguration</span></span>

<span data-ttu-id="a8c2d-618">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare i provider di configurazione dell'app, oltre a quelli aggiunti automaticamente da `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-618">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="a8c2d-619">Sostituire la configurazione precedente con argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="a8c2d-619">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="a8c2d-620">Per fornire la configurazione dell'app che può essere sostituita con argomenti della riga di comando, chiamare `AddCommandLine` per ultimo:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-620">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="a8c2d-621">Rimuovere i provider aggiunti da CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="a8c2d-621">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="a8c2d-622">Per rimuovere i provider aggiunti da `CreateDefaultBuilder` , chiamare prima [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) in [IConfigurationBuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="a8c2d-622">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="a8c2d-623">Utilizzare la configurazione durante l'avvio dell'app</span><span class="sxs-lookup"><span data-stu-id="a8c2d-623">Consume configuration during app startup</span></span>

<span data-ttu-id="a8c2d-624">La configurazione fornita all'app in `ConfigureAppConfiguration` è disponibile durante l'avvio dell'app, incluso `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-624">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a8c2d-625">Per altre informazioni, vedere la sezione [Accedere alla configurazione durante l'avvio](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-625">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="a8c2d-626">Provider di configurazione della riga di comando</span><span class="sxs-lookup"><span data-stu-id="a8c2d-626">Command-line Configuration Provider</span></span>

<span data-ttu-id="a8c2d-627"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carica la configurazione da coppie chiave-valore di argomenti della riga di comando in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-627">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="a8c2d-628">Per attivare la configurazione della riga di comando, metodo di estensione <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> viene chiamato su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-628">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a8c2d-629">`AddCommandLine` viene chiamato automaticamente quando viene chiamato il metodo `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-629">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="a8c2d-630">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-630">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="a8c2d-631">`CreateDefaultBuilder` carica anche:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-631">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="a8c2d-632">Configurazione facoltativa da *appsettings.json* e *appSettings. { Environment} file JSON* .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-632">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="a8c2d-633">[Segreti utente (Secret Manager)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-633">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="a8c2d-634">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-634">Environment variables.</span></span>

<span data-ttu-id="a8c2d-635">`CreateDefaultBuilder` aggiunge il provider di configurazione della riga di comando per ultimo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-635">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="a8c2d-636">Gli argomenti della riga di comando passati in fase di esecuzione sostituiscono la configurazione impostata dagli altri provider.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-636">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="a8c2d-637">`CreateDefaultBuilder` opera durante la creazione dell'host.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-637">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="a8c2d-638">Pertanto, la configurazione della riga di comando attivata da `CreateDefaultBuilder` può influire sul modo in cui l'host viene configurato.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-638">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="a8c2d-639">Per le app basate sui modelli di ASP.NET Core, `AddCommandLine` è già stato chiamato da `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-639">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a8c2d-640">Per aggiungere altri provider di configurazione e mantenere la possibilità di sostituire la configurazione di tali provider con argomenti della riga di comando, chiamare i provider aggiuntivi dell'app in `ConfigureAppConfiguration` e quindi chiamare `AddCommandLine` per ultimo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-640">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="a8c2d-641">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="a8c2d-641">**Example**</span></span>

<span data-ttu-id="a8c2d-642">L'app di esempio consente di sfruttare il metodo di servizio statico `CreateDefaultBuilder` per creare l'host, che include una chiamata a <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-642">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="a8c2d-643">Aprire un prompt dei comandi nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-643">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="a8c2d-644">Fornire un argomento della riga di comando per il comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-644">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="a8c2d-645">Dopo che l'app è in esecuzione, aprire un browser per l'app all'indirizzo `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-645">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="a8c2d-646">Notare che l'output contiene la coppia chiave-valore per l'argomento della riga di comando di configurazione fornito per `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-646">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="a8c2d-647">Argomenti</span><span class="sxs-lookup"><span data-stu-id="a8c2d-647">Arguments</span></span>

<span data-ttu-id="a8c2d-648">Il valore deve seguire un segno di uguale (`=`) o la chiave deve avere un prefisso (`--` o `/`) quando il valore segue uno spazio.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-648">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="a8c2d-649">Il valore non è necessario se viene usato un segno di uguale, ad esempio `CommandLineKey=`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-649">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="a8c2d-650">Prefisso chiave</span><span class="sxs-lookup"><span data-stu-id="a8c2d-650">Key prefix</span></span>               | <span data-ttu-id="a8c2d-651">Esempio</span><span class="sxs-lookup"><span data-stu-id="a8c2d-651">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="a8c2d-652">Nessun prefisso</span><span class="sxs-lookup"><span data-stu-id="a8c2d-652">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="a8c2d-653">Due trattini (`--`)</span><span class="sxs-lookup"><span data-stu-id="a8c2d-653">Two dashes (`--`)</span></span>        | <span data-ttu-id="a8c2d-654">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="a8c2d-654">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="a8c2d-655">Barra (`/`)</span><span class="sxs-lookup"><span data-stu-id="a8c2d-655">Forward slash (`/`)</span></span>      | <span data-ttu-id="a8c2d-656">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="a8c2d-656">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="a8c2d-657">Nello stesso comando, non mischiare coppie chiave-valore di argomenti della riga di comando che usano un segno di uguale con coppie chiave-valore che usano uno spazio.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-657">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="a8c2d-658">Comandi di esempio:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-658">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="a8c2d-659">Mapping di sostituzione</span><span class="sxs-lookup"><span data-stu-id="a8c2d-659">Switch mappings</span></span>

<span data-ttu-id="a8c2d-660">I mapping di sostituzione consentono di usare la logica di sostituzione del nome della chiave.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-660">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="a8c2d-661">Quando si compila manualmente la configurazione con un oggetto <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , fornire un dizionario di sostituzioni switch al <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-661">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="a8c2d-662">Quando viene utilizzato il dizionario dei mapping di sostituzione, nel dizionario viene controllata la presenza di una chiave corrispondente alla chiave fornita da un argomento della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-662">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="a8c2d-663">Se la chiave della riga di comando viene trovata nel dizionario, il valore del dizionario (la sostituzione della chiave) viene passato nuovamente per impostare la coppia chiave-valore nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-663">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="a8c2d-664">Un mapping di sostituzione è necessario per le chiavi della riga di comando con un trattino singolo (`-`) come prefisso.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-664">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="a8c2d-665">Regole principali del dizionario dei mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-665">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="a8c2d-666">Le sostituzioni devono iniziare con un trattino (`-`) o un trattino doppio (`--`).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-666">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="a8c2d-667">Il dizionario dei mapping di sostituzione non deve contenere chiavi duplicate.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-667">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="a8c2d-668">Creare un dizionario dei mapping di sostituzione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-668">Create a switch mappings dictionary.</span></span> <span data-ttu-id="a8c2d-669">Nell'esempio seguente vengono creati due mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-669">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="a8c2d-670">Quando viene creato l'host, chiamare `AddCommandLine` con il dizionario dei mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-670">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="a8c2d-671">Per le app che usano i mapping di sostituzione, la chiamata a `CreateDefaultBuilder` non deve passare argomenti.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-671">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="a8c2d-672">La chiamata `AddCommandLine` del metodo `CreateDefaultBuilder` non include sostituzioni mappate e non è possibile passare il dizionario dei mapping di sostituzione a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-672">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a8c2d-673">La soluzione consiste nel non passare gli argomenti a `CreateDefaultBuilder` consentendo invece al metodo `AddCommandLine` del metodo `ConfigurationBuilder` di elaborare entrambi gli argomenti e il dizionario dei mapping di sostituzione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-673">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="a8c2d-674">Il dizionario dei mapping di sostituzione creato contiene i dati visualizzati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-674">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="a8c2d-675">Chiave</span><span class="sxs-lookup"><span data-stu-id="a8c2d-675">Key</span></span>       | <span data-ttu-id="a8c2d-676">Valore</span><span class="sxs-lookup"><span data-stu-id="a8c2d-676">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="a8c2d-677">Se le chiavi con mapping di sostituzione vengono usate all'avvio dell'app, la configurazione riceve il valore di configurazione per la chiave fornita dal dizionario:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-677">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="a8c2d-678">Dopo aver eseguito il comando precedente, la configurazione contiene i valori mostrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-678">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="a8c2d-679">Chiave</span><span class="sxs-lookup"><span data-stu-id="a8c2d-679">Key</span></span>               | <span data-ttu-id="a8c2d-680">Valore</span><span class="sxs-lookup"><span data-stu-id="a8c2d-680">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="a8c2d-681">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="a8c2d-681">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="a8c2d-682"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carica la configurazione da coppie chiave-valore di variabili di ambiente in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-682">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="a8c2d-683">Per attivare la configurazione delle variabili di ambiente, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-683">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="a8c2d-684">[App Azure servizio](https://azure.microsoft.com/services/app-service/) consente di impostare le variabili di ambiente nel portale di Azure che possono eseguire l'override della configurazione dell'app usando il provider di configurazione delle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-684">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="a8c2d-685">Per altre informazioni, vedere [App di Azure: Eseguire l'override della configurazione delle app usando il portale di Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-685">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="a8c2d-686">`AddEnvironmentVariables` consente di caricare variabili di ambiente con prefisso `ASPNETCORE_` per la [configurazione host](#host-versus-app-configuration) quando viene inizializzato un nuovo generatore di host con l'[host Web](xref:fundamentals/host/web-host) e viene chiamato `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-686">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="a8c2d-687">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-687">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="a8c2d-688">`CreateDefaultBuilder` carica anche:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-688">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="a8c2d-689">Configurazione delle app dalle variabili di ambiente senza prefisso chiamando `AddEnvironmentVariables` senza prefisso.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-689">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="a8c2d-690">Configurazione facoltativa da *appsettings.json* e *appSettings. { Environment} file JSON* .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-690">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="a8c2d-691">[Segreti utente (Secret Manager)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-691">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="a8c2d-692">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-692">Command-line arguments.</span></span>

<span data-ttu-id="a8c2d-693">Il provider di configurazione delle variabili di ambiente viene chiamato dopo aver stabilito la configurazione dai segreti utente e dai file *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-693">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="a8c2d-694">La chiamata del provider in questa posizione consente alle variabili di ambiente lette in fase di esecuzione di sostituire la configurazione impostata dai segreti utente e dai file *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-694">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="a8c2d-695">Per fornire la configurazione dell'app da altre variabili di ambiente, chiamare i provider aggiuntivi dell'app in `ConfigureAppConfiguration` e chiamare `AddEnvironmentVariables` con il prefisso:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-695">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="a8c2d-696">Chiamare `AddEnvironmentVariables` Last per consentire alle variabili di ambiente con il prefisso specificato di eseguire l'override dei valori di altri provider.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-696">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="a8c2d-697">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="a8c2d-697">**Example**</span></span>

<span data-ttu-id="a8c2d-698">L'app di esempio consente di sfruttare il metodo di servizio statico `CreateDefaultBuilder` per creare l'host, che include una chiamata a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-698">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="a8c2d-699">Eseguire l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-699">Run the sample app.</span></span> <span data-ttu-id="a8c2d-700">Aprire un browser per l'app all'indirizzo `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-700">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="a8c2d-701">Notare che l'output contiene la coppia chiave-valore per la variabile di ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-701">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="a8c2d-702">Il valore riflette l'ambiente in cui l'app è in esecuzione, in genere `Development` durante l'esecuzione locale.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-702">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="a8c2d-703">Per limitare l'elenco delle variabili di ambiente restituito dall'app, l'app filtra le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-703">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="a8c2d-704">Vedere il file *Pages/Index.cshtml.cs* dell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-704">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="a8c2d-705">Per esporre tutte le variabili di ambiente disponibili per l'app, modificare `FilteredConfiguration` in *pages/index. cshtml. cs* come riportato di seguito:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-705">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="a8c2d-706">Prefissi</span><span class="sxs-lookup"><span data-stu-id="a8c2d-706">Prefixes</span></span>

<span data-ttu-id="a8c2d-707">Le variabili di ambiente caricate nella configurazione dell'app vengono filtrate quando si specifica un prefisso per il `AddEnvironmentVariables` metodo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-707">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="a8c2d-708">Ad esempio, per filtrare le variabili di ambiente in base al prefisso `CUSTOM_`, fornire il prefisso al provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-708">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="a8c2d-709">Il prefisso viene rimosso quando vengono create le coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-709">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="a8c2d-710">Quando viene creato il generatore di host, la configurazione dell'host viene fornita dalle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-710">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="a8c2d-711">Per altre informazioni sul prefisso usato per queste variabili di ambiente, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-711">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="a8c2d-712">**Prefissi della stringa di connessione**</span><span class="sxs-lookup"><span data-stu-id="a8c2d-712">**Connection string prefixes**</span></span>

<span data-ttu-id="a8c2d-713">L'API di configurazione prevede regole di elaborazione speciali per quattro variabili di ambiente di stringa di connessione coinvolte nella configurazione di stringhe di connessione di Azure per l'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-713">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="a8c2d-714">Le variabili di ambiente con i prefissi indicati nella tabella vengono caricate nell'app se non viene fornito alcun prefisso a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-714">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="a8c2d-715">Prefisso della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="a8c2d-715">Connection string prefix</span></span> | <span data-ttu-id="a8c2d-716">Provider</span><span class="sxs-lookup"><span data-stu-id="a8c2d-716">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="a8c2d-717">Provider personalizzato</span><span class="sxs-lookup"><span data-stu-id="a8c2d-717">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="a8c2d-718">MySQL</span><span class="sxs-lookup"><span data-stu-id="a8c2d-718">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="a8c2d-719">Database SQL di Azure</span><span class="sxs-lookup"><span data-stu-id="a8c2d-719">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="a8c2d-720">SQL Server</span><span class="sxs-lookup"><span data-stu-id="a8c2d-720">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="a8c2d-721">Quando una variabile di ambiente viene individuata e caricata nella configurazione con uno qualsiasi dei quattro prefissi indicati nella tabella:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-721">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="a8c2d-722">La chiave di configurazione viene creata rimuovendo il prefisso della variabile di ambiente e aggiungendo una sezione per la chiave di configurazione (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-722">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="a8c2d-723">Viene creata una nuova coppia chiave-valore della configurazione che rappresenta il provider di connessione al database (ad eccezione di `CUSTOMCONNSTR_`, che non ha un provider dichiarato).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-723">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="a8c2d-724">Chiave della variabile di ambiente</span><span class="sxs-lookup"><span data-stu-id="a8c2d-724">Environment variable key</span></span> | <span data-ttu-id="a8c2d-725">Chiave di configurazione convertita</span><span class="sxs-lookup"><span data-stu-id="a8c2d-725">Converted configuration key</span></span> | <span data-ttu-id="a8c2d-726">Voce di configurazione del provider</span><span class="sxs-lookup"><span data-stu-id="a8c2d-726">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a8c2d-727">Voce di configurazione non creata.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-727">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a8c2d-728">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-728">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="a8c2d-729">Valore: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="a8c2d-729">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a8c2d-730">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-730">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="a8c2d-731">Valore: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="a8c2d-731">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a8c2d-732">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-732">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="a8c2d-733">Valore: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="a8c2d-733">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="a8c2d-734">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="a8c2d-734">**Example**</span></span>

<span data-ttu-id="a8c2d-735">Nel server viene creata una variabile di ambiente della stringa di connessione personalizzata:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-735">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="a8c2d-736">Nome: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="a8c2d-736">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="a8c2d-737">Valore: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="a8c2d-737">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="a8c2d-738">Se `IConfiguration` viene inserito e assegnato a un campo denominato `_config` , leggere il valore:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-738">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="a8c2d-739">Provider di configurazione dei file</span><span class="sxs-lookup"><span data-stu-id="a8c2d-739">File Configuration Provider</span></span>

<span data-ttu-id="a8c2d-740"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> è la classe base per il caricamento della configurazione dal file system.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-740"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="a8c2d-741">I provider di configurazione seguenti sono dedicati per specifici tipi di file:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-741">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="a8c2d-742">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="a8c2d-742">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="a8c2d-743">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="a8c2d-743">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="a8c2d-744">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="a8c2d-744">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="a8c2d-745">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="a8c2d-745">INI Configuration Provider</span></span>

<span data-ttu-id="a8c2d-746"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carica la configurazione da coppie chiave-valore di file INI in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-746">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="a8c2d-747">Per attivare la configurazione di file INI, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-747">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a8c2d-748">I due punti possono essere usati come delimitatore di sezione nella configurazione di file INI.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-748">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="a8c2d-749">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-749">Overloads permit specifying:</span></span>

* <span data-ttu-id="a8c2d-750">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-750">Whether the file is optional.</span></span>
* <span data-ttu-id="a8c2d-751">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-751">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="a8c2d-752">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider> usato per accedere al file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-752">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="a8c2d-753">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-753">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="a8c2d-754">Un esempio generico di un file di configurazione INI:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-754">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="a8c2d-755">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-755">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a8c2d-756">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-756">section0:key0</span></span>
* <span data-ttu-id="a8c2d-757">section0:key1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-757">section0:key1</span></span>
* <span data-ttu-id="a8c2d-758">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="a8c2d-758">section1:subsection:key</span></span>
* <span data-ttu-id="a8c2d-759">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="a8c2d-759">section2:subsection0:key</span></span>
* <span data-ttu-id="a8c2d-760">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="a8c2d-760">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="a8c2d-761">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="a8c2d-761">JSON Configuration Provider</span></span>

<span data-ttu-id="a8c2d-762">Il <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carica la configurazione da coppie chiave-valore di file JSON in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-762">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="a8c2d-763">Per attivare la configurazione di file JSON, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-763">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a8c2d-764">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-764">Overloads permit specifying:</span></span>

* <span data-ttu-id="a8c2d-765">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-765">Whether the file is optional.</span></span>
* <span data-ttu-id="a8c2d-766">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-766">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="a8c2d-767">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider> usato per accedere al file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-767">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="a8c2d-768">`AddJsonFile` viene chiamato automaticamente due volte quando viene inizializzato un nuovo generatore host con `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-768">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a8c2d-769">Il metodo viene chiamato per caricare la configurazione da:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-769">The method is called to load configuration from:</span></span>

* <span data-ttu-id="a8c2d-770">*appsettings.json*: Questo file viene letto per primo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-770">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="a8c2d-771">La versione dell'ambiente del file può sostituire i valori forniti dal *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-771">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="a8c2d-772">*appSettings. {Environment}. JSON*: la versione dell'ambiente del file è stata caricata in base a [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-772">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="a8c2d-773">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-773">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="a8c2d-774">`CreateDefaultBuilder` carica anche:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-774">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="a8c2d-775">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-775">Environment variables.</span></span>
* <span data-ttu-id="a8c2d-776">[Segreti utente (Secret Manager)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-776">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="a8c2d-777">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-777">Command-line arguments.</span></span>

<span data-ttu-id="a8c2d-778">Il provider di configurazione JSON viene stabilito per primo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-778">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="a8c2d-779">I segreti utente, le variabili di ambiente e gli argomenti della riga di comando sostituiscono quindi la configurazione impostata dai file *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-779">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="a8c2d-780">Chiamare `ConfigureAppConfiguration` quando si compila l'host per specificare la configurazione dell'app per i file diversi da *appsettings.json* e *appSettings. { Environment}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-780">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="a8c2d-781">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="a8c2d-781">**Example**</span></span>

<span data-ttu-id="a8c2d-782">L'app di esempio sfrutta il metodo di convenienza statica `CreateDefaultBuilder` per compilare l'host, che include due chiamate a `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="a8c2d-782">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="a8c2d-783">La prima chiamata a `AddJsonFile` carica la configurazione da *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="a8c2d-783">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="a8c2d-784">La seconda chiamata a `AddJsonFile` carica la configurazione da *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-784">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="a8c2d-785">Per *appsettings.Development.js* nell'app di esempio, viene caricato il file seguente:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-785">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="a8c2d-786">Eseguire l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-786">Run the sample app.</span></span> <span data-ttu-id="a8c2d-787">Aprire un browser per l'app all'indirizzo `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-787">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="a8c2d-788">L'output contiene coppie chiave-valore per la configurazione basata sull'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-788">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="a8c2d-789">Il livello di registrazione della chiave `Logging:LogLevel:Default` è `Debug` quando si esegue l'app nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-789">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="a8c2d-790">Eseguire di nuovo l'app di esempio nell'ambiente di produzione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-790">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="a8c2d-791">Aprire le *Proprietà/launchSettings.jssu* file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-791">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="a8c2d-792">Nel `ConfigurationSample` profilo, modificare il valore della variabile di `ASPNETCORE_ENVIRONMENT` ambiente in `Production` .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-792">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="a8c2d-793">Salvare il file ed eseguire l'app con `dotnet run` in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-793">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="a8c2d-794">Le impostazioni nel *appsettings.Development.jssu* non sostituiscono più le impostazioni in *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-794">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="a8c2d-795">Il livello di registrazione per la chiave `Logging:LogLevel:Default` è `Warning` .</span><span class="sxs-lookup"><span data-stu-id="a8c2d-795">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="a8c2d-796">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="a8c2d-796">XML Configuration Provider</span></span>

<span data-ttu-id="a8c2d-797">Il <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carica la configurazione da coppie chiave-valore di file XML in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-797">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="a8c2d-798">Per attivare la configurazione di file XML, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-798">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a8c2d-799">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-799">Overloads permit specifying:</span></span>

* <span data-ttu-id="a8c2d-800">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-800">Whether the file is optional.</span></span>
* <span data-ttu-id="a8c2d-801">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-801">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="a8c2d-802">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider> usato per accedere al file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-802">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="a8c2d-803">Il nodo radice del file di configurazione viene ignorato quando vengono create le coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-803">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="a8c2d-804">Non specificare una definizione DTD (Document Type Definition) o uno spazio dei nomi nel file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-804">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="a8c2d-805">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-805">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="a8c2d-806">I file di configurazione XML possono usare nomi di elementi distinti per le sezioni ripetute:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-806">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="a8c2d-807">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-807">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a8c2d-808">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-808">section0:key0</span></span>
* <span data-ttu-id="a8c2d-809">section0:key1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-809">section0:key1</span></span>
* <span data-ttu-id="a8c2d-810">section1:key0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-810">section1:key0</span></span>
* <span data-ttu-id="a8c2d-811">section1:key1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-811">section1:key1</span></span>

<span data-ttu-id="a8c2d-812">La ripetizione di elementi che usano lo stesso nome di elemento funziona se si usa l'attributo `name` per distinguere gli elementi:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-812">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="a8c2d-813">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-813">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a8c2d-814">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-814">section:section0:key:key0</span></span>
* <span data-ttu-id="a8c2d-815">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-815">section:section0:key:key1</span></span>
* <span data-ttu-id="a8c2d-816">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-816">section:section1:key:key0</span></span>
* <span data-ttu-id="a8c2d-817">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-817">section:section1:key:key1</span></span>

<span data-ttu-id="a8c2d-818">È possibile usare attributi per fornire valori:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-818">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="a8c2d-819">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-819">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a8c2d-820">key:attribute</span><span class="sxs-lookup"><span data-stu-id="a8c2d-820">key:attribute</span></span>
* <span data-ttu-id="a8c2d-821">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="a8c2d-821">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="a8c2d-822">Provider di configurazione KeyPerFile</span><span class="sxs-lookup"><span data-stu-id="a8c2d-822">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="a8c2d-823">Il <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa i file di una directory come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-823">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="a8c2d-824">La chiave è il nome del file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-824">The key is the file name.</span></span> <span data-ttu-id="a8c2d-825">Il valore contiene il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-825">The value contains the file's contents.</span></span> <span data-ttu-id="a8c2d-826">Il provider di configurazione KeyPerFile viene usato in scenari di hosting Docker.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-826">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="a8c2d-827">Per attivare la configurazione chiave-per-file, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-827">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="a8c2d-828">Il `directoryPath` per i file deve essere un percorso assoluto.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-828">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="a8c2d-829">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-829">Overloads permit specifying:</span></span>

* <span data-ttu-id="a8c2d-830">Un delegato `Action<KeyPerFileConfigurationSource>` che configura l'origine.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-830">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="a8c2d-831">Se la directory è facoltativa e il percorso della directory.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-831">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="a8c2d-832">Il doppio carattere di sottolineatura (`__`) viene usato come delimitatore per le chiavi di configurazione nei nomi dei file.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-832">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="a8c2d-833">Ad esempio, il nome di file `Logging__LogLevel__System` produce la chiave di configurazione `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-833">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="a8c2d-834">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-834">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="a8c2d-835">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="a8c2d-835">Memory Configuration Provider</span></span>

<span data-ttu-id="a8c2d-836">Il <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una raccolta in memoria come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-836">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="a8c2d-837">Per attivare la configurazione della raccolta in memoria, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-837">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a8c2d-838">Il provider di configurazione può essere inizializzato con un `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-838">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="a8c2d-839">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-839">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="a8c2d-840">Nell'esempio seguente viene creato un dizionario di configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-840">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="a8c2d-841">Il dizionario viene usato con una chiamata a `AddInMemoryCollection` per fornire la configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-841">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="a8c2d-842">GetValue</span><span class="sxs-lookup"><span data-stu-id="a8c2d-842">GetValue</span></span>

<span data-ttu-id="a8c2d-843">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) estrae un singolo valore dalla configurazione con una chiave specificata e lo converte nel tipo non di raccolta specificato.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-843">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="a8c2d-844">Un overload accetta un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-844">An overload accepts a default value.</span></span>

<span data-ttu-id="a8c2d-845">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-845">The following example:</span></span>

* <span data-ttu-id="a8c2d-846">Estrae il valore di stringa dalla configurazione con la chiave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-846">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="a8c2d-847">Se `NumberKey` non viene trovato nelle chiavi di configurazione, viene usato il valore predefinito di `99`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-847">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="a8c2d-848">Assegna al valore il tipo `int`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-848">Types the value as an `int`.</span></span>
* <span data-ttu-id="a8c2d-849">Memorizza il valore nella proprietà `NumberConfig` per l'uso da parte della pagina.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-849">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="a8c2d-850">GetSection, GetChildren ed Exists</span><span class="sxs-lookup"><span data-stu-id="a8c2d-850">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="a8c2d-851">Per gli esempi che seguono, prendere in considerazione il file JSON seguente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-851">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="a8c2d-852">Vengono trovate quattro chiavi in due sezioni, una delle quali include una coppia di sottosezioni:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-852">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="a8c2d-853">Quando il file viene letto nella configurazione, vengono create le chiavi gerarchiche univoche seguenti per contenere i valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-853">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="a8c2d-854">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-854">section0:key0</span></span>
* <span data-ttu-id="a8c2d-855">section0:key1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-855">section0:key1</span></span>
* <span data-ttu-id="a8c2d-856">section1:key0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-856">section1:key0</span></span>
* <span data-ttu-id="a8c2d-857">section1:key1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-857">section1:key1</span></span>
* <span data-ttu-id="a8c2d-858">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-858">section2:subsection0:key0</span></span>
* <span data-ttu-id="a8c2d-859">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-859">section2:subsection0:key1</span></span>
* <span data-ttu-id="a8c2d-860">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-860">section2:subsection1:key0</span></span>
* <span data-ttu-id="a8c2d-861">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-861">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="a8c2d-862">GetSection</span><span class="sxs-lookup"><span data-stu-id="a8c2d-862">GetSection</span></span>

<span data-ttu-id="a8c2d-863">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) estrae una sottosezione della configurazione con la chiave di sottosezione specificata.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-863">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="a8c2d-864">Per restituire una <xref:Microsoft.Extensions.Configuration.IConfigurationSection> che contiene solo le coppie chiave-valore in `section1`, chiamare `GetSection` e fornire il nome della sezione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-864">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="a8c2d-865">`configSection` non ha un valore, ma solo una chiave e un percorso.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-865">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="a8c2d-866">In modo analogo, per ottenere i valori per le chiavi in `section2:subsection0`, chiamare `GetSection` e fornire il percorso della sezione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-866">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="a8c2d-867">`GetSection` non restituisce mai `null`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-867">`GetSection` never returns `null`.</span></span> <span data-ttu-id="a8c2d-868">Se non viene trovata una sezione corrispondente, viene restituita una `IConfigurationSection` vuota.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-868">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="a8c2d-869">Quando `GetSection` restituisce una sezione corrispondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> non viene compilata.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-869">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="a8c2d-870">Quando la sezione esiste, vengono restituiti <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-870">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="a8c2d-871">GetChildren</span><span class="sxs-lookup"><span data-stu-id="a8c2d-871">GetChildren</span></span>

<span data-ttu-id="a8c2d-872">Una chiamata a [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) per `section2` ottiene una `IEnumerable<IConfigurationSection>` che include:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-872">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="a8c2d-873">Exists</span><span class="sxs-lookup"><span data-stu-id="a8c2d-873">Exists</span></span>

<span data-ttu-id="a8c2d-874">Usare [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) per determinare se esiste una sezione di configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-874">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="a8c2d-875">Considerati i dati di esempio, `sectionExists` è `false` perché non esiste una sezione `section2:subsection2` nei dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-875">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="a8c2d-876">Associazione a un oggetto grafico</span><span class="sxs-lookup"><span data-stu-id="a8c2d-876">Bind to an object graph</span></span>

<span data-ttu-id="a8c2d-877"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> è in grado di associare un intero oggetto grafico POCO.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-877"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="a8c2d-878">Come per l'associazione di un oggetto semplice, vengono associate solo le proprietà di lettura/scrittura pubbliche.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-878">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="a8c2d-879">L'esempio contiene un modello `TvShow` il cui oggetto grafico include `Metadata` e le classi `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="a8c2d-879">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="a8c2d-880">L'app di esempio ha un file *tvshow.xml* che contiene i dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-880">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="a8c2d-881">La configurazione viene associata all'intero oggetto grafico `TvShow` con il metodo `Bind`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-881">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="a8c2d-882">L'istanza associata viene assegnata a una proprietà per il rendering:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-882">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="a8c2d-883">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e restituisce il tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-883">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="a8c2d-884">`Get<T>` può essere più comodo che usare `Bind`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-884">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="a8c2d-885">Il codice seguente illustra come usare `Get<T>` con l'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-885">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="a8c2d-886">Associare una matrice a una classe</span><span class="sxs-lookup"><span data-stu-id="a8c2d-886">Bind an array to a class</span></span>

<span data-ttu-id="a8c2d-887">*L'app di esempio dimostra i concetti spiegati in questa sezione.*</span><span class="sxs-lookup"><span data-stu-id="a8c2d-887">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="a8c2d-888">Il <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supporta l'associazione di matrici agli oggetti usando gli indici delle matrici nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-888">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="a8c2d-889">Qualsiasi formato di matrice che espone un segmento di chiave numerico ( `:0:` , `:1:` , &hellip; `:{n}:` ) è in grado di associare array a una matrice di classi poco.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-889">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="a8c2d-890">L'associazione viene fornita per convenzione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-890">Binding is provided by convention.</span></span> <span data-ttu-id="a8c2d-891">I provider di configurazione personalizzati non devono implementare l'associazione di matrici.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-891">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="a8c2d-892">**Elaborazione di matrici in memoria**</span><span class="sxs-lookup"><span data-stu-id="a8c2d-892">**In-memory array processing**</span></span>

<span data-ttu-id="a8c2d-893">Prendere in considerazione le chiavi di configurazione e i valori indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-893">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="a8c2d-894">Chiave</span><span class="sxs-lookup"><span data-stu-id="a8c2d-894">Key</span></span>             | <span data-ttu-id="a8c2d-895">Valore</span><span class="sxs-lookup"><span data-stu-id="a8c2d-895">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="a8c2d-896">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-896">array:entries:0</span></span> | <span data-ttu-id="a8c2d-897">value0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-897">value0</span></span> |
| <span data-ttu-id="a8c2d-898">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-898">array:entries:1</span></span> | <span data-ttu-id="a8c2d-899">value1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-899">value1</span></span> |
| <span data-ttu-id="a8c2d-900">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="a8c2d-900">array:entries:2</span></span> | <span data-ttu-id="a8c2d-901">value2</span><span class="sxs-lookup"><span data-stu-id="a8c2d-901">value2</span></span> |
| <span data-ttu-id="a8c2d-902">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="a8c2d-902">array:entries:4</span></span> | <span data-ttu-id="a8c2d-903">value4</span><span class="sxs-lookup"><span data-stu-id="a8c2d-903">value4</span></span> |
| <span data-ttu-id="a8c2d-904">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="a8c2d-904">array:entries:5</span></span> | <span data-ttu-id="a8c2d-905">value5</span><span class="sxs-lookup"><span data-stu-id="a8c2d-905">value5</span></span> |

<span data-ttu-id="a8c2d-906">Queste chiavi e i valori vengono caricati nell'app di esempio usando il provider di configurazione della memoria:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-906">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="a8c2d-907">La matrice ignora un valore per l'indice &num;3.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-907">The array skips a value for index &num;3.</span></span> <span data-ttu-id="a8c2d-908">Lo strumento di associazione di configurazione non è in grado di associare valori null o di creare voci null negli oggetti associati, situazione che diventerà chiara tra poco quando viene illustrato il risultato dell'associazione di questa matrice a un oggetto.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-908">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="a8c2d-909">Nell'app di esempio, è disponibile una classe POCO per i dati di configurazione associati:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-909">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="a8c2d-910">I dati di configurazione sono associati all'oggetto:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-910">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="a8c2d-911">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) è anche possibile usare la sintassi, che comporta un codice più compatto:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-911">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="a8c2d-912">L'oggetto associato, un'istanza di `ArrayExample`, riceve i dati di matrice dalla configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-912">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="a8c2d-913">Indice di `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="a8c2d-913">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="a8c2d-914">Valore di `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="a8c2d-914">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="a8c2d-915">0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-915">0</span></span>                            | <span data-ttu-id="a8c2d-916">value0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-916">value0</span></span>                       |
| <span data-ttu-id="a8c2d-917">1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-917">1</span></span>                            | <span data-ttu-id="a8c2d-918">value1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-918">value1</span></span>                       |
| <span data-ttu-id="a8c2d-919">2</span><span class="sxs-lookup"><span data-stu-id="a8c2d-919">2</span></span>                            | <span data-ttu-id="a8c2d-920">value2</span><span class="sxs-lookup"><span data-stu-id="a8c2d-920">value2</span></span>                       |
| <span data-ttu-id="a8c2d-921">3</span><span class="sxs-lookup"><span data-stu-id="a8c2d-921">3</span></span>                            | <span data-ttu-id="a8c2d-922">value4</span><span class="sxs-lookup"><span data-stu-id="a8c2d-922">value4</span></span>                       |
| <span data-ttu-id="a8c2d-923">4</span><span class="sxs-lookup"><span data-stu-id="a8c2d-923">4</span></span>                            | <span data-ttu-id="a8c2d-924">value5</span><span class="sxs-lookup"><span data-stu-id="a8c2d-924">value5</span></span>                       |

<span data-ttu-id="a8c2d-925">L'indice &num;3 nell'oggetto associato contiene i dati di configurazione per la chiave di configurazione `array:4` e il relativo valore `value4`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-925">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="a8c2d-926">Quando i dati di configurazione contenenti una matrice vengono associati, gli indici di matrice nelle chiavi di configurazione vengono usati semplicemente per scorrere i dati di configurazione quando si crea l'oggetto.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-926">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="a8c2d-927">Un valore null non può essere mantenuto nei dati di configurazione e una voce con valore null non viene creata in un oggetto associato quando una matrice nelle chiavi di configurazione ignora uno o più indici.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-927">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="a8c2d-928">L'elemento di configurazione mancante per l'indice &num;3 può essere fornito prima dell'associazione all'istanza `ArrayExample` da qualsiasi provider di configurazione che produce la coppia chiave-valore corretta nella configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-928">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="a8c2d-929">Se il codice di esempio include un provider di configurazione JSON aggiuntivo con la coppia chiave-valore mancante, `ArrayExample.Entries` corrisponde alla matrice di configurazione completa:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-929">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="a8c2d-930">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-930">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="a8c2d-931">In `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-931">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="a8c2d-932">La coppia chiave-valore mostrata nella tabella viene caricata nella configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-932">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="a8c2d-933">Chiave</span><span class="sxs-lookup"><span data-stu-id="a8c2d-933">Key</span></span>             | <span data-ttu-id="a8c2d-934">Valore</span><span class="sxs-lookup"><span data-stu-id="a8c2d-934">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="a8c2d-935">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="a8c2d-935">array:entries:3</span></span> | <span data-ttu-id="a8c2d-936">value3</span><span class="sxs-lookup"><span data-stu-id="a8c2d-936">value3</span></span> |

<span data-ttu-id="a8c2d-937">Se l'istanza della classe `ArrayExample` viene associata dopo che il provider di configurazione JSON include la voce per l'indice &num;3, la matrice `ArrayExample.Entries` include il valore.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-937">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="a8c2d-938">Indice di `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="a8c2d-938">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="a8c2d-939">Valore di `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="a8c2d-939">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="a8c2d-940">0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-940">0</span></span>                            | <span data-ttu-id="a8c2d-941">value0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-941">value0</span></span>                       |
| <span data-ttu-id="a8c2d-942">1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-942">1</span></span>                            | <span data-ttu-id="a8c2d-943">value1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-943">value1</span></span>                       |
| <span data-ttu-id="a8c2d-944">2</span><span class="sxs-lookup"><span data-stu-id="a8c2d-944">2</span></span>                            | <span data-ttu-id="a8c2d-945">value2</span><span class="sxs-lookup"><span data-stu-id="a8c2d-945">value2</span></span>                       |
| <span data-ttu-id="a8c2d-946">3</span><span class="sxs-lookup"><span data-stu-id="a8c2d-946">3</span></span>                            | <span data-ttu-id="a8c2d-947">value3</span><span class="sxs-lookup"><span data-stu-id="a8c2d-947">value3</span></span>                       |
| <span data-ttu-id="a8c2d-948">4</span><span class="sxs-lookup"><span data-stu-id="a8c2d-948">4</span></span>                            | <span data-ttu-id="a8c2d-949">value4</span><span class="sxs-lookup"><span data-stu-id="a8c2d-949">value4</span></span>                       |
| <span data-ttu-id="a8c2d-950">5</span><span class="sxs-lookup"><span data-stu-id="a8c2d-950">5</span></span>                            | <span data-ttu-id="a8c2d-951">value5</span><span class="sxs-lookup"><span data-stu-id="a8c2d-951">value5</span></span>                       |

<span data-ttu-id="a8c2d-952">**Elaborazione di matrice JSON**</span><span class="sxs-lookup"><span data-stu-id="a8c2d-952">**JSON array processing**</span></span>

<span data-ttu-id="a8c2d-953">Se un file JSON contiene una matrice, vengono create chiavi di configurazione per gli elementi della matrice con un indice di sezione in base zero.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-953">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="a8c2d-954">Nel file di configurazione seguente, `subsection` è una matrice:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-954">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="a8c2d-955">Il provider di configurazione JSON legge i dati di configurazione nelle coppie chiave-valore seguenti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-955">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="a8c2d-956">Chiave</span><span class="sxs-lookup"><span data-stu-id="a8c2d-956">Key</span></span>                     | <span data-ttu-id="a8c2d-957">Valore</span><span class="sxs-lookup"><span data-stu-id="a8c2d-957">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="a8c2d-958">json_array:key</span><span class="sxs-lookup"><span data-stu-id="a8c2d-958">json_array:key</span></span>          | <span data-ttu-id="a8c2d-959">valueA</span><span class="sxs-lookup"><span data-stu-id="a8c2d-959">valueA</span></span> |
| <span data-ttu-id="a8c2d-960">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-960">json_array:subsection:0</span></span> | <span data-ttu-id="a8c2d-961">valueB</span><span class="sxs-lookup"><span data-stu-id="a8c2d-961">valueB</span></span> |
| <span data-ttu-id="a8c2d-962">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-962">json_array:subsection:1</span></span> | <span data-ttu-id="a8c2d-963">valueC</span><span class="sxs-lookup"><span data-stu-id="a8c2d-963">valueC</span></span> |
| <span data-ttu-id="a8c2d-964">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="a8c2d-964">json_array:subsection:2</span></span> | <span data-ttu-id="a8c2d-965">valueD</span><span class="sxs-lookup"><span data-stu-id="a8c2d-965">valueD</span></span> |

<span data-ttu-id="a8c2d-966">Nell'app di esempio, la classe POCO seguente è disponibile per associare le coppie chiave-valore della configurazione:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-966">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="a8c2d-967">Dopo l'associazione, `JsonArrayExample.Key` contiene il valore `valueA`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-967">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="a8c2d-968">I valori di sottosezione vengono archiviati nella proprietà matrice POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-968">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="a8c2d-969">Indice di `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="a8c2d-969">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="a8c2d-970">Valore di `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="a8c2d-970">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="a8c2d-971">0</span><span class="sxs-lookup"><span data-stu-id="a8c2d-971">0</span></span>                                   | <span data-ttu-id="a8c2d-972">valueB</span><span class="sxs-lookup"><span data-stu-id="a8c2d-972">valueB</span></span>                              |
| <span data-ttu-id="a8c2d-973">1</span><span class="sxs-lookup"><span data-stu-id="a8c2d-973">1</span></span>                                   | <span data-ttu-id="a8c2d-974">valueC</span><span class="sxs-lookup"><span data-stu-id="a8c2d-974">valueC</span></span>                              |
| <span data-ttu-id="a8c2d-975">2</span><span class="sxs-lookup"><span data-stu-id="a8c2d-975">2</span></span>                                   | <span data-ttu-id="a8c2d-976">valueD</span><span class="sxs-lookup"><span data-stu-id="a8c2d-976">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="a8c2d-977">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="a8c2d-977">Custom configuration provider</span></span>

<span data-ttu-id="a8c2d-978">L'app di esempio dimostra come creare un provider di configurazione di base che legge le coppie chiave-valore di configurazione da un database usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-978">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="a8c2d-979">Il provider ha le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-979">The provider has the following characteristics:</span></span>

* <span data-ttu-id="a8c2d-980">Il database in memoria di Entity Framework viene usato a scopo dimostrativo.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-980">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="a8c2d-981">Per usare un database che richiede una stringa di connessione, implementare un `ConfigurationBuilder` secondario per fornire la stringa di connessione da un altro provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-981">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="a8c2d-982">Il provider legge una tabella di database in una configurazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-982">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="a8c2d-983">Il provider non esegue una query sul database per ogni chiave.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-983">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="a8c2d-984">Il ricaricamento in caso di modifica non è implementato, quindi l'aggiornamento del database dopo l'avvio dell'app non ha alcun effetto sulla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-984">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="a8c2d-985">Definire un'entità `EFConfigurationValue` per l'archiviazione dei valori di configurazione nel database.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-985">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="a8c2d-986">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-986">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="a8c2d-987">Aggiungere `EFConfigurationContext` per archiviare i valori configurati e accedervi.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-987">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="a8c2d-988">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-988">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="a8c2d-989">Creare una classe che implementi <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-989">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="a8c2d-990">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-990">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="a8c2d-991">Creare il provider di configurazione personalizzato ereditando da <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-991">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="a8c2d-992">Il provider di configurazione inizializza il database quando è vuoto.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-992">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="a8c2d-993">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-993">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="a8c2d-994">Un metodo di estensione `AddEFConfiguration` consente di aggiungere l'origine di configurazione a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-994">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="a8c2d-995">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-995">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="a8c2d-996">L'esempio di codice seguente mostra come usare il `EFConfigurationProvider` personalizzato in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-996">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="a8c2d-997">Accedere alla configurazione durante l'avvio</span><span class="sxs-lookup"><span data-stu-id="a8c2d-997">Access configuration during startup</span></span>

<span data-ttu-id="a8c2d-998">Inserire `IConfiguration` nel costruttore `Startup` per accedere ai valori di configurazione in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-998">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a8c2d-999">Per accedere alla configurazione in `Startup.Configure`, inserire `IConfiguration` direttamente nel metodo o usare l'istanza dal costruttore:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-999">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="a8c2d-1000">Per un esempio di accesso alla configurazione usando metodi di servizio di avvio, vedere [Avvio dell'applicazione: Metodi pratici](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="a8c2d-1000">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="a8c2d-1001">Accedere alla configurazione in una Razor pagina di pagine o in una visualizzazione MVC</span><span class="sxs-lookup"><span data-stu-id="a8c2d-1001">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="a8c2d-1002">Per accedere alle impostazioni di configurazione in una Razor pagina di pagine o in una visualizzazione MVC, aggiungere una [direttiva using](xref:mvc/views/razor#using) ([riferimenti per C#: direttiva using](/dotnet/csharp/language-reference/keywords/using-directive)) per lo [ spazio dei nomiMicrosoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) e inserire <xref:Microsoft.Extensions.Configuration.IConfiguration> nella pagina o nella vista.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-1002">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="a8c2d-1003">In una Razor pagina di pagine:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-1003">In a Razor Pages page:</span></span>

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

<span data-ttu-id="a8c2d-1004">In una visualizzazione MVC:</span><span class="sxs-lookup"><span data-stu-id="a8c2d-1004">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="a8c2d-1005">Aggiungere la configurazione da un assembly esterno</span><span class="sxs-lookup"><span data-stu-id="a8c2d-1005">Add configuration from an external assembly</span></span>

<span data-ttu-id="a8c2d-1006">Un'implementazione <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> consente l'aggiunta di miglioramenti a un'app all'avvio, da un assembly esterno alla classe `Startup` dell'app.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-1006">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="a8c2d-1007">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a8c2d-1007">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a8c2d-1008">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a8c2d-1008">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
