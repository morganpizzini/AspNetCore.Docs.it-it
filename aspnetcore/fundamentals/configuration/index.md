---
title: Configurazione in ASP.NET Core
author: rick-anderson
description: Informazioni su come usare l'API di configurazione per configurare un'app ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/24/2020
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
ms.openlocfilehash: 97ee00dd37ed4eef1c013e0f45b598a79f3f260c
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035866"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="b97cb-103">Configurazione in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b97cb-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="b97cb-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="b97cb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b97cb-105">La configurazione in ASP.NET Core viene eseguita utilizzando uno o più [provider di configurazione](#cp).</span><span class="sxs-lookup"><span data-stu-id="b97cb-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="b97cb-106">I provider di configurazione leggono i dati di configurazione da coppie chiave-valore usando un'ampia gamma di origini di configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="b97cb-107">File di impostazioni, ad esempio *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="b97cb-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="b97cb-108">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="b97cb-108">Environment variables</span></span>
* <span data-ttu-id="b97cb-109">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="b97cb-109">Azure Key Vault</span></span>
* <span data-ttu-id="b97cb-110">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="b97cb-110">Azure App Configuration</span></span>
* <span data-ttu-id="b97cb-111">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="b97cb-111">Command-line arguments</span></span>
* <span data-ttu-id="b97cb-112">Provider personalizzati, installati o creati</span><span class="sxs-lookup"><span data-stu-id="b97cb-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="b97cb-113">File della directory</span><span class="sxs-lookup"><span data-stu-id="b97cb-113">Directory files</span></span>
* <span data-ttu-id="b97cb-114">Oggetti .NET in memoria</span><span class="sxs-lookup"><span data-stu-id="b97cb-114">In-memory .NET objects</span></span>

<span data-ttu-id="b97cb-115">In questo argomento vengono fornite informazioni sulla configurazione di in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b97cb-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="b97cb-116">Per informazioni sull'uso della configurazione nelle app console, vedere [configurazione di .NET](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="b97cb-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="b97cb-117">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b97cb-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="b97cb-118">Configurazione predefinita</span><span class="sxs-lookup"><span data-stu-id="b97cb-118">Default configuration</span></span>

<span data-ttu-id="b97cb-119">ASP.NET Core app Web create con [DotNet New](/dotnet/core/tools/dotnet-new) o Visual Studio generano il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="b97cb-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="b97cb-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fornisce la configurazione predefinita per l'app nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="b97cb-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="b97cb-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : aggiunge un oggetto esistente `IConfiguration` come origine.</span><span class="sxs-lookup"><span data-stu-id="b97cb-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="b97cb-122">Nel caso di configurazione predefinita, aggiunge la configurazione [host](#hvac) e la imposta come prima origine per la configurazione dell' _app_ .</span><span class="sxs-lookup"><span data-stu-id="b97cb-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="b97cb-123">[appsettings.json](#appsettingsjson) uso del [provider di configurazione JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b97cb-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="b97cb-124">*appSettings.* `Environment` *. JSON* con il [provider di configurazione JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b97cb-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="b97cb-125">Ad esempio, *appSettings*. ***Produzione \* \* _._json* e *appSettings*. \* \* \* sviluppo** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="b97cb-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="b97cb-126">[Segreti dell'app](xref:security/app-secrets) quando l'app viene eseguita nell' `Development` ambiente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="b97cb-127">Variabili di ambiente che usano il [provider di configurazione delle variabili di ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="b97cb-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="b97cb-128">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line).</span><span class="sxs-lookup"><span data-stu-id="b97cb-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="b97cb-129">I provider di configurazione aggiunti successivamente sostituiscono le impostazioni di chiave precedenti.</span><span class="sxs-lookup"><span data-stu-id="b97cb-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="b97cb-130">Se, ad esempio, `MyKey` è impostato in *appsettings.json* e nell'ambiente, viene utilizzato il valore dell'ambiente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="b97cb-131">Utilizzando i provider di configurazione predefiniti, il  [provider di configurazione della riga di comando](#clcp) esegue l'override di tutti gli altri provider.</span><span class="sxs-lookup"><span data-stu-id="b97cb-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="b97cb-132">Per ulteriori informazioni su `CreateDefaultBuilder` , vedere [impostazioni predefinite del generatore](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="b97cb-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="b97cb-133">Il codice seguente Visualizza i provider di configurazione abilitati nell'ordine in cui sono stati aggiunti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="b97cb-134">Si consideri il seguente *appsettings.json* file:</span><span class="sxs-lookup"><span data-stu-id="b97cb-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="b97cb-135">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="b97cb-136">Il valore predefinito <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carica la configurazione nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="b97cb-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="b97cb-137">*appSettings.* `Environment` *. JSON* : ad esempio, *appSettings*. \*\**Produzione \* \* _._json* e *appSettings*. \*\*\*\* \* sviluppo _._json \* file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="b97cb-138">La versione dell'ambiente del file viene caricata in base a [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="b97cb-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="b97cb-139">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="b97cb-140">*appSettings*. `Environment` . i valori *JSON* eseguono l'override delle chiavi in *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b97cb-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="b97cb-141">Ad esempio, per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="b97cb-141">For example, by default:</span></span>

* <span data-ttu-id="b97cb-142">Durante lo sviluppo, la configurazione *appSettings*. \***Development** _._json \* sovrascrive i valori presenti in *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b97cb-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="b97cb-143">In produzione, la configurazione *appSettings*. \***Production** _._json \* sovrascrive i valori presenti in *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b97cb-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="b97cb-144">Ad esempio, quando si distribuisce l'app in Azure.</span><span class="sxs-lookup"><span data-stu-id="b97cb-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="b97cb-145">Associare i dati di configurazione gerarchici usando il modello di opzioni</span><span class="sxs-lookup"><span data-stu-id="b97cb-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="b97cb-146">Utilizzando la configurazione [predefinita](#default) , *appsettings.json* e *appSettings.* `Environment` i file con *estensione JSON* sono abilitati con [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="b97cb-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="b97cb-147">Modifiche apportate a *appsettings.json* e *appSettings.* `Environment` file con *estensione JSON* \***dopo** l'avvio di _ l'app viene letta dal [provider di configurazione JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="b97cb-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file \***after** _ the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="b97cb-148">Per informazioni sull'aggiunta di altri file di configurazione JSON, vedere [provider di configurazione JSON](#jcp) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="b97cb-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="b97cb-149">Combinazione della raccolta di servizi</span><span class="sxs-lookup"><span data-stu-id="b97cb-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-user-secrets"></a><span data-ttu-id="b97cb-150">Sicurezza e segreti utente</span><span class="sxs-lookup"><span data-stu-id="b97cb-150">Security and user secrets</span></span>

<span data-ttu-id="b97cb-151">Linee guida sui dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-151">Configuration data guidelines:</span></span>

<span data-ttu-id="b97cb-152">_ Non archiviare mai le password o altri dati sensibili nel codice del provider di configurazione o nei file di configurazione di testo normale.</span><span class="sxs-lookup"><span data-stu-id="b97cb-152">_ Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="b97cb-153">Lo strumento di [gestione](xref:security/app-secrets) dei segreti può essere usato per archiviare i segreti in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-153">The [Secret Manager](xref:security/app-secrets) tool can be used to store secrets in development.</span></span>
* <span data-ttu-id="b97cb-154">Non usare i segreti di produzione in ambienti di sviluppo o di test.</span><span class="sxs-lookup"><span data-stu-id="b97cb-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="b97cb-155">Specificare i segreti all'esterno del progetto in modo che non possano essere inavvertitamente inviati a un repository del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="b97cb-156">Per [impostazione predefinita](#default), l'origine di configurazione dei segreti utente viene registrata dopo le origini di configurazione JSON.</span><span class="sxs-lookup"><span data-stu-id="b97cb-156">By [default](#default), the user secrets configuration source is registered after the JSON configuration sources.</span></span> <span data-ttu-id="b97cb-157">Pertanto, le chiavi dei segreti utente hanno la precedenza sulle chiavi in *appsettings.json* e *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="b97cb-157">Therefore, user secrets keys take precedence over keys in *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="b97cb-158">Per ulteriori informazioni sull'archiviazione di password o altri dati sensibili:</span><span class="sxs-lookup"><span data-stu-id="b97cb-158">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="b97cb-159"><xref:security/app-secrets>: Include consigli sull'uso delle variabili di ambiente per archiviare dati riservati.</span><span class="sxs-lookup"><span data-stu-id="b97cb-159"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="b97cb-160">Lo strumento Secret Manager usa il [provider di configurazione file](#fcp) per archiviare i segreti utente in un file JSON nel sistema locale.</span><span class="sxs-lookup"><span data-stu-id="b97cb-160">The Secret Manager tool uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="b97cb-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) archivia in modo sicuro i segreti delle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b97cb-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="b97cb-162">Per altre informazioni, vedere <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-162">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="b97cb-163">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="b97cb-163">Environment variables</span></span>

<span data-ttu-id="b97cb-164">Utilizzando la configurazione [predefinita](#default) , <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carica la configurazione dalle coppie chiave-valore della variabile di ambiente dopo la lettura *appsettings.json* , *appSettings.* `Environment` *. JSON* e [segreti utente](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="b97cb-164">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [user secrets](xref:security/app-secrets).</span></span> <span data-ttu-id="b97cb-165">Pertanto, i valori di chiave letti dall'ambiente eseguono l'override dei valori letti da *appsettings.json* , *appSettings.* `Environment` *. JSON* e segreti utente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-165">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and user secrets.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="b97cb-166">I `set` comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-166">The following `set` commands:</span></span>

* <span data-ttu-id="b97cb-167">Impostare le chiavi e i valori di ambiente dell' [esempio precedente](#appsettingsjson) in Windows.</span><span class="sxs-lookup"><span data-stu-id="b97cb-167">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="b97cb-168">Testare le impostazioni quando si usa il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="b97cb-168">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="b97cb-169">Il `dotnet run` comando deve essere eseguito nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="b97cb-169">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="b97cb-170">Le impostazioni di ambiente precedenti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-170">The preceding environment settings:</span></span>

* <span data-ttu-id="b97cb-171">Vengono impostati solo nei processi avviati dalla finestra di comando in cui sono stati impostati.</span><span class="sxs-lookup"><span data-stu-id="b97cb-171">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="b97cb-172">Non verrà letto dai browser avviati con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b97cb-172">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="b97cb-173">Per impostare le chiavi e i valori di ambiente in Windows, è possibile usare i comandi [Setx](/windows-server/administration/windows-commands/setx) seguenti.</span><span class="sxs-lookup"><span data-stu-id="b97cb-173">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="b97cb-174">Diversamente da `set` , `setx` le impostazioni sono rese permanente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-174">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="b97cb-175">`/M` imposta la variabile nell'ambiente di sistema.</span><span class="sxs-lookup"><span data-stu-id="b97cb-175">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="b97cb-176">Se l' `/M` opzione non viene usata, viene impostata una variabile di ambiente utente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-176">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="b97cb-177">Per verificare che i comandi precedenti eseguano l'override di *appsettings.json* e *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="b97cb-177">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="b97cb-178">Con Visual Studio: chiudere e riavviare Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b97cb-178">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="b97cb-179">Con l'interfaccia della riga di comando: avviare una nuova finestra di comando e immettere `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="b97cb-179">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="b97cb-180">Chiamare <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> con una stringa per specificare un prefisso per le variabili di ambiente:</span><span class="sxs-lookup"><span data-stu-id="b97cb-180">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="b97cb-181">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="b97cb-181">In the preceding code:</span></span>

* <span data-ttu-id="b97cb-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` viene aggiunto dopo i [provider di configurazione predefiniti](#default).</span><span class="sxs-lookup"><span data-stu-id="b97cb-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="b97cb-183">Per un esempio di ordinamento dei provider di configurazione, vedere [provider di configurazione JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="b97cb-183">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="b97cb-184">Le variabili di ambiente impostate con il `MyCustomPrefix_` prefisso sostituiscono i [provider di configurazione predefiniti](#default).</span><span class="sxs-lookup"><span data-stu-id="b97cb-184">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="b97cb-185">Sono incluse le variabili di ambiente senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="b97cb-185">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="b97cb-186">Il prefisso viene rimosso quando vengono lette le coppie chiave-valore di configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-186">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="b97cb-187">I comandi seguenti verificano il prefisso personalizzato:</span><span class="sxs-lookup"><span data-stu-id="b97cb-187">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="b97cb-188">La [configurazione predefinita](#default) carica le variabili di ambiente e gli argomenti della riga di comando preceduti da `DOTNET_` e `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="b97cb-188">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="b97cb-189">I `DOTNET_` `ASPNETCORE_` prefissi e vengono usati da ASP.NET Core per la [configurazione dell'host e dell'app](xref:fundamentals/host/generic-host#host-configuration), ma non per la configurazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-189">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="b97cb-190">Per ulteriori informazioni sulla configurazione dell'host e dell'app, vedere [.NET Generic Host](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="b97cb-190">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="b97cb-191">In [app Azure servizio](https://azure.microsoft.com/services/app-service/)selezionare **impostazione nuova applicazione** nella pagina **Impostazioni > configurazione** .</span><span class="sxs-lookup"><span data-stu-id="b97cb-191">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="b97cb-192">App Azure impostazioni dell'applicazione del servizio sono:</span><span class="sxs-lookup"><span data-stu-id="b97cb-192">Azure App Service application settings are:</span></span>

* <span data-ttu-id="b97cb-193">Crittografati a riposo e trasmessi su un canale crittografato.</span><span class="sxs-lookup"><span data-stu-id="b97cb-193">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="b97cb-194">Esposto come variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-194">Exposed as environment variables.</span></span>

<span data-ttu-id="b97cb-195">Per altre informazioni, vedere [App di Azure: Eseguire l'override della configurazione delle app usando il portale di Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="b97cb-195">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="b97cb-196">Per informazioni sulle stringhe di connessione del database di Azure, vedere [prefissi della stringa di connessione](#constr) .</span><span class="sxs-lookup"><span data-stu-id="b97cb-196">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="b97cb-197">Denominazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="b97cb-197">Naming of environment variables</span></span>

<span data-ttu-id="b97cb-198">I nomi delle variabili di ambiente riflettono la struttura di un *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-198">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="b97cb-199">Ogni elemento della gerarchia è separato da un doppio carattere di sottolineatura (preferibile) o da due punti.</span><span class="sxs-lookup"><span data-stu-id="b97cb-199">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="b97cb-200">Quando la struttura dell'elemento include una matrice, l'indice della matrice deve essere trattato come un nome di elemento aggiuntivo in questo percorso.</span><span class="sxs-lookup"><span data-stu-id="b97cb-200">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="b97cb-201">Si consideri il *appsettings.json* file seguente e i valori equivalenti rappresentati come variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-201">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

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

<span data-ttu-id="b97cb-202">**variabili di ambiente**</span><span class="sxs-lookup"><span data-stu-id="b97cb-202">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="b97cb-203">Variabili di ambiente impostate in launchSettings.js</span><span class="sxs-lookup"><span data-stu-id="b97cb-203">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="b97cb-204">Le variabili di ambiente impostate in *launchSettings.jssu* sostituiscono quelle impostate nell'ambiente di sistema.</span><span class="sxs-lookup"><span data-stu-id="b97cb-204">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="b97cb-205">Riga di comando</span><span class="sxs-lookup"><span data-stu-id="b97cb-205">Command-line</span></span>

<span data-ttu-id="b97cb-206">Utilizzando la configurazione [predefinita](#default) , <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carica la configurazione dalle coppie chiave-valore dell'argomento della riga di comando dopo le origini di configurazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-206">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="b97cb-207">*appsettings.json* e *appSettings*. `Environment` . file *JSON* .</span><span class="sxs-lookup"><span data-stu-id="b97cb-207">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="b97cb-208">[Segreti dell'app](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-208">[App secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="b97cb-209">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-209">Environment variables.</span></span>

<span data-ttu-id="b97cb-210">Per [impostazione predefinita](#default), i valori di configurazione impostati nei valori di configurazione di override della riga di comando impostati con tutti gli altri provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-210">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="b97cb-211">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="b97cb-211">Command-line arguments</span></span>

<span data-ttu-id="b97cb-212">Il comando seguente imposta le chiavi e i valori usando `=` :</span><span class="sxs-lookup"><span data-stu-id="b97cb-212">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="b97cb-213">Il comando seguente imposta le chiavi e i valori usando `/` :</span><span class="sxs-lookup"><span data-stu-id="b97cb-213">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="b97cb-214">Il comando seguente imposta le chiavi e i valori usando `--` :</span><span class="sxs-lookup"><span data-stu-id="b97cb-214">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="b97cb-215">Valore chiave:</span><span class="sxs-lookup"><span data-stu-id="b97cb-215">The key value:</span></span>

* <span data-ttu-id="b97cb-216">Deve seguire `=` oppure la chiave deve avere un prefisso `--` o `/` quando il valore segue uno spazio.</span><span class="sxs-lookup"><span data-stu-id="b97cb-216">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="b97cb-217">Non è obbligatorio se `=` si usa.</span><span class="sxs-lookup"><span data-stu-id="b97cb-217">Isn't required if `=` is used.</span></span> <span data-ttu-id="b97cb-218">Ad esempio, `MySetting=`</span><span class="sxs-lookup"><span data-stu-id="b97cb-218">For example, `MySetting=`.</span></span>

<span data-ttu-id="b97cb-219">All'interno dello stesso comando, non combinare coppie chiave-valore dell'argomento della riga di comando che usano `=` con coppie chiave-valore che usano uno spazio.</span><span class="sxs-lookup"><span data-stu-id="b97cb-219">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="b97cb-220">Mapping di sostituzione</span><span class="sxs-lookup"><span data-stu-id="b97cb-220">Switch mappings</span></span>

<span data-ttu-id="b97cb-221">I mapping switch consentono la logica di sostituzione del nome **chiave** .</span><span class="sxs-lookup"><span data-stu-id="b97cb-221">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="b97cb-222">Fornire un dizionario di sostituzioni switch al <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-222">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="b97cb-223">Quando viene utilizzato il dizionario dei mapping di sostituzione, nel dizionario viene controllata la presenza di una chiave corrispondente alla chiave fornita da un argomento della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="b97cb-223">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="b97cb-224">Se la chiave della riga di comando viene trovata nel dizionario, il valore del dizionario viene passato di nuovo per impostare la coppia chiave-valore nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-224">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="b97cb-225">Un mapping di sostituzione è necessario per le chiavi della riga di comando con un trattino singolo (`-`) come prefisso.</span><span class="sxs-lookup"><span data-stu-id="b97cb-225">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="b97cb-226">Regole principali del dizionario dei mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-226">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="b97cb-227">Le opzioni devono iniziare con `-` o `--` .</span><span class="sxs-lookup"><span data-stu-id="b97cb-227">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="b97cb-228">Il dizionario dei mapping di sostituzione non deve contenere chiavi duplicate.</span><span class="sxs-lookup"><span data-stu-id="b97cb-228">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="b97cb-229">Per usare un dizionario dei mapping switch, passarlo alla chiamata a `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="b97cb-229">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="b97cb-230">Il codice seguente mostra i valori chiave per le chiavi sostituite:</span><span class="sxs-lookup"><span data-stu-id="b97cb-230">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="b97cb-231">Il comando seguente funziona per testare la sostituzione della chiave:</span><span class="sxs-lookup"><span data-stu-id="b97cb-231">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="b97cb-232">Per le app che usano i mapping di sostituzione, la chiamata a `CreateDefaultBuilder` non deve passare argomenti.</span><span class="sxs-lookup"><span data-stu-id="b97cb-232">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="b97cb-233">La `CreateDefaultBuilder` chiamata al metodo `AddCommandLine` non include opzioni mappate e non è possibile passare il dizionario di mapping switch a `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b97cb-233">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b97cb-234">La soluzione non passa gli argomenti a `CreateDefaultBuilder` ma per consentire al metodo del `ConfigurationBuilder` metodo `AddCommandLine` di elaborare sia gli argomenti sia il dizionario di mapping delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="b97cb-234">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="b97cb-235">Dati di configurazione gerarchici</span><span class="sxs-lookup"><span data-stu-id="b97cb-235">Hierarchical configuration data</span></span>

<span data-ttu-id="b97cb-236">L'API di configurazione legge i dati di configurazione gerarchici rendendo flat i dati gerarchici con l'uso di un delimitatore nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-236">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="b97cb-237">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il seguente  *appsettings.json* file:</span><span class="sxs-lookup"><span data-stu-id="b97cb-237">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="b97cb-238">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-238">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="b97cb-239">Il modo migliore per leggere i dati di configurazione gerarchici consiste nell'usare il modello di opzioni.</span><span class="sxs-lookup"><span data-stu-id="b97cb-239">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="b97cb-240">Per altre informazioni, vedere [associare dati di configurazione gerarchici](#optpat) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="b97cb-240">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="b97cb-241">I metodi <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sono disponibili per isolare le sezioni e gli elementi figlio di una sezione nei dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-241"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="b97cb-242">Questi metodi sono descritti più avanti in [GetSection, GetChildren ed Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="b97cb-242">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="b97cb-243">Chiavi e valori di configurazione</span><span class="sxs-lookup"><span data-stu-id="b97cb-243">Configuration keys and values</span></span>

<span data-ttu-id="b97cb-244">Chiavi di configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-244">Configuration keys:</span></span>

* <span data-ttu-id="b97cb-245">Non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="b97cb-245">Are case-insensitive.</span></span> <span data-ttu-id="b97cb-246">Ad esempio, `ConnectionString` e `connectionstring` vengono considerate chiavi equivalenti.</span><span class="sxs-lookup"><span data-stu-id="b97cb-246">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="b97cb-247">Se una chiave e un valore vengono impostati in più provider di configurazione, viene utilizzato il valore dell'ultimo provider aggiunto.</span><span class="sxs-lookup"><span data-stu-id="b97cb-247">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="b97cb-248">Per ulteriori informazioni, vedere [configurazione predefinita](#default).</span><span class="sxs-lookup"><span data-stu-id="b97cb-248">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="b97cb-249">Chiavi gerarchiche</span><span class="sxs-lookup"><span data-stu-id="b97cb-249">Hierarchical keys</span></span>
  * <span data-ttu-id="b97cb-250">Nell'ambito dell'API di configurazione, il separatore due punti (`:`) funziona in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="b97cb-250">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="b97cb-251">Nelle variabili di ambiente, un separatore due punti potrebbe non funzionare in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="b97cb-251">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="b97cb-252">Un doppio carattere di sottolineatura, `__` , è supportato da tutte le piattaforme e viene convertito automaticamente in due punti `:` .</span><span class="sxs-lookup"><span data-stu-id="b97cb-252">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="b97cb-253">In Azure Key Vault, le chiavi gerarchiche utilizzano `--` come separatore.</span><span class="sxs-lookup"><span data-stu-id="b97cb-253">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="b97cb-254">Il [provider di configurazione Azure Key Vault](xref:security/key-vault-configuration) sostituisce automaticamente `--` con un `:` quando i segreti vengono caricati nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-254">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="b97cb-255">Il <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supporta l'associazione di matrici agli oggetti usando gli indici delle matrici nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-255">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b97cb-256">L'associazione di matrici è descritta nella sezione [Associare una matrice a una classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="b97cb-256">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="b97cb-257">Valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-257">Configuration values:</span></span>

* <span data-ttu-id="b97cb-258">Sono stringhe.</span><span class="sxs-lookup"><span data-stu-id="b97cb-258">Are strings.</span></span>
* <span data-ttu-id="b97cb-259">I valori null non possono essere archiviati nella configurazione o associati a oggetti.</span><span class="sxs-lookup"><span data-stu-id="b97cb-259">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="b97cb-260">Provider di configurazione</span><span class="sxs-lookup"><span data-stu-id="b97cb-260">Configuration providers</span></span>

<span data-ttu-id="b97cb-261">La tabella seguente mostra i provider di configurazione disponibili per le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b97cb-261">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="b97cb-262">Provider</span><span class="sxs-lookup"><span data-stu-id="b97cb-262">Provider</span></span> | <span data-ttu-id="b97cb-263">Fornisce la configurazione da</span><span class="sxs-lookup"><span data-stu-id="b97cb-263">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="b97cb-264">Provider di configurazione Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b97cb-264">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="b97cb-265">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="b97cb-265">Azure Key Vault</span></span> |
| [<span data-ttu-id="b97cb-266">Provider di configurazione app Azure</span><span class="sxs-lookup"><span data-stu-id="b97cb-266">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="b97cb-267">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="b97cb-267">Azure App Configuration</span></span> |
| [<span data-ttu-id="b97cb-268">Provider di configurazione della riga di comando</span><span class="sxs-lookup"><span data-stu-id="b97cb-268">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="b97cb-269">Parametri della riga di comando</span><span class="sxs-lookup"><span data-stu-id="b97cb-269">Command-line parameters</span></span> |
| [<span data-ttu-id="b97cb-270">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="b97cb-270">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="b97cb-271">Origine personalizzata</span><span class="sxs-lookup"><span data-stu-id="b97cb-271">Custom source</span></span> |
| [<span data-ttu-id="b97cb-272">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="b97cb-272">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="b97cb-273">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="b97cb-273">Environment variables</span></span> |
| [<span data-ttu-id="b97cb-274">Provider di configurazione file</span><span class="sxs-lookup"><span data-stu-id="b97cb-274">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="b97cb-275">File INI, JSON e XML</span><span class="sxs-lookup"><span data-stu-id="b97cb-275">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="b97cb-276">Provider di configurazione chiave per file</span><span class="sxs-lookup"><span data-stu-id="b97cb-276">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="b97cb-277">File della directory</span><span class="sxs-lookup"><span data-stu-id="b97cb-277">Directory files</span></span> |
| [<span data-ttu-id="b97cb-278">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="b97cb-278">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="b97cb-279">Raccolte in memoria</span><span class="sxs-lookup"><span data-stu-id="b97cb-279">In-memory collections</span></span> |
| [<span data-ttu-id="b97cb-280">Segreti utente</span><span class="sxs-lookup"><span data-stu-id="b97cb-280">User secrets</span></span>](xref:security/app-secrets) | <span data-ttu-id="b97cb-281">File nella directory dei profili utente</span><span class="sxs-lookup"><span data-stu-id="b97cb-281">File in the user profile directory</span></span> |

<span data-ttu-id="b97cb-282">Le origini di configurazione vengono lette nell'ordine in cui sono specificati i provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-282">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="b97cb-283">Ordinare i provider di configurazione nel codice in base alle priorità per le origini di configurazione sottostanti richieste dall'app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-283">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="b97cb-284">Una sequenza tipica di provider di configurazione è:</span><span class="sxs-lookup"><span data-stu-id="b97cb-284">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="b97cb-285">*appSettings*. `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="b97cb-285">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="b97cb-286">Segreti utente</span><span class="sxs-lookup"><span data-stu-id="b97cb-286">User secrets</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="b97cb-287">Variabili di ambiente che usano il [provider di configurazione delle variabili di ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="b97cb-287">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="b97cb-288">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b97cb-288">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="b97cb-289">Una procedura comune consiste nell'aggiungere il provider di configurazione della riga di comando per ultimo in una serie di provider per consentire agli argomenti della riga di comando di eseguire l'override del set di configurazione da parte degli altri provider.</span><span class="sxs-lookup"><span data-stu-id="b97cb-289">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="b97cb-290">La sequenza di provider precedente viene utilizzata nella [configurazione predefinita](#default).</span><span class="sxs-lookup"><span data-stu-id="b97cb-290">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="b97cb-291">Prefissi della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="b97cb-291">Connection string prefixes</span></span>

<span data-ttu-id="b97cb-292">L'API di configurazione dispone di regole di elaborazione speciali per quattro variabili di ambiente della stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-292">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="b97cb-293">Queste stringhe di connessione sono necessarie per configurare le stringhe di connessione di Azure per l'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-293">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="b97cb-294">Le variabili di ambiente con i prefissi visualizzati nella tabella vengono caricate nell'app con la [configurazione predefinita](#default) o quando non viene fornito alcun prefisso a `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="b97cb-294">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="b97cb-295">Prefisso della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="b97cb-295">Connection string prefix</span></span> | <span data-ttu-id="b97cb-296">Provider</span><span class="sxs-lookup"><span data-stu-id="b97cb-296">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="b97cb-297">Provider personalizzato</span><span class="sxs-lookup"><span data-stu-id="b97cb-297">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="b97cb-298">MySQL</span><span class="sxs-lookup"><span data-stu-id="b97cb-298">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="b97cb-299">Database SQL di Azure</span><span class="sxs-lookup"><span data-stu-id="b97cb-299">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="b97cb-300">SQL Server</span><span class="sxs-lookup"><span data-stu-id="b97cb-300">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="b97cb-301">Quando una variabile di ambiente viene individuata e caricata nella configurazione con uno qualsiasi dei quattro prefissi indicati nella tabella:</span><span class="sxs-lookup"><span data-stu-id="b97cb-301">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="b97cb-302">La chiave di configurazione viene creata rimuovendo il prefisso della variabile di ambiente e aggiungendo una sezione per la chiave di configurazione (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="b97cb-302">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="b97cb-303">Viene creata una nuova coppia chiave-valore della configurazione che rappresenta il provider di connessione al database (ad eccezione di `CUSTOMCONNSTR_`, che non ha un provider dichiarato).</span><span class="sxs-lookup"><span data-stu-id="b97cb-303">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="b97cb-304">Chiave della variabile di ambiente</span><span class="sxs-lookup"><span data-stu-id="b97cb-304">Environment variable key</span></span> | <span data-ttu-id="b97cb-305">Chiave di configurazione convertita</span><span class="sxs-lookup"><span data-stu-id="b97cb-305">Converted configuration key</span></span> | <span data-ttu-id="b97cb-306">Voce di configurazione del provider</span><span class="sxs-lookup"><span data-stu-id="b97cb-306">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b97cb-307">Voce di configurazione non creata.</span><span class="sxs-lookup"><span data-stu-id="b97cb-307">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b97cb-308">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="b97cb-308">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="b97cb-309">Valore: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="b97cb-309">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b97cb-310">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="b97cb-310">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="b97cb-311">Valore: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b97cb-311">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b97cb-312">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="b97cb-312">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="b97cb-313">Valore: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b97cb-313">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="b97cb-314">Provider di configurazione file</span><span class="sxs-lookup"><span data-stu-id="b97cb-314">File configuration provider</span></span>

<span data-ttu-id="b97cb-315"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> è la classe base per il caricamento della configurazione dal file system.</span><span class="sxs-lookup"><span data-stu-id="b97cb-315"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="b97cb-316">I provider di configurazione seguenti derivano da `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="b97cb-316">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="b97cb-317">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="b97cb-317">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="b97cb-318">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="b97cb-318">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="b97cb-319">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="b97cb-319">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="b97cb-320">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="b97cb-320">INI configuration provider</span></span>

<span data-ttu-id="b97cb-321"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carica la configurazione da coppie chiave-valore di file INI in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-321">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="b97cb-322">Il codice seguente cancella tutti i provider di configurazione e aggiunge diversi provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-322">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="b97cb-323">Nel codice precedente, le impostazioni nel *MyIniConfig.ini* e in  *MyIniConfig*. `Environment` i file *ini* vengono sottoposti a override dalle impostazioni nel:</span><span class="sxs-lookup"><span data-stu-id="b97cb-323">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="b97cb-324">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="b97cb-324">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="b97cb-325">[Provider di configurazione della riga di comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="b97cb-325">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="b97cb-326">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il file di *MyIniConfig.ini* seguente:</span><span class="sxs-lookup"><span data-stu-id="b97cb-326">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="b97cb-327">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-327">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="b97cb-328">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="b97cb-328">JSON configuration provider</span></span>

<span data-ttu-id="b97cb-329"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Carica la configurazione dalle coppie chiave-valore del file JSON.</span><span class="sxs-lookup"><span data-stu-id="b97cb-329">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="b97cb-330">Gli overload possono specificare:</span><span class="sxs-lookup"><span data-stu-id="b97cb-330">Overloads can specify:</span></span>

* <span data-ttu-id="b97cb-331">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-331">Whether the file is optional.</span></span>
* <span data-ttu-id="b97cb-332">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-332">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="b97cb-333">Osservare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="b97cb-333">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="b97cb-334">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="b97cb-334">The preceding code:</span></span>

* <span data-ttu-id="b97cb-335">Configura il provider di configurazione JSON per caricare il *MyConfig.jsnel* file con le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-335">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="b97cb-336">`optional: true`: Il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-336">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="b97cb-337">`reloadOnChange: true` : Il file viene ricaricato quando vengono salvate le modifiche.</span><span class="sxs-lookup"><span data-stu-id="b97cb-337">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="b97cb-338">Legge i [provider di configurazione predefiniti](#default) prima del *MyConfig.jssul* file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-338">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="b97cb-339">Impostazioni nell'impostazione *MyConfig.js* di sostituzione dei file nei provider di configurazione predefiniti, tra cui il [provider di configurazione delle variabili di ambiente](#evcp) e il provider di configurazione della riga di [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="b97cb-339">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="b97cb-340">In genere **non** si vuole che un file JSON personalizzato che esegue l'override dei valori impostati nel [provider di configurazione delle variabili di ambiente](#evcp) e nel provider di configurazione della riga di [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="b97cb-340">You typically \***don't** _ want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="b97cb-341">Il codice seguente cancella tutti i provider di configurazione e aggiunge diversi provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-341">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="b97cb-342">Nel codice precedente, le impostazioni nel _MyConfig.jsin \* e in  *config*. `Environment` . file *JSON* :</span><span class="sxs-lookup"><span data-stu-id="b97cb-342">In the preceding code, settings in the _MyConfig.json\* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="b97cb-343">Eseguire l'override delle impostazioni in *appsettings.json* e *appSettings*. `Environment` . file *JSON* .</span><span class="sxs-lookup"><span data-stu-id="b97cb-343">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="b97cb-344">Viene sottoposto a override dalle impostazioni del [provider di configurazione delle variabili di ambiente](#evcp) e del provider di configurazione della riga di [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="b97cb-344">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="b97cb-345">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene i  *MyConfig.jsseguenti nel* file:</span><span class="sxs-lookup"><span data-stu-id="b97cb-345">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="b97cb-346">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-346">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="b97cb-347">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="b97cb-347">XML configuration provider</span></span>

<span data-ttu-id="b97cb-348">Il <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carica la configurazione da coppie chiave-valore di file XML in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-348">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="b97cb-349">Il codice seguente cancella tutti i provider di configurazione e aggiunge diversi provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="b97cb-350">Nel codice precedente, le impostazioni nel *MyXMLFile.xml* e in  *MyXMLFile*. `Environment` i file *XML* vengono sottoposti a override dalle impostazioni in:</span><span class="sxs-lookup"><span data-stu-id="b97cb-350">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="b97cb-351">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="b97cb-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="b97cb-352">[Provider di configurazione della riga di comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="b97cb-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="b97cb-353">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il file di *MyXMLFile.xml* seguente:</span><span class="sxs-lookup"><span data-stu-id="b97cb-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="b97cb-354">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="b97cb-355">La ripetizione di elementi che usano lo stesso nome di elemento funziona se si usa l'attributo `name` per distinguere gli elementi:</span><span class="sxs-lookup"><span data-stu-id="b97cb-355">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="b97cb-356">Il codice seguente legge il file di configurazione precedente e visualizza le chiavi e i valori:</span><span class="sxs-lookup"><span data-stu-id="b97cb-356">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="b97cb-357">È possibile usare attributi per fornire valori:</span><span class="sxs-lookup"><span data-stu-id="b97cb-357">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="b97cb-358">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="b97cb-358">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b97cb-359">key:attribute</span><span class="sxs-lookup"><span data-stu-id="b97cb-359">key:attribute</span></span>
* <span data-ttu-id="b97cb-360">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="b97cb-360">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="b97cb-361">Provider di configurazione chiave per file</span><span class="sxs-lookup"><span data-stu-id="b97cb-361">Key-per-file configuration provider</span></span>

<span data-ttu-id="b97cb-362">Il <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa i file di una directory come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-362">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="b97cb-363">La chiave è il nome del file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-363">The key is the file name.</span></span> <span data-ttu-id="b97cb-364">Il valore contiene il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-364">The value contains the file's contents.</span></span> <span data-ttu-id="b97cb-365">Il provider di configurazione chiave per file viene usato negli scenari di hosting di Docker.</span><span class="sxs-lookup"><span data-stu-id="b97cb-365">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="b97cb-366">Per attivare la configurazione chiave-per-file, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-366">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="b97cb-367">Il `directoryPath` per i file deve essere un percorso assoluto.</span><span class="sxs-lookup"><span data-stu-id="b97cb-367">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="b97cb-368">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="b97cb-368">Overloads permit specifying:</span></span>

* <span data-ttu-id="b97cb-369">Un delegato `Action<KeyPerFileConfigurationSource>` che configura l'origine.</span><span class="sxs-lookup"><span data-stu-id="b97cb-369">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="b97cb-370">Se la directory è facoltativa e il percorso della directory.</span><span class="sxs-lookup"><span data-stu-id="b97cb-370">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="b97cb-371">Il doppio carattere di sottolineatura (`__`) viene usato come delimitatore per le chiavi di configurazione nei nomi dei file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-371">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="b97cb-372">Ad esempio, il nome di file `Logging__LogLevel__System` produce la chiave di configurazione `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-372">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="b97cb-373">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="b97cb-373">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="b97cb-374">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="b97cb-374">Memory configuration provider</span></span>

<span data-ttu-id="b97cb-375">Il <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una raccolta in memoria come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-375">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="b97cb-376">Il codice seguente aggiunge una raccolta di memoria al sistema di configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-376">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="b97cb-377">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza le impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-377">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="b97cb-378">Nel codice precedente, `config.AddInMemoryCollection(Dict)` viene aggiunto dopo i [provider di configurazione predefiniti](#default).</span><span class="sxs-lookup"><span data-stu-id="b97cb-378">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="b97cb-379">Per un esempio di ordinamento dei provider di configurazione, vedere [provider di configurazione JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="b97cb-379">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="b97cb-380">Vedere [associare una matrice](#boa) per un altro esempio di utilizzo di `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="b97cb-380">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="b97cb-381">GetValue</span><span class="sxs-lookup"><span data-stu-id="b97cb-381">GetValue</span></span>

<span data-ttu-id="b97cb-382">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) estrae un singolo valore dalla configurazione con una chiave specificata e lo converte nel tipo specificato:</span><span class="sxs-lookup"><span data-stu-id="b97cb-382">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="b97cb-383">Nel codice precedente, se `NumberKey` non è stato trovato nella configurazione, viene usato il valore predefinito di `99` .</span><span class="sxs-lookup"><span data-stu-id="b97cb-383">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="b97cb-384">GetSection, GetChildren ed Exists</span><span class="sxs-lookup"><span data-stu-id="b97cb-384">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="b97cb-385">Per gli esempi seguenti, prendere in considerazione i seguenti *MySubsection.jsnel* file:</span><span class="sxs-lookup"><span data-stu-id="b97cb-385">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="b97cb-386">Il codice seguente aggiunge *MySubsection.js* ai provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-386">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="b97cb-387">GetSection</span><span class="sxs-lookup"><span data-stu-id="b97cb-387">GetSection</span></span>

<span data-ttu-id="b97cb-388">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) restituisce una sottosezione di configurazione con la chiave della sottosezione specificata.</span><span class="sxs-lookup"><span data-stu-id="b97cb-388">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="b97cb-389">Il codice seguente restituisce i valori per `section1` :</span><span class="sxs-lookup"><span data-stu-id="b97cb-389">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="b97cb-390">Il codice seguente restituisce i valori per `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="b97cb-390">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="b97cb-391">`GetSection` non restituisce mai `null`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-391">`GetSection` never returns `null`.</span></span> <span data-ttu-id="b97cb-392">Se non viene trovata una sezione corrispondente, viene restituita una `IConfigurationSection` vuota.</span><span class="sxs-lookup"><span data-stu-id="b97cb-392">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="b97cb-393">Quando `GetSection` restituisce una sezione corrispondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> non viene compilata.</span><span class="sxs-lookup"><span data-stu-id="b97cb-393">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="b97cb-394">Quando la sezione esiste, vengono restituiti <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-394">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="b97cb-395">GetChildren ed EXISTS</span><span class="sxs-lookup"><span data-stu-id="b97cb-395">GetChildren and Exists</span></span>

<span data-ttu-id="b97cb-396">Il codice seguente chiama [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) e restituisce i valori per `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="b97cb-396">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="b97cb-397">Il codice precedente chiama [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) per verificare che la sezione esista:</span><span class="sxs-lookup"><span data-stu-id="b97cb-397">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="b97cb-398">Associare una matrice</span><span class="sxs-lookup"><span data-stu-id="b97cb-398">Bind an array</span></span>

<span data-ttu-id="b97cb-399">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supporta l'associazione di matrici a oggetti usando gli indici di matrice nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-399">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b97cb-400">Qualsiasi formato di matrice che espone un segmento di chiave numerica è in grado di associare array a una matrice di classi [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="b97cb-400">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="b97cb-401">Prendere *in considerazioneMyArray.js* dal [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="b97cb-401">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="b97cb-402">Il codice seguente aggiunge *MyArray.js* ai provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-402">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="b97cb-403">Il codice seguente legge la configurazione e Visualizza i valori:</span><span class="sxs-lookup"><span data-stu-id="b97cb-403">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="b97cb-404">Il codice precedente restituisce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="b97cb-404">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="b97cb-405">Nell'output precedente index 3 ha un valore `value40` corrispondente a `"4": "value40",` in *MyArray.json*.</span><span class="sxs-lookup"><span data-stu-id="b97cb-405">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="b97cb-406">Gli indici di matrice associati sono continui e non sono associati all'indice della chiave di configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-406">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="b97cb-407">Il binder di configurazione non è in grado di associare valori null o di creare voci null negli oggetti associati</span><span class="sxs-lookup"><span data-stu-id="b97cb-407">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="b97cb-408">Il codice seguente carica la `array:entries` configurazione con il <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-408">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="b97cb-409">Il codice seguente legge la configurazione in `arrayDict` `Dictionary` e Visualizza i valori:</span><span class="sxs-lookup"><span data-stu-id="b97cb-409">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="b97cb-410">Il codice precedente restituisce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="b97cb-410">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="b97cb-411">L'indice &num;3 nell'oggetto associato contiene i dati di configurazione per la chiave di configurazione `array:4` e il relativo valore `value4`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-411">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="b97cb-412">Quando i dati di configurazione contenenti una matrice sono associati, gli indici di matrice nelle chiavi di configurazione vengono usati per scorrere i dati di configurazione durante la creazione dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="b97cb-412">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="b97cb-413">Un valore null non può essere mantenuto nei dati di configurazione e una voce con valore null non viene creata in un oggetto associato quando una matrice nelle chiavi di configurazione ignora uno o più indici.</span><span class="sxs-lookup"><span data-stu-id="b97cb-413">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="b97cb-414">È possibile specificare l'elemento di configurazione mancante per &num; l'indice 3 prima di eseguire il binding all' `ArrayExample` istanza da qualsiasi provider di configurazione che legga la &num; coppia chiave/valore dell'indice 3.</span><span class="sxs-lookup"><span data-stu-id="b97cb-414">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="b97cb-415">Prendere in considerazione i seguenti *Value3.jssul* file dal Download di esempio:</span><span class="sxs-lookup"><span data-stu-id="b97cb-415">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="b97cb-416">Il codice seguente include la configurazione per *Value3.jsin* e `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="b97cb-416">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="b97cb-417">Il codice seguente legge la configurazione precedente e Visualizza i valori:</span><span class="sxs-lookup"><span data-stu-id="b97cb-417">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="b97cb-418">Il codice precedente restituisce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="b97cb-418">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="b97cb-419">I provider di configurazione personalizzati non devono implementare l'associazione di matrici.</span><span class="sxs-lookup"><span data-stu-id="b97cb-419">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="b97cb-420">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="b97cb-420">Custom configuration provider</span></span>

<span data-ttu-id="b97cb-421">L'app di esempio dimostra come creare un provider di configurazione di base che legge le coppie chiave-valore di configurazione da un database usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="b97cb-421">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="b97cb-422">Il provider ha le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-422">The provider has the following characteristics:</span></span>

* <span data-ttu-id="b97cb-423">Il database in memoria di Entity Framework viene usato a scopo dimostrativo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-423">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="b97cb-424">Per usare un database che richiede una stringa di connessione, implementare un `ConfigurationBuilder` secondario per fornire la stringa di connessione da un altro provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-424">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="b97cb-425">Il provider legge una tabella di database in una configurazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="b97cb-425">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="b97cb-426">Il provider non esegue una query sul database per ogni chiave.</span><span class="sxs-lookup"><span data-stu-id="b97cb-426">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="b97cb-427">Il ricaricamento in caso di modifica non è implementato, quindi l'aggiornamento del database dopo l'avvio dell'app non ha alcun effetto sulla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-427">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="b97cb-428">Definire un'entità `EFConfigurationValue` per l'archiviazione dei valori di configurazione nel database.</span><span class="sxs-lookup"><span data-stu-id="b97cb-428">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="b97cb-429">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="b97cb-429">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="b97cb-430">Aggiungere `EFConfigurationContext` per archiviare i valori configurati e accedervi.</span><span class="sxs-lookup"><span data-stu-id="b97cb-430">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="b97cb-431">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="b97cb-431">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="b97cb-432">Creare una classe che implementi <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-432">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="b97cb-433">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="b97cb-433">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="b97cb-434">Creare il provider di configurazione personalizzato ereditando da <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-434">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="b97cb-435">Il provider di configurazione inizializza il database quando è vuoto.</span><span class="sxs-lookup"><span data-stu-id="b97cb-435">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="b97cb-436">Poiché le [chiavi di configurazione non fanno distinzione tra maiuscole](#keys)e minuscole, il dizionario utilizzato per inizializzare il database viene creato con l'operatore di confronto senza distinzione tra maiuscole e minuscole ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="b97cb-436">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="b97cb-437">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="b97cb-437">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="b97cb-438">Un metodo di estensione `AddEFConfiguration` consente di aggiungere l'origine di configurazione a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-438">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="b97cb-439">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="b97cb-439">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="b97cb-440">L'esempio di codice seguente mostra come usare il `EFConfigurationProvider` personalizzato in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b97cb-440">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="b97cb-441">Accedi alla configurazione all'avvio</span><span class="sxs-lookup"><span data-stu-id="b97cb-441">Access configuration in Startup</span></span>

<span data-ttu-id="b97cb-442">Il codice seguente consente di visualizzare i dati di configurazione nei `Startup` metodi:</span><span class="sxs-lookup"><span data-stu-id="b97cb-442">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="b97cb-443">Per un esempio di accesso alla configurazione usando metodi di servizio di avvio, vedere [Avvio dell'applicazione: Metodi pratici](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="b97cb-443">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="b97cb-444">Accedi alla configurazione nelle Razor pagine</span><span class="sxs-lookup"><span data-stu-id="b97cb-444">Access configuration in Razor Pages</span></span>

<span data-ttu-id="b97cb-445">Il codice seguente consente di visualizzare i dati di configurazione in una Razor pagina:</span><span class="sxs-lookup"><span data-stu-id="b97cb-445">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="b97cb-446">Nel codice seguente, `MyOptions` viene aggiunto al contenitore del servizio con <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associato alla configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-446">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="b97cb-447">Il markup seguente utilizza la [`@inject`](xref:mvc/views/razor#inject) Razor direttiva per risolvere e visualizzare i valori delle opzioni:</span><span class="sxs-lookup"><span data-stu-id="b97cb-447">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="b97cb-448">Accedere alla configurazione in un file di visualizzazione MVC</span><span class="sxs-lookup"><span data-stu-id="b97cb-448">Access configuration in a MVC view file</span></span>

<span data-ttu-id="b97cb-449">Il codice seguente consente di visualizzare i dati di configurazione in una visualizzazione MVC:</span><span class="sxs-lookup"><span data-stu-id="b97cb-449">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="b97cb-450">Configurare le opzioni con un delegato</span><span class="sxs-lookup"><span data-stu-id="b97cb-450">Configure options with a delegate</span></span>

<span data-ttu-id="b97cb-451">Le opzioni configurate in un delegato eseguono l'override dei valori impostati nei provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-451">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="b97cb-452">La configurazione delle opzioni con un delegato è illustrata nell'esempio 2 nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="b97cb-452">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="b97cb-453">Nel codice seguente <xref:Microsoft.Extensions.Options.IConfigureOptions%601> viene aggiunto un servizio al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="b97cb-453">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="b97cb-454">Usa un delegato per configurare i valori per `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="b97cb-454">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="b97cb-455">Il codice seguente consente di visualizzare i valori delle opzioni:</span><span class="sxs-lookup"><span data-stu-id="b97cb-455">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="b97cb-456">Nell'esempio precedente, i valori di `Option1` e `Option2` vengono specificati in *appsettings.json* e quindi sottoposti a override dal delegato configurato.</span><span class="sxs-lookup"><span data-stu-id="b97cb-456">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="b97cb-457">Host e configurazione delle app</span><span class="sxs-lookup"><span data-stu-id="b97cb-457">Host versus app configuration</span></span>

<span data-ttu-id="b97cb-458">Prima che l'app venga configurata e avviata, viene configurato e avviato un *host*.</span><span class="sxs-lookup"><span data-stu-id="b97cb-458">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="b97cb-459">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-459">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="b97cb-460">Sia l'app che l'host vengono configurati tramite i provider di configurazione descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="b97cb-460">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="b97cb-461">Anche le coppie chiave-valore di configurazione dell'host sono incluse nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-461">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="b97cb-462">Per altre informazioni su come vengono usati i provider di configurazione quando viene creato l'host e sugli effetti delle origini di configurazione sull'host di configurazione, vedere <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-462">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="b97cb-463">Configurazione host predefinita</span><span class="sxs-lookup"><span data-stu-id="b97cb-463">Default host configuration</span></span>

<span data-ttu-id="b97cb-464">Per informazioni dettagliate sulla configurazione predefinita quando viene usato l'[host Web](xref:fundamentals/host/web-host), vedere la [versione di questo argomento per ASP.NET Core 2.2](?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="b97cb-464">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="b97cb-465">La configurazione dell'host viene specificata da:</span><span class="sxs-lookup"><span data-stu-id="b97cb-465">Host configuration is provided from:</span></span>
  * <span data-ttu-id="b97cb-466">Variabili di ambiente con prefisso `DOTNET_` (ad esempio, `DOTNET_ENVIRONMENT` ) utilizzando il [provider di configurazione delle variabili di ambiente](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="b97cb-466">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="b97cb-467">Il prefisso (`DOTNET_`) viene rimosso al caricamento delle coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-467">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="b97cb-468">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b97cb-468">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="b97cb-469">La configurazione predefinita dell'host Web viene stabilita (`ConfigureWebHostDefaults`) nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="b97cb-469">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="b97cb-470">Kestrel viene usato come server Web e configurato con i provider di configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-470">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="b97cb-471">Aggiungere il middleware di filtro host.</span><span class="sxs-lookup"><span data-stu-id="b97cb-471">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="b97cb-472">Aggiungere il middleware delle intestazioni inoltrate se la variabile di ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è impostata su `true`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-472">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="b97cb-473">Abilitare l'integrazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="b97cb-473">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="b97cb-474">Altra configurazione</span><span class="sxs-lookup"><span data-stu-id="b97cb-474">Other configuration</span></span>

<span data-ttu-id="b97cb-475">Questo argomento riguarda solo la *configurazione dell'app*.</span><span class="sxs-lookup"><span data-stu-id="b97cb-475">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="b97cb-476">Altri aspetti dell'esecuzione e dell'hosting di app ASP.NET Core sono configurati usando i file di configurazione non trattati in questo argomento:</span><span class="sxs-lookup"><span data-stu-id="b97cb-476">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="b97cb-477">*launch.js* / *launchSettings.jssu* sono i file di configurazione degli strumenti per l'ambiente di sviluppo, descritti di seguito:</span><span class="sxs-lookup"><span data-stu-id="b97cb-477">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="b97cb-478">In <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="b97cb-478">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="b97cb-479">Nella documentazione in cui vengono usati i file per configurare ASP.NET Core app per scenari di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-479">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="b97cb-480">*web.config* è un file di configurazione del server, descritto negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-480">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="b97cb-481">Le variabili di ambiente impostate in *launchSettings.jssu* sostituiscono quelle impostate nell'ambiente di sistema.</span><span class="sxs-lookup"><span data-stu-id="b97cb-481">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="b97cb-482">Per ulteriori informazioni sulla migrazione della configurazione dell'app da versioni precedenti di ASP.NET, vedere <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="b97cb-482">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="b97cb-483">Aggiungere la configurazione da un assembly esterno</span><span class="sxs-lookup"><span data-stu-id="b97cb-483">Add configuration from an external assembly</span></span>

<span data-ttu-id="b97cb-484">Un'implementazione <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> consente l'aggiunta di miglioramenti a un'app all'avvio, da un assembly esterno alla classe `Startup` dell'app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-484">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="b97cb-485">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-485">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b97cb-486">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b97cb-486">Additional resources</span></span>

* [<span data-ttu-id="b97cb-487">Codice sorgente configurazione</span><span class="sxs-lookup"><span data-stu-id="b97cb-487">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b97cb-488">La configurazione delle app in ASP.NET Core si basa su coppie chiave-valore stabilite dai *provider di configurazione*.</span><span class="sxs-lookup"><span data-stu-id="b97cb-488">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="b97cb-489">I provider di configurazione leggono i dati di configurazione in coppie chiave-valore da un'ampia gamma di origini di configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-489">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="b97cb-490">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="b97cb-490">Azure Key Vault</span></span>
* <span data-ttu-id="b97cb-491">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="b97cb-491">Azure App Configuration</span></span>
* <span data-ttu-id="b97cb-492">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="b97cb-492">Command-line arguments</span></span>
* <span data-ttu-id="b97cb-493">Provider personalizzati (installati o creati)</span><span class="sxs-lookup"><span data-stu-id="b97cb-493">Custom providers (installed or created)</span></span>
* <span data-ttu-id="b97cb-494">File della directory</span><span class="sxs-lookup"><span data-stu-id="b97cb-494">Directory files</span></span>
* <span data-ttu-id="b97cb-495">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="b97cb-495">Environment variables</span></span>
* <span data-ttu-id="b97cb-496">Oggetti .NET in memoria</span><span class="sxs-lookup"><span data-stu-id="b97cb-496">In-memory .NET objects</span></span>
* <span data-ttu-id="b97cb-497">File di impostazioni</span><span class="sxs-lookup"><span data-stu-id="b97cb-497">Settings files</span></span>

<span data-ttu-id="b97cb-498">I pacchetti di configurazione per gli scenari di provider di configurazione comuni ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sono inclusi nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b97cb-498">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b97cb-499">Gli esempi di codice che seguono e quelli inclusi nell'app di esempio usano lo spazio dei nomi <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="b97cb-499">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="b97cb-500">Il *modello di opzioni* è un'estensione dei concetti di configurazione descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="b97cb-500">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="b97cb-501">Le opzioni usano le classi per rappresentare i gruppi di impostazioni correlate.</span><span class="sxs-lookup"><span data-stu-id="b97cb-501">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="b97cb-502">Per altre informazioni, vedere <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-502">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="b97cb-503">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b97cb-503">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="b97cb-504">Host e configurazione delle app</span><span class="sxs-lookup"><span data-stu-id="b97cb-504">Host versus app configuration</span></span>

<span data-ttu-id="b97cb-505">Prima che l'app venga configurata e avviata, viene configurato e avviato un *host*.</span><span class="sxs-lookup"><span data-stu-id="b97cb-505">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="b97cb-506">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-506">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="b97cb-507">Sia l'app che l'host vengono configurati tramite i provider di configurazione descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="b97cb-507">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="b97cb-508">Anche le coppie chiave-valore di configurazione dell'host sono incluse nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-508">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="b97cb-509">Per altre informazioni su come vengono usati i provider di configurazione quando viene creato l'host e sugli effetti delle origini di configurazione sull'host di configurazione, vedere <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-509">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="b97cb-510">Altra configurazione</span><span class="sxs-lookup"><span data-stu-id="b97cb-510">Other configuration</span></span>

<span data-ttu-id="b97cb-511">Questo argomento riguarda solo la *configurazione dell'app*.</span><span class="sxs-lookup"><span data-stu-id="b97cb-511">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="b97cb-512">Altri aspetti dell'esecuzione e dell'hosting di app ASP.NET Core sono configurati usando i file di configurazione non trattati in questo argomento:</span><span class="sxs-lookup"><span data-stu-id="b97cb-512">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="b97cb-513">*launch.js* / *launchSettings.jssu* sono i file di configurazione degli strumenti per l'ambiente di sviluppo, descritti di seguito:</span><span class="sxs-lookup"><span data-stu-id="b97cb-513">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="b97cb-514">In <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="b97cb-514">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="b97cb-515">Nella documentazione in cui vengono usati i file per configurare ASP.NET Core app per scenari di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-515">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="b97cb-516">*web.config* è un file di configurazione del server, descritto negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-516">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="b97cb-517">Per ulteriori informazioni sulla migrazione della configurazione dell'app da versioni precedenti di ASP.NET, vedere <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="b97cb-517">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="b97cb-518">Configurazione predefinita</span><span class="sxs-lookup"><span data-stu-id="b97cb-518">Default configuration</span></span>

<span data-ttu-id="b97cb-519">Le app basate sui modelli [dotnet new](/dotnet/core/tools/dotnet-new) di ASP.NET Core chiamano <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> durante la compilazione di un host.</span><span class="sxs-lookup"><span data-stu-id="b97cb-519">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="b97cb-520">`CreateDefaultBuilder` fornisce la configurazione predefinita per l'app nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="b97cb-520">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="b97cb-521">La configurazione seguente si applica alle app che usano l'[host Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="b97cb-521">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="b97cb-522">Per informazioni dettagliate sulla configurazione predefinita quando viene usato l'[host generico](xref:fundamentals/host/generic-host), vedere la [versione più recente di questo argomento](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="b97cb-522">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="b97cb-523">La configurazione dell'host viene specificata da:</span><span class="sxs-lookup"><span data-stu-id="b97cb-523">Host configuration is provided from:</span></span>
  * <span data-ttu-id="b97cb-524">Variabili di ambiente con prefisso `ASPNETCORE_` (ad esempio `ASPNETCORE_ENVIRONMENT`) che usano il [provider di configurazione delle variabili di ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b97cb-524">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="b97cb-525">Il prefisso (`ASPNETCORE_`) viene rimosso al caricamento delle coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-525">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="b97cb-526">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b97cb-526">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="b97cb-527">La configurazione dell'app viene fornita da:</span><span class="sxs-lookup"><span data-stu-id="b97cb-527">App configuration is provided from:</span></span>
  * <span data-ttu-id="b97cb-528">*appsettings.json* utilizzando il [provider di configurazione file](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b97cb-528">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="b97cb-529">*appsettings.{Ambiente}.json* mediante il [provider di configurazione dei file](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b97cb-529">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="b97cb-530">[Segreti dell'utente](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development` usando l'assembly di ingresso.</span><span class="sxs-lookup"><span data-stu-id="b97cb-530">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="b97cb-531">Variabili di ambiente che usano il [provider di configurazione delle variabili di ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b97cb-531">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="b97cb-532">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b97cb-532">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="b97cb-533">Sicurezza</span><span class="sxs-lookup"><span data-stu-id="b97cb-533">Security</span></span>

<span data-ttu-id="b97cb-534">Per proteggere i dati di configurazione sensibili, adottare le pratiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-534">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="b97cb-535">Non archiviare mai la password o altri dati sensibili nel codice del provider di configurazione o in file di configurazione di testo normale.</span><span class="sxs-lookup"><span data-stu-id="b97cb-535">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="b97cb-536">Non usare i segreti di produzione in ambienti di sviluppo o di test.</span><span class="sxs-lookup"><span data-stu-id="b97cb-536">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="b97cb-537">Specificare i segreti all'esterno del progetto in modo che non possano essere inavvertitamente inviati a un repository del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-537">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="b97cb-538">Per altre informazioni, vedere i seguenti argomenti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-538">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="b97cb-539"><xref:security/app-secrets>: Include consigli sull'uso delle variabili di ambiente per archiviare dati riservati.</span><span class="sxs-lookup"><span data-stu-id="b97cb-539"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="b97cb-540">Lo strumento Secret Manager usa il provider di configurazione file per archiviare i segreti utente in un file JSON nel sistema locale.</span><span class="sxs-lookup"><span data-stu-id="b97cb-540">The Secret Manager tool uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="b97cb-541">Il provider di configurazione dei file è descritto più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="b97cb-541">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="b97cb-542">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) archivia in modo sicuro i segreti delle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b97cb-542">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="b97cb-543">Per altre informazioni, vedere <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-543">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="b97cb-544">Dati di configurazione gerarchici</span><span class="sxs-lookup"><span data-stu-id="b97cb-544">Hierarchical configuration data</span></span>

<span data-ttu-id="b97cb-545">L'API di configurazione è in grado di mantenere i dati di configurazione gerarchici rendendoli flat grazie all'uso di un delimitatore nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-545">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="b97cb-546">Nel file JSON seguente, esistono quattro chiavi in una gerarchia strutturata di due sezioni:</span><span class="sxs-lookup"><span data-stu-id="b97cb-546">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="b97cb-547">Quando il file viene letto nella configurazione, vengono create chiavi univoche per mantenere la struttura di dati gerarchici originale dell'origine di configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-547">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="b97cb-548">Le sezioni e le chiavi vengono rese flat usando due punti (`:`) per mantenere la struttura originale:</span><span class="sxs-lookup"><span data-stu-id="b97cb-548">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="b97cb-549">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b97cb-549">section0:key0</span></span>
* <span data-ttu-id="b97cb-550">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b97cb-550">section0:key1</span></span>
* <span data-ttu-id="b97cb-551">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b97cb-551">section1:key0</span></span>
* <span data-ttu-id="b97cb-552">section1:key1</span><span class="sxs-lookup"><span data-stu-id="b97cb-552">section1:key1</span></span>

<span data-ttu-id="b97cb-553">I metodi <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sono disponibili per isolare le sezioni e gli elementi figlio di una sezione nei dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-553"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="b97cb-554">Questi metodi sono descritti più avanti in [GetSection, GetChildren ed Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="b97cb-554">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="b97cb-555">Convenzioni</span><span class="sxs-lookup"><span data-stu-id="b97cb-555">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="b97cb-556">Origini e provider</span><span class="sxs-lookup"><span data-stu-id="b97cb-556">Sources and providers</span></span>

<span data-ttu-id="b97cb-557">All'avvio dell'app, le origini di configurazione vengono lette nell'ordine con cui sono specificati i rispettivi provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-557">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="b97cb-558">I provider di configurazione che implementano il rilevamento delle modifiche sono in grado di ricaricare la configurazione quando viene modificata un'impostazione sottostante.</span><span class="sxs-lookup"><span data-stu-id="b97cb-558">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="b97cb-559">Ad esempio, il provider di configurazione dei file (descritto più avanti in questo argomento) e il [provider di configurazione di Azure Key Vault](xref:security/key-vault-configuration) implementano il rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="b97cb-559">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="b97cb-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> è disponibile nel contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) dell'app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b97cb-561"><xref:Microsoft.Extensions.Configuration.IConfiguration> può essere inserito in una Razor pagina <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> o MVC <xref:Microsoft.AspNetCore.Mvc.Controller> per ottenere la configurazione per la classe.</span><span class="sxs-lookup"><span data-stu-id="b97cb-561"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="b97cb-562">Negli esempi seguenti il `_config` campo viene usato per accedere ai valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-562">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="b97cb-563">I provider di configurazione non possono usare l'inserimento delle dipendenze, perché non è disponibile quando vengono configurati dall'host.</span><span class="sxs-lookup"><span data-stu-id="b97cb-563">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="b97cb-564">Chiavi</span><span class="sxs-lookup"><span data-stu-id="b97cb-564">Keys</span></span>

<span data-ttu-id="b97cb-565">Le chiavi di configurazione adottano le convenzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-565">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="b97cb-566">Per le chiavi non viene fatta distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="b97cb-566">Keys are case-insensitive.</span></span> <span data-ttu-id="b97cb-567">Ad esempio, `ConnectionString` e `connectionstring` vengono considerate chiavi equivalenti.</span><span class="sxs-lookup"><span data-stu-id="b97cb-567">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="b97cb-568">Se un valore per la stessa chiave viene impostato dallo stesso provider di configurazione o da provider diversi, viene usato l'ultimo valore impostato per la chiave.</span><span class="sxs-lookup"><span data-stu-id="b97cb-568">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="b97cb-569">Per altre informazioni sulle chiavi JSON duplicate, vedere [questo problema di GitHub](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="b97cb-569">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="b97cb-570">Chiavi gerarchiche</span><span class="sxs-lookup"><span data-stu-id="b97cb-570">Hierarchical keys</span></span>
  * <span data-ttu-id="b97cb-571">Nell'ambito dell'API di configurazione, il separatore due punti (`:`) funziona in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="b97cb-571">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="b97cb-572">Nelle variabili di ambiente, un separatore due punti potrebbe non funzionare in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="b97cb-572">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="b97cb-573">Il doppio carattere di sottolineatura (`__`) è supportato da tutte le piattaforme e viene convertito automaticamente nei due punti.</span><span class="sxs-lookup"><span data-stu-id="b97cb-573">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="b97cb-574">In Azure Key Vault, le chiavi gerarchiche usano `--` (due trattini) come separatore.</span><span class="sxs-lookup"><span data-stu-id="b97cb-574">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="b97cb-575">Scrivere il codice per sostituire i trattini con i due punti quando i segreti vengono caricati nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-575">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="b97cb-576">Il <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supporta l'associazione di matrici agli oggetti usando gli indici delle matrici nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-576">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b97cb-577">L'associazione di matrici è descritta nella sezione [Associare una matrice a una classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="b97cb-577">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="b97cb-578">Valori</span><span class="sxs-lookup"><span data-stu-id="b97cb-578">Values</span></span>

<span data-ttu-id="b97cb-579">I valori di configurazione adottano le convenzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-579">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="b97cb-580">I valori sono stringhe.</span><span class="sxs-lookup"><span data-stu-id="b97cb-580">Values are strings.</span></span>
* <span data-ttu-id="b97cb-581">I valori null non possono essere archiviati nella configurazione o associati a oggetti.</span><span class="sxs-lookup"><span data-stu-id="b97cb-581">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="b97cb-582">Provider</span><span class="sxs-lookup"><span data-stu-id="b97cb-582">Providers</span></span>

<span data-ttu-id="b97cb-583">La tabella seguente mostra i provider di configurazione disponibili per le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b97cb-583">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="b97cb-584">Provider</span><span class="sxs-lookup"><span data-stu-id="b97cb-584">Provider</span></span> | <span data-ttu-id="b97cb-585">Fornisce la configurazione da&hellip;</span><span class="sxs-lookup"><span data-stu-id="b97cb-585">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="b97cb-586">[Provider di configurazione di Azure Key Vault](xref:security/key-vault-configuration) (argomenti *Sicurezza*)</span><span class="sxs-lookup"><span data-stu-id="b97cb-586">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="b97cb-587">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="b97cb-587">Azure Key Vault</span></span> |
| <span data-ttu-id="b97cb-588">[Provider di Configurazione app](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Documentazione di Azure)</span><span class="sxs-lookup"><span data-stu-id="b97cb-588">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="b97cb-589">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="b97cb-589">Azure App Configuration</span></span> |
| [<span data-ttu-id="b97cb-590">Provider di configurazione della riga di comando</span><span class="sxs-lookup"><span data-stu-id="b97cb-590">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="b97cb-591">Parametri della riga di comando</span><span class="sxs-lookup"><span data-stu-id="b97cb-591">Command-line parameters</span></span> |
| [<span data-ttu-id="b97cb-592">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="b97cb-592">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="b97cb-593">Origine personalizzata</span><span class="sxs-lookup"><span data-stu-id="b97cb-593">Custom source</span></span> |
| [<span data-ttu-id="b97cb-594">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="b97cb-594">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="b97cb-595">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="b97cb-595">Environment variables</span></span> |
| [<span data-ttu-id="b97cb-596">Provider di configurazione file</span><span class="sxs-lookup"><span data-stu-id="b97cb-596">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="b97cb-597">File (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="b97cb-597">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="b97cb-598">Provider di configurazione KeyPerFile</span><span class="sxs-lookup"><span data-stu-id="b97cb-598">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="b97cb-599">File della directory</span><span class="sxs-lookup"><span data-stu-id="b97cb-599">Directory files</span></span> |
| [<span data-ttu-id="b97cb-600">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="b97cb-600">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="b97cb-601">Raccolte in memoria</span><span class="sxs-lookup"><span data-stu-id="b97cb-601">In-memory collections</span></span> |
| <span data-ttu-id="b97cb-602">[Segreti utente](xref:security/app-secrets) (argomenti *sulla sicurezza* )</span><span class="sxs-lookup"><span data-stu-id="b97cb-602">[User secrets](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="b97cb-603">File nella directory dei profili utente</span><span class="sxs-lookup"><span data-stu-id="b97cb-603">File in the user profile directory</span></span> |

<span data-ttu-id="b97cb-604">Le origini di configurazione vengono lette nell'ordine in cui vengono specificati i rispetti provider di configurazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="b97cb-604">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="b97cb-605">I provider di configurazione descritti in questo argomento sono descritti in ordine alfabetico, non nell'ordine in cui il codice li dispone.</span><span class="sxs-lookup"><span data-stu-id="b97cb-605">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="b97cb-606">Ordinare i provider di configurazione nel codice in base alle priorità per le origini di configurazione sottostanti richieste dall'app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-606">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="b97cb-607">Una sequenza tipica di provider di configurazione è:</span><span class="sxs-lookup"><span data-stu-id="b97cb-607">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="b97cb-608">File ( *appsettings.json* , *appSettings. { Environment}. JSON*, dove `{Environment}` è l'ambiente host corrente dell'app)</span><span class="sxs-lookup"><span data-stu-id="b97cb-608">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="b97cb-609">Insieme di credenziali chiave Azure</span><span class="sxs-lookup"><span data-stu-id="b97cb-609">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="b97cb-610">[Segreti utente](xref:security/app-secrets) (solo per l'ambiente di sviluppo)</span><span class="sxs-lookup"><span data-stu-id="b97cb-610">[User secrets](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="b97cb-611">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="b97cb-611">Environment variables</span></span>
1. <span data-ttu-id="b97cb-612">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="b97cb-612">Command-line arguments</span></span>

<span data-ttu-id="b97cb-613">È pratica comune posizionare il provider di configurazione della riga di comando per ultimo in una serie di provider per consentire agli argomenti della riga di comando di sostituire la configurazione impostata da altri provider.</span><span class="sxs-lookup"><span data-stu-id="b97cb-613">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="b97cb-614">La sequenza di provider precedente viene utilizzata quando viene inizializzato un nuovo generatore host con `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b97cb-614">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b97cb-615">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="b97cb-615">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="b97cb-616">Configurare il generatore di host con UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="b97cb-616">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="b97cb-617">Per configurare il generatore di host, chiamare <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> sul generatore di host con la configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-617">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="b97cb-618">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="b97cb-618">ConfigureAppConfiguration</span></span>

<span data-ttu-id="b97cb-619">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare i provider di configurazione dell'app, oltre a quelli aggiunti automaticamente da `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="b97cb-619">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="b97cb-620">Sostituire la configurazione precedente con argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="b97cb-620">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="b97cb-621">Per fornire la configurazione dell'app che può essere sostituita con argomenti della riga di comando, chiamare `AddCommandLine` per ultimo:</span><span class="sxs-lookup"><span data-stu-id="b97cb-621">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="b97cb-622">Rimuovere i provider aggiunti da CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="b97cb-622">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="b97cb-623">Per rimuovere i provider aggiunti da `CreateDefaultBuilder` , chiamare prima [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) in [IConfigurationBuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="b97cb-623">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="b97cb-624">Utilizzare la configurazione durante l'avvio dell'app</span><span class="sxs-lookup"><span data-stu-id="b97cb-624">Consume configuration during app startup</span></span>

<span data-ttu-id="b97cb-625">La configurazione fornita all'app in `ConfigureAppConfiguration` è disponibile durante l'avvio dell'app, incluso `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-625">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b97cb-626">Per altre informazioni, vedere la sezione [Accedere alla configurazione durante l'avvio](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="b97cb-626">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="b97cb-627">Provider di configurazione della riga di comando</span><span class="sxs-lookup"><span data-stu-id="b97cb-627">Command-line Configuration Provider</span></span>

<span data-ttu-id="b97cb-628"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carica la configurazione da coppie chiave-valore di argomenti della riga di comando in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-628">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="b97cb-629">Per attivare la configurazione della riga di comando, metodo di estensione <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> viene chiamato su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-629">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b97cb-630">`AddCommandLine` viene chiamato automaticamente quando viene chiamato il metodo `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-630">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="b97cb-631">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="b97cb-631">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="b97cb-632">`CreateDefaultBuilder` carica anche:</span><span class="sxs-lookup"><span data-stu-id="b97cb-632">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b97cb-633">Configurazione facoltativa da *appsettings.json* e *appSettings. { Environment} file JSON* .</span><span class="sxs-lookup"><span data-stu-id="b97cb-633">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="b97cb-634">[Segreti utente](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-634">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="b97cb-635">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-635">Environment variables.</span></span>

<span data-ttu-id="b97cb-636">`CreateDefaultBuilder` aggiunge il provider di configurazione della riga di comando per ultimo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-636">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="b97cb-637">Gli argomenti della riga di comando passati in fase di esecuzione sostituiscono la configurazione impostata dagli altri provider.</span><span class="sxs-lookup"><span data-stu-id="b97cb-637">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="b97cb-638">`CreateDefaultBuilder` opera durante la creazione dell'host.</span><span class="sxs-lookup"><span data-stu-id="b97cb-638">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="b97cb-639">Pertanto, la configurazione della riga di comando attivata da `CreateDefaultBuilder` può influire sul modo in cui l'host viene configurato.</span><span class="sxs-lookup"><span data-stu-id="b97cb-639">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="b97cb-640">Per le app basate sui modelli di ASP.NET Core, `AddCommandLine` è già stato chiamato da `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-640">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b97cb-641">Per aggiungere altri provider di configurazione e mantenere la possibilità di sostituire la configurazione di tali provider con argomenti della riga di comando, chiamare i provider aggiuntivi dell'app in `ConfigureAppConfiguration` e quindi chiamare `AddCommandLine` per ultimo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-641">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="b97cb-642">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="b97cb-642">**Example**</span></span>

<span data-ttu-id="b97cb-643">L'app di esempio consente di sfruttare il metodo di servizio statico `CreateDefaultBuilder` per creare l'host, che include una chiamata a <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-643">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="b97cb-644">Aprire un prompt dei comandi nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="b97cb-644">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="b97cb-645">Fornire un argomento della riga di comando per il comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-645">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="b97cb-646">Dopo che l'app è in esecuzione, aprire un browser per l'app all'indirizzo `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-646">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b97cb-647">Notare che l'output contiene la coppia chiave-valore per l'argomento della riga di comando di configurazione fornito per `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-647">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="b97cb-648">Argomenti</span><span class="sxs-lookup"><span data-stu-id="b97cb-648">Arguments</span></span>

<span data-ttu-id="b97cb-649">Il valore deve seguire un segno di uguale (`=`) o la chiave deve avere un prefisso (`--` o `/`) quando il valore segue uno spazio.</span><span class="sxs-lookup"><span data-stu-id="b97cb-649">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="b97cb-650">Il valore non è necessario se viene usato un segno di uguale, ad esempio `CommandLineKey=`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-650">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="b97cb-651">Prefisso chiave</span><span class="sxs-lookup"><span data-stu-id="b97cb-651">Key prefix</span></span>               | <span data-ttu-id="b97cb-652">Esempio</span><span class="sxs-lookup"><span data-stu-id="b97cb-652">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="b97cb-653">Nessun prefisso</span><span class="sxs-lookup"><span data-stu-id="b97cb-653">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="b97cb-654">Due trattini (`--`)</span><span class="sxs-lookup"><span data-stu-id="b97cb-654">Two dashes (`--`)</span></span>        | <span data-ttu-id="b97cb-655">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="b97cb-655">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="b97cb-656">Barra (`/`)</span><span class="sxs-lookup"><span data-stu-id="b97cb-656">Forward slash (`/`)</span></span>      | <span data-ttu-id="b97cb-657">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="b97cb-657">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="b97cb-658">Nello stesso comando, non mischiare coppie chiave-valore di argomenti della riga di comando che usano un segno di uguale con coppie chiave-valore che usano uno spazio.</span><span class="sxs-lookup"><span data-stu-id="b97cb-658">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="b97cb-659">Comandi di esempio:</span><span class="sxs-lookup"><span data-stu-id="b97cb-659">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="b97cb-660">Mapping di sostituzione</span><span class="sxs-lookup"><span data-stu-id="b97cb-660">Switch mappings</span></span>

<span data-ttu-id="b97cb-661">I mapping di sostituzione consentono di usare la logica di sostituzione del nome della chiave.</span><span class="sxs-lookup"><span data-stu-id="b97cb-661">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="b97cb-662">Quando si compila manualmente la configurazione con un oggetto <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , fornire un dizionario di sostituzioni switch al <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-662">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="b97cb-663">Quando viene utilizzato il dizionario dei mapping di sostituzione, nel dizionario viene controllata la presenza di una chiave corrispondente alla chiave fornita da un argomento della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="b97cb-663">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="b97cb-664">Se la chiave della riga di comando viene trovata nel dizionario, il valore del dizionario (la sostituzione della chiave) viene passato nuovamente per impostare la coppia chiave-valore nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-664">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="b97cb-665">Un mapping di sostituzione è necessario per le chiavi della riga di comando con un trattino singolo (`-`) come prefisso.</span><span class="sxs-lookup"><span data-stu-id="b97cb-665">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="b97cb-666">Regole principali del dizionario dei mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-666">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="b97cb-667">Le sostituzioni devono iniziare con un trattino (`-`) o un trattino doppio (`--`).</span><span class="sxs-lookup"><span data-stu-id="b97cb-667">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="b97cb-668">Il dizionario dei mapping di sostituzione non deve contenere chiavi duplicate.</span><span class="sxs-lookup"><span data-stu-id="b97cb-668">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="b97cb-669">Creare un dizionario dei mapping di sostituzione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-669">Create a switch mappings dictionary.</span></span> <span data-ttu-id="b97cb-670">Nell'esempio seguente vengono creati due mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-670">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="b97cb-671">Quando viene creato l'host, chiamare `AddCommandLine` con il dizionario dei mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-671">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="b97cb-672">Per le app che usano i mapping di sostituzione, la chiamata a `CreateDefaultBuilder` non deve passare argomenti.</span><span class="sxs-lookup"><span data-stu-id="b97cb-672">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="b97cb-673">La chiamata `AddCommandLine` del metodo `CreateDefaultBuilder` non include sostituzioni mappate e non è possibile passare il dizionario dei mapping di sostituzione a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-673">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b97cb-674">La soluzione consiste nel non passare gli argomenti a `CreateDefaultBuilder` consentendo invece al metodo `AddCommandLine` del metodo `ConfigurationBuilder` di elaborare entrambi gli argomenti e il dizionario dei mapping di sostituzione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-674">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="b97cb-675">Il dizionario dei mapping di sostituzione creato contiene i dati visualizzati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-675">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="b97cb-676">Chiave</span><span class="sxs-lookup"><span data-stu-id="b97cb-676">Key</span></span>       | <span data-ttu-id="b97cb-677">valore</span><span class="sxs-lookup"><span data-stu-id="b97cb-677">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="b97cb-678">Se le chiavi con mapping di sostituzione vengono usate all'avvio dell'app, la configurazione riceve il valore di configurazione per la chiave fornita dal dizionario:</span><span class="sxs-lookup"><span data-stu-id="b97cb-678">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="b97cb-679">Dopo aver eseguito il comando precedente, la configurazione contiene i valori mostrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-679">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="b97cb-680">Chiave</span><span class="sxs-lookup"><span data-stu-id="b97cb-680">Key</span></span>               | <span data-ttu-id="b97cb-681">valore</span><span class="sxs-lookup"><span data-stu-id="b97cb-681">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="b97cb-682">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="b97cb-682">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="b97cb-683"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carica la configurazione da coppie chiave-valore di variabili di ambiente in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-683">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="b97cb-684">Per attivare la configurazione delle variabili di ambiente, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-684">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="b97cb-685">[App Azure servizio](https://azure.microsoft.com/services/app-service/) consente di impostare le variabili di ambiente nel portale di Azure che possono eseguire l'override della configurazione dell'app usando il provider di configurazione delle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-685">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="b97cb-686">Per altre informazioni, vedere [App di Azure: Eseguire l'override della configurazione delle app usando il portale di Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="b97cb-686">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="b97cb-687">`AddEnvironmentVariables` consente di caricare variabili di ambiente con prefisso `ASPNETCORE_` per la [configurazione host](#host-versus-app-configuration) quando viene inizializzato un nuovo generatore di host con l'[host Web](xref:fundamentals/host/web-host) e viene chiamato `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-687">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="b97cb-688">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="b97cb-688">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="b97cb-689">`CreateDefaultBuilder` carica anche:</span><span class="sxs-lookup"><span data-stu-id="b97cb-689">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b97cb-690">Configurazione delle app dalle variabili di ambiente senza prefisso chiamando `AddEnvironmentVariables` senza prefisso.</span><span class="sxs-lookup"><span data-stu-id="b97cb-690">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="b97cb-691">Configurazione facoltativa da *appsettings.json* e *appSettings. { Environment} file JSON* .</span><span class="sxs-lookup"><span data-stu-id="b97cb-691">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="b97cb-692">[Segreti utente](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-692">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="b97cb-693">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="b97cb-693">Command-line arguments.</span></span>

<span data-ttu-id="b97cb-694">Il provider di configurazione delle variabili di ambiente viene chiamato dopo aver stabilito la configurazione dai segreti utente e dai file *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="b97cb-694">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="b97cb-695">La chiamata del provider in questa posizione consente alle variabili di ambiente lette in fase di esecuzione di sostituire la configurazione impostata dai segreti utente e dai file *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="b97cb-695">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="b97cb-696">Per fornire la configurazione dell'app da altre variabili di ambiente, chiamare i provider aggiuntivi dell'app in `ConfigureAppConfiguration` e chiamare `AddEnvironmentVariables` con il prefisso:</span><span class="sxs-lookup"><span data-stu-id="b97cb-696">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="b97cb-697">Chiamare `AddEnvironmentVariables` Last per consentire alle variabili di ambiente con il prefisso specificato di eseguire l'override dei valori di altri provider.</span><span class="sxs-lookup"><span data-stu-id="b97cb-697">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="b97cb-698">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="b97cb-698">**Example**</span></span>

<span data-ttu-id="b97cb-699">L'app di esempio consente di sfruttare il metodo di servizio statico `CreateDefaultBuilder` per creare l'host, che include una chiamata a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-699">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="b97cb-700">Eseguire l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="b97cb-700">Run the sample app.</span></span> <span data-ttu-id="b97cb-701">Aprire un browser per l'app all'indirizzo `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-701">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b97cb-702">Notare che l'output contiene la coppia chiave-valore per la variabile di ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-702">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="b97cb-703">Il valore riflette l'ambiente in cui l'app è in esecuzione, in genere `Development` durante l'esecuzione locale.</span><span class="sxs-lookup"><span data-stu-id="b97cb-703">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="b97cb-704">Per limitare l'elenco delle variabili di ambiente restituito dall'app, l'app filtra le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-704">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="b97cb-705">Vedere il file *Pages/Index.cshtml.cs* dell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="b97cb-705">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="b97cb-706">Per esporre tutte le variabili di ambiente disponibili per l'app, modificare `FilteredConfiguration` in *pages/index. cshtml. cs* come riportato di seguito:</span><span class="sxs-lookup"><span data-stu-id="b97cb-706">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="b97cb-707">Prefissi</span><span class="sxs-lookup"><span data-stu-id="b97cb-707">Prefixes</span></span>

<span data-ttu-id="b97cb-708">Le variabili di ambiente caricate nella configurazione dell'app vengono filtrate quando si specifica un prefisso per il `AddEnvironmentVariables` metodo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-708">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="b97cb-709">Ad esempio, per filtrare le variabili di ambiente in base al prefisso `CUSTOM_`, fornire il prefisso al provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-709">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="b97cb-710">Il prefisso viene rimosso quando vengono create le coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-710">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="b97cb-711">Quando viene creato il generatore di host, la configurazione dell'host viene fornita dalle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-711">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="b97cb-712">Per altre informazioni sul prefisso usato per queste variabili di ambiente, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="b97cb-712">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="b97cb-713">**Prefissi della stringa di connessione**</span><span class="sxs-lookup"><span data-stu-id="b97cb-713">**Connection string prefixes**</span></span>

<span data-ttu-id="b97cb-714">L'API di configurazione prevede regole di elaborazione speciali per quattro variabili di ambiente di stringa di connessione coinvolte nella configurazione di stringhe di connessione di Azure per l'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-714">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="b97cb-715">Le variabili di ambiente con i prefissi indicati nella tabella vengono caricate nell'app se non viene fornito alcun prefisso a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-715">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="b97cb-716">Prefisso della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="b97cb-716">Connection string prefix</span></span> | <span data-ttu-id="b97cb-717">Provider</span><span class="sxs-lookup"><span data-stu-id="b97cb-717">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="b97cb-718">Provider personalizzato</span><span class="sxs-lookup"><span data-stu-id="b97cb-718">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="b97cb-719">MySQL</span><span class="sxs-lookup"><span data-stu-id="b97cb-719">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="b97cb-720">Database SQL di Azure</span><span class="sxs-lookup"><span data-stu-id="b97cb-720">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="b97cb-721">SQL Server</span><span class="sxs-lookup"><span data-stu-id="b97cb-721">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="b97cb-722">Quando una variabile di ambiente viene individuata e caricata nella configurazione con uno qualsiasi dei quattro prefissi indicati nella tabella:</span><span class="sxs-lookup"><span data-stu-id="b97cb-722">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="b97cb-723">La chiave di configurazione viene creata rimuovendo il prefisso della variabile di ambiente e aggiungendo una sezione per la chiave di configurazione (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="b97cb-723">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="b97cb-724">Viene creata una nuova coppia chiave-valore della configurazione che rappresenta il provider di connessione al database (ad eccezione di `CUSTOMCONNSTR_`, che non ha un provider dichiarato).</span><span class="sxs-lookup"><span data-stu-id="b97cb-724">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="b97cb-725">Chiave della variabile di ambiente</span><span class="sxs-lookup"><span data-stu-id="b97cb-725">Environment variable key</span></span> | <span data-ttu-id="b97cb-726">Chiave di configurazione convertita</span><span class="sxs-lookup"><span data-stu-id="b97cb-726">Converted configuration key</span></span> | <span data-ttu-id="b97cb-727">Voce di configurazione del provider</span><span class="sxs-lookup"><span data-stu-id="b97cb-727">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b97cb-728">Voce di configurazione non creata.</span><span class="sxs-lookup"><span data-stu-id="b97cb-728">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b97cb-729">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="b97cb-729">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="b97cb-730">Valore: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="b97cb-730">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b97cb-731">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="b97cb-731">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="b97cb-732">Valore: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b97cb-732">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b97cb-733">Chiave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="b97cb-733">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="b97cb-734">Valore: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b97cb-734">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="b97cb-735">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="b97cb-735">**Example**</span></span>

<span data-ttu-id="b97cb-736">Nel server viene creata una variabile di ambiente della stringa di connessione personalizzata:</span><span class="sxs-lookup"><span data-stu-id="b97cb-736">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="b97cb-737">Nome: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="b97cb-737">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="b97cb-738">Valore: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="b97cb-738">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="b97cb-739">Se `IConfiguration` viene inserito e assegnato a un campo denominato `_config` , leggere il valore:</span><span class="sxs-lookup"><span data-stu-id="b97cb-739">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="b97cb-740">Provider di configurazione dei file</span><span class="sxs-lookup"><span data-stu-id="b97cb-740">File Configuration Provider</span></span>

<span data-ttu-id="b97cb-741"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> è la classe base per il caricamento della configurazione dal file system.</span><span class="sxs-lookup"><span data-stu-id="b97cb-741"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="b97cb-742">I provider di configurazione seguenti sono dedicati per specifici tipi di file:</span><span class="sxs-lookup"><span data-stu-id="b97cb-742">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="b97cb-743">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="b97cb-743">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="b97cb-744">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="b97cb-744">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="b97cb-745">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="b97cb-745">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="b97cb-746">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="b97cb-746">INI Configuration Provider</span></span>

<span data-ttu-id="b97cb-747"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carica la configurazione da coppie chiave-valore di file INI in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-747">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="b97cb-748">Per attivare la configurazione di file INI, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-748">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b97cb-749">I due punti possono essere usati come delimitatore di sezione nella configurazione di file INI.</span><span class="sxs-lookup"><span data-stu-id="b97cb-749">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="b97cb-750">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="b97cb-750">Overloads permit specifying:</span></span>

* <span data-ttu-id="b97cb-751">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-751">Whether the file is optional.</span></span>
* <span data-ttu-id="b97cb-752">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-752">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b97cb-753">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider> usato per accedere al file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-753">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="b97cb-754">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="b97cb-754">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="b97cb-755">Un esempio generico di un file di configurazione INI:</span><span class="sxs-lookup"><span data-stu-id="b97cb-755">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="b97cb-756">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="b97cb-756">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b97cb-757">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b97cb-757">section0:key0</span></span>
* <span data-ttu-id="b97cb-758">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b97cb-758">section0:key1</span></span>
* <span data-ttu-id="b97cb-759">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="b97cb-759">section1:subsection:key</span></span>
* <span data-ttu-id="b97cb-760">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="b97cb-760">section2:subsection0:key</span></span>
* <span data-ttu-id="b97cb-761">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="b97cb-761">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="b97cb-762">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="b97cb-762">JSON Configuration Provider</span></span>

<span data-ttu-id="b97cb-763">Il <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carica la configurazione da coppie chiave-valore di file JSON in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-763">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="b97cb-764">Per attivare la configurazione di file JSON, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-764">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b97cb-765">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="b97cb-765">Overloads permit specifying:</span></span>

* <span data-ttu-id="b97cb-766">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-766">Whether the file is optional.</span></span>
* <span data-ttu-id="b97cb-767">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-767">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b97cb-768">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider> usato per accedere al file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-768">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="b97cb-769">`AddJsonFile` viene chiamato automaticamente due volte quando viene inizializzato un nuovo generatore host con `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b97cb-769">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b97cb-770">Il metodo viene chiamato per caricare la configurazione da:</span><span class="sxs-lookup"><span data-stu-id="b97cb-770">The method is called to load configuration from:</span></span>

* <span data-ttu-id="b97cb-771">*appsettings.json*: Questo file viene letto per primo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-771">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="b97cb-772">La versione dell'ambiente del file può sostituire i valori forniti dal *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-772">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="b97cb-773">*appSettings. {Environment}. JSON*: la versione dell'ambiente del file è stata caricata in base a [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="b97cb-773">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="b97cb-774">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="b97cb-774">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="b97cb-775">`CreateDefaultBuilder` carica anche:</span><span class="sxs-lookup"><span data-stu-id="b97cb-775">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b97cb-776">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-776">Environment variables.</span></span>
* <span data-ttu-id="b97cb-777">[Segreti utente](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-777">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="b97cb-778">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="b97cb-778">Command-line arguments.</span></span>

<span data-ttu-id="b97cb-779">Il provider di configurazione JSON viene stabilito per primo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-779">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="b97cb-780">I segreti utente, le variabili di ambiente e gli argomenti della riga di comando sostituiscono quindi la configurazione impostata dai file *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="b97cb-780">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="b97cb-781">Chiamare `ConfigureAppConfiguration` quando si compila l'host per specificare la configurazione dell'app per i file diversi da *appsettings.json* e *appSettings. { Environment}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="b97cb-781">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="b97cb-782">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="b97cb-782">**Example**</span></span>

<span data-ttu-id="b97cb-783">L'app di esempio sfrutta il metodo di convenienza statica `CreateDefaultBuilder` per compilare l'host, che include due chiamate a `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="b97cb-783">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="b97cb-784">La prima chiamata a `AddJsonFile` carica la configurazione da *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="b97cb-784">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="b97cb-785">La seconda chiamata a `AddJsonFile` carica la configurazione da *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="b97cb-785">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="b97cb-786">Per *appsettings.Development.js* nell'app di esempio, viene caricato il file seguente:</span><span class="sxs-lookup"><span data-stu-id="b97cb-786">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="b97cb-787">Eseguire l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="b97cb-787">Run the sample app.</span></span> <span data-ttu-id="b97cb-788">Aprire un browser per l'app all'indirizzo `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-788">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b97cb-789">L'output contiene coppie chiave-valore per la configurazione basata sull'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-789">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="b97cb-790">Il livello di registrazione della chiave `Logging:LogLevel:Default` è `Debug` quando si esegue l'app nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-790">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="b97cb-791">Eseguire di nuovo l'app di esempio nell'ambiente di produzione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-791">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="b97cb-792">Aprire le *Proprietà/launchSettings.jssu* file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-792">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="b97cb-793">Nel `ConfigurationSample` profilo, modificare il valore della variabile di `ASPNETCORE_ENVIRONMENT` ambiente in `Production` .</span><span class="sxs-lookup"><span data-stu-id="b97cb-793">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="b97cb-794">Salvare il file ed eseguire l'app con `dotnet run` in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="b97cb-794">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="b97cb-795">Le impostazioni nel *appsettings.Development.jssu* non sostituiscono più le impostazioni in *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b97cb-795">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="b97cb-796">Il livello di registrazione per la chiave `Logging:LogLevel:Default` è `Warning` .</span><span class="sxs-lookup"><span data-stu-id="b97cb-796">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="b97cb-797">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="b97cb-797">XML Configuration Provider</span></span>

<span data-ttu-id="b97cb-798">Il <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carica la configurazione da coppie chiave-valore di file XML in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-798">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="b97cb-799">Per attivare la configurazione di file XML, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-799">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b97cb-800">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="b97cb-800">Overloads permit specifying:</span></span>

* <span data-ttu-id="b97cb-801">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-801">Whether the file is optional.</span></span>
* <span data-ttu-id="b97cb-802">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-802">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b97cb-803">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider> usato per accedere al file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-803">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="b97cb-804">Il nodo radice del file di configurazione viene ignorato quando vengono create le coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-804">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="b97cb-805">Non specificare una definizione DTD (Document Type Definition) o uno spazio dei nomi nel file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-805">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="b97cb-806">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="b97cb-806">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="b97cb-807">I file di configurazione XML possono usare nomi di elementi distinti per le sezioni ripetute:</span><span class="sxs-lookup"><span data-stu-id="b97cb-807">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="b97cb-808">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="b97cb-808">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b97cb-809">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b97cb-809">section0:key0</span></span>
* <span data-ttu-id="b97cb-810">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b97cb-810">section0:key1</span></span>
* <span data-ttu-id="b97cb-811">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b97cb-811">section1:key0</span></span>
* <span data-ttu-id="b97cb-812">section1:key1</span><span class="sxs-lookup"><span data-stu-id="b97cb-812">section1:key1</span></span>

<span data-ttu-id="b97cb-813">La ripetizione di elementi che usano lo stesso nome di elemento funziona se si usa l'attributo `name` per distinguere gli elementi:</span><span class="sxs-lookup"><span data-stu-id="b97cb-813">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="b97cb-814">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="b97cb-814">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b97cb-815">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="b97cb-815">section:section0:key:key0</span></span>
* <span data-ttu-id="b97cb-816">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="b97cb-816">section:section0:key:key1</span></span>
* <span data-ttu-id="b97cb-817">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="b97cb-817">section:section1:key:key0</span></span>
* <span data-ttu-id="b97cb-818">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="b97cb-818">section:section1:key:key1</span></span>

<span data-ttu-id="b97cb-819">È possibile usare attributi per fornire valori:</span><span class="sxs-lookup"><span data-stu-id="b97cb-819">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="b97cb-820">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="b97cb-820">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b97cb-821">key:attribute</span><span class="sxs-lookup"><span data-stu-id="b97cb-821">key:attribute</span></span>
* <span data-ttu-id="b97cb-822">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="b97cb-822">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="b97cb-823">Provider di configurazione KeyPerFile</span><span class="sxs-lookup"><span data-stu-id="b97cb-823">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="b97cb-824">Il <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa i file di una directory come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-824">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="b97cb-825">La chiave è il nome del file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-825">The key is the file name.</span></span> <span data-ttu-id="b97cb-826">Il valore contiene il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-826">The value contains the file's contents.</span></span> <span data-ttu-id="b97cb-827">Il provider di configurazione KeyPerFile viene usato in scenari di hosting Docker.</span><span class="sxs-lookup"><span data-stu-id="b97cb-827">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="b97cb-828">Per attivare la configurazione chiave-per-file, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-828">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="b97cb-829">Il `directoryPath` per i file deve essere un percorso assoluto.</span><span class="sxs-lookup"><span data-stu-id="b97cb-829">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="b97cb-830">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="b97cb-830">Overloads permit specifying:</span></span>

* <span data-ttu-id="b97cb-831">Un delegato `Action<KeyPerFileConfigurationSource>` che configura l'origine.</span><span class="sxs-lookup"><span data-stu-id="b97cb-831">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="b97cb-832">Se la directory è facoltativa e il percorso della directory.</span><span class="sxs-lookup"><span data-stu-id="b97cb-832">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="b97cb-833">Il doppio carattere di sottolineatura (`__`) viene usato come delimitatore per le chiavi di configurazione nei nomi dei file.</span><span class="sxs-lookup"><span data-stu-id="b97cb-833">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="b97cb-834">Ad esempio, il nome di file `Logging__LogLevel__System` produce la chiave di configurazione `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-834">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="b97cb-835">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="b97cb-835">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="b97cb-836">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="b97cb-836">Memory Configuration Provider</span></span>

<span data-ttu-id="b97cb-837">Il <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una raccolta in memoria come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-837">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="b97cb-838">Per attivare la configurazione della raccolta in memoria, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-838">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b97cb-839">Il provider di configurazione può essere inizializzato con un `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-839">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="b97cb-840">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-840">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="b97cb-841">Nell'esempio seguente viene creato un dizionario di configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-841">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="b97cb-842">Il dizionario viene usato con una chiamata a `AddInMemoryCollection` per fornire la configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-842">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="b97cb-843">GetValue</span><span class="sxs-lookup"><span data-stu-id="b97cb-843">GetValue</span></span>

<span data-ttu-id="b97cb-844">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) estrae un singolo valore dalla configurazione con una chiave specificata e lo converte nel tipo non di raccolta specificato.</span><span class="sxs-lookup"><span data-stu-id="b97cb-844">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="b97cb-845">Un overload accetta un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="b97cb-845">An overload accepts a default value.</span></span>

<span data-ttu-id="b97cb-846">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="b97cb-846">The following example:</span></span>

* <span data-ttu-id="b97cb-847">Estrae il valore di stringa dalla configurazione con la chiave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-847">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="b97cb-848">Se `NumberKey` non viene trovato nelle chiavi di configurazione, viene usato il valore predefinito di `99`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-848">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="b97cb-849">Assegna al valore il tipo `int`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-849">Types the value as an `int`.</span></span>
* <span data-ttu-id="b97cb-850">Memorizza il valore nella proprietà `NumberConfig` per l'uso da parte della pagina.</span><span class="sxs-lookup"><span data-stu-id="b97cb-850">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="b97cb-851">GetSection, GetChildren ed Exists</span><span class="sxs-lookup"><span data-stu-id="b97cb-851">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="b97cb-852">Per gli esempi che seguono, prendere in considerazione il file JSON seguente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-852">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="b97cb-853">Vengono trovate quattro chiavi in due sezioni, una delle quali include una coppia di sottosezioni:</span><span class="sxs-lookup"><span data-stu-id="b97cb-853">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="b97cb-854">Quando il file viene letto nella configurazione, vengono create le chiavi gerarchiche univoche seguenti per contenere i valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-854">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="b97cb-855">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b97cb-855">section0:key0</span></span>
* <span data-ttu-id="b97cb-856">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b97cb-856">section0:key1</span></span>
* <span data-ttu-id="b97cb-857">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b97cb-857">section1:key0</span></span>
* <span data-ttu-id="b97cb-858">section1:key1</span><span class="sxs-lookup"><span data-stu-id="b97cb-858">section1:key1</span></span>
* <span data-ttu-id="b97cb-859">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="b97cb-859">section2:subsection0:key0</span></span>
* <span data-ttu-id="b97cb-860">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="b97cb-860">section2:subsection0:key1</span></span>
* <span data-ttu-id="b97cb-861">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="b97cb-861">section2:subsection1:key0</span></span>
* <span data-ttu-id="b97cb-862">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="b97cb-862">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="b97cb-863">GetSection</span><span class="sxs-lookup"><span data-stu-id="b97cb-863">GetSection</span></span>

<span data-ttu-id="b97cb-864">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) estrae una sottosezione della configurazione con la chiave di sottosezione specificata.</span><span class="sxs-lookup"><span data-stu-id="b97cb-864">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="b97cb-865">Per restituire una <xref:Microsoft.Extensions.Configuration.IConfigurationSection> che contiene solo le coppie chiave-valore in `section1`, chiamare `GetSection` e fornire il nome della sezione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-865">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="b97cb-866">`configSection` non ha un valore, ma solo una chiave e un percorso.</span><span class="sxs-lookup"><span data-stu-id="b97cb-866">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="b97cb-867">In modo analogo, per ottenere i valori per le chiavi in `section2:subsection0`, chiamare `GetSection` e fornire il percorso della sezione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-867">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="b97cb-868">`GetSection` non restituisce mai `null`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-868">`GetSection` never returns `null`.</span></span> <span data-ttu-id="b97cb-869">Se non viene trovata una sezione corrispondente, viene restituita una `IConfigurationSection` vuota.</span><span class="sxs-lookup"><span data-stu-id="b97cb-869">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="b97cb-870">Quando `GetSection` restituisce una sezione corrispondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> non viene compilata.</span><span class="sxs-lookup"><span data-stu-id="b97cb-870">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="b97cb-871">Quando la sezione esiste, vengono restituiti <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-871">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="b97cb-872">GetChildren</span><span class="sxs-lookup"><span data-stu-id="b97cb-872">GetChildren</span></span>

<span data-ttu-id="b97cb-873">Una chiamata a [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) per `section2` ottiene una `IEnumerable<IConfigurationSection>` che include:</span><span class="sxs-lookup"><span data-stu-id="b97cb-873">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="b97cb-874">Exists</span><span class="sxs-lookup"><span data-stu-id="b97cb-874">Exists</span></span>

<span data-ttu-id="b97cb-875">Usare [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) per determinare se esiste una sezione di configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-875">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="b97cb-876">Considerati i dati di esempio, `sectionExists` è `false` perché non esiste una sezione `section2:subsection2` nei dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-876">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="b97cb-877">Associazione a un oggetto grafico</span><span class="sxs-lookup"><span data-stu-id="b97cb-877">Bind to an object graph</span></span>

<span data-ttu-id="b97cb-878"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> è in grado di associare un intero oggetto grafico POCO.</span><span class="sxs-lookup"><span data-stu-id="b97cb-878"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="b97cb-879">Come per l'associazione di un oggetto semplice, vengono associate solo le proprietà di lettura/scrittura pubbliche.</span><span class="sxs-lookup"><span data-stu-id="b97cb-879">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="b97cb-880">L'esempio contiene un modello `TvShow` il cui oggetto grafico include `Metadata` e le classi `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="b97cb-880">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="b97cb-881">L'app di esempio ha un file *tvshow.xml* che contiene i dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-881">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="b97cb-882">La configurazione viene associata all'intero oggetto grafico `TvShow` con il metodo `Bind`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-882">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="b97cb-883">L'istanza associata viene assegnata a una proprietà per il rendering:</span><span class="sxs-lookup"><span data-stu-id="b97cb-883">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="b97cb-884">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e restituisce il tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="b97cb-884">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="b97cb-885">`Get<T>` può essere più comodo che usare `Bind`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-885">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="b97cb-886">Il codice seguente illustra come usare `Get<T>` con l'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="b97cb-886">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="b97cb-887">Associare una matrice a una classe</span><span class="sxs-lookup"><span data-stu-id="b97cb-887">Bind an array to a class</span></span>

<span data-ttu-id="b97cb-888">*L'app di esempio dimostra i concetti spiegati in questa sezione.*</span><span class="sxs-lookup"><span data-stu-id="b97cb-888">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="b97cb-889">Il <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supporta l'associazione di matrici agli oggetti usando gli indici delle matrici nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-889">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b97cb-890">Qualsiasi formato di matrice che espone un segmento di chiave numerico ( `:0:` , `:1:` , &hellip; `:{n}:` ) è in grado di associare array a una matrice di classi poco.</span><span class="sxs-lookup"><span data-stu-id="b97cb-890">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="b97cb-891">L'associazione viene fornita per convenzione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-891">Binding is provided by convention.</span></span> <span data-ttu-id="b97cb-892">I provider di configurazione personalizzati non devono implementare l'associazione di matrici.</span><span class="sxs-lookup"><span data-stu-id="b97cb-892">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="b97cb-893">**Elaborazione di matrici in memoria**</span><span class="sxs-lookup"><span data-stu-id="b97cb-893">**In-memory array processing**</span></span>

<span data-ttu-id="b97cb-894">Prendere in considerazione le chiavi di configurazione e i valori indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="b97cb-894">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="b97cb-895">Chiave</span><span class="sxs-lookup"><span data-stu-id="b97cb-895">Key</span></span>             | <span data-ttu-id="b97cb-896">valore</span><span class="sxs-lookup"><span data-stu-id="b97cb-896">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="b97cb-897">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="b97cb-897">array:entries:0</span></span> | <span data-ttu-id="b97cb-898">value0</span><span class="sxs-lookup"><span data-stu-id="b97cb-898">value0</span></span> |
| <span data-ttu-id="b97cb-899">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="b97cb-899">array:entries:1</span></span> | <span data-ttu-id="b97cb-900">value1</span><span class="sxs-lookup"><span data-stu-id="b97cb-900">value1</span></span> |
| <span data-ttu-id="b97cb-901">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="b97cb-901">array:entries:2</span></span> | <span data-ttu-id="b97cb-902">value2</span><span class="sxs-lookup"><span data-stu-id="b97cb-902">value2</span></span> |
| <span data-ttu-id="b97cb-903">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="b97cb-903">array:entries:4</span></span> | <span data-ttu-id="b97cb-904">value4</span><span class="sxs-lookup"><span data-stu-id="b97cb-904">value4</span></span> |
| <span data-ttu-id="b97cb-905">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="b97cb-905">array:entries:5</span></span> | <span data-ttu-id="b97cb-906">value5</span><span class="sxs-lookup"><span data-stu-id="b97cb-906">value5</span></span> |

<span data-ttu-id="b97cb-907">Queste chiavi e i valori vengono caricati nell'app di esempio usando il provider di configurazione della memoria:</span><span class="sxs-lookup"><span data-stu-id="b97cb-907">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="b97cb-908">La matrice ignora un valore per l'indice &num;3.</span><span class="sxs-lookup"><span data-stu-id="b97cb-908">The array skips a value for index &num;3.</span></span> <span data-ttu-id="b97cb-909">Lo strumento di associazione di configurazione non è in grado di associare valori null o di creare voci null negli oggetti associati, situazione che diventerà chiara tra poco quando viene illustrato il risultato dell'associazione di questa matrice a un oggetto.</span><span class="sxs-lookup"><span data-stu-id="b97cb-909">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="b97cb-910">Nell'app di esempio, è disponibile una classe POCO per i dati di configurazione associati:</span><span class="sxs-lookup"><span data-stu-id="b97cb-910">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="b97cb-911">I dati di configurazione sono associati all'oggetto:</span><span class="sxs-lookup"><span data-stu-id="b97cb-911">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="b97cb-912">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) è anche possibile usare la sintassi, che comporta un codice più compatto:</span><span class="sxs-lookup"><span data-stu-id="b97cb-912">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="b97cb-913">L'oggetto associato, un'istanza di `ArrayExample`, riceve i dati di matrice dalla configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-913">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="b97cb-914">Indice di `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="b97cb-914">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="b97cb-915">Valore di `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="b97cb-915">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="b97cb-916">0</span><span class="sxs-lookup"><span data-stu-id="b97cb-916">0</span></span>                            | <span data-ttu-id="b97cb-917">value0</span><span class="sxs-lookup"><span data-stu-id="b97cb-917">value0</span></span>                       |
| <span data-ttu-id="b97cb-918">1</span><span class="sxs-lookup"><span data-stu-id="b97cb-918">1</span></span>                            | <span data-ttu-id="b97cb-919">value1</span><span class="sxs-lookup"><span data-stu-id="b97cb-919">value1</span></span>                       |
| <span data-ttu-id="b97cb-920">2</span><span class="sxs-lookup"><span data-stu-id="b97cb-920">2</span></span>                            | <span data-ttu-id="b97cb-921">value2</span><span class="sxs-lookup"><span data-stu-id="b97cb-921">value2</span></span>                       |
| <span data-ttu-id="b97cb-922">3</span><span class="sxs-lookup"><span data-stu-id="b97cb-922">3</span></span>                            | <span data-ttu-id="b97cb-923">value4</span><span class="sxs-lookup"><span data-stu-id="b97cb-923">value4</span></span>                       |
| <span data-ttu-id="b97cb-924">4</span><span class="sxs-lookup"><span data-stu-id="b97cb-924">4</span></span>                            | <span data-ttu-id="b97cb-925">value5</span><span class="sxs-lookup"><span data-stu-id="b97cb-925">value5</span></span>                       |

<span data-ttu-id="b97cb-926">L'indice &num;3 nell'oggetto associato contiene i dati di configurazione per la chiave di configurazione `array:4` e il relativo valore `value4`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-926">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="b97cb-927">Quando i dati di configurazione contenenti una matrice vengono associati, gli indici di matrice nelle chiavi di configurazione vengono usati semplicemente per scorrere i dati di configurazione quando si crea l'oggetto.</span><span class="sxs-lookup"><span data-stu-id="b97cb-927">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="b97cb-928">Un valore null non può essere mantenuto nei dati di configurazione e una voce con valore null non viene creata in un oggetto associato quando una matrice nelle chiavi di configurazione ignora uno o più indici.</span><span class="sxs-lookup"><span data-stu-id="b97cb-928">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="b97cb-929">L'elemento di configurazione mancante per l'indice &num;3 può essere fornito prima dell'associazione all'istanza `ArrayExample` da qualsiasi provider di configurazione che produce la coppia chiave-valore corretta nella configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-929">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="b97cb-930">Se il codice di esempio include un provider di configurazione JSON aggiuntivo con la coppia chiave-valore mancante, `ArrayExample.Entries` corrisponde alla matrice di configurazione completa:</span><span class="sxs-lookup"><span data-stu-id="b97cb-930">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="b97cb-931">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="b97cb-931">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="b97cb-932">In `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="b97cb-932">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="b97cb-933">La coppia chiave-valore mostrata nella tabella viene caricata nella configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-933">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="b97cb-934">Chiave</span><span class="sxs-lookup"><span data-stu-id="b97cb-934">Key</span></span>             | <span data-ttu-id="b97cb-935">valore</span><span class="sxs-lookup"><span data-stu-id="b97cb-935">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="b97cb-936">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="b97cb-936">array:entries:3</span></span> | <span data-ttu-id="b97cb-937">value3</span><span class="sxs-lookup"><span data-stu-id="b97cb-937">value3</span></span> |

<span data-ttu-id="b97cb-938">Se l'istanza della classe `ArrayExample` viene associata dopo che il provider di configurazione JSON include la voce per l'indice &num;3, la matrice `ArrayExample.Entries` include il valore.</span><span class="sxs-lookup"><span data-stu-id="b97cb-938">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="b97cb-939">Indice di `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="b97cb-939">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="b97cb-940">Valore di `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="b97cb-940">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="b97cb-941">0</span><span class="sxs-lookup"><span data-stu-id="b97cb-941">0</span></span>                            | <span data-ttu-id="b97cb-942">value0</span><span class="sxs-lookup"><span data-stu-id="b97cb-942">value0</span></span>                       |
| <span data-ttu-id="b97cb-943">1</span><span class="sxs-lookup"><span data-stu-id="b97cb-943">1</span></span>                            | <span data-ttu-id="b97cb-944">value1</span><span class="sxs-lookup"><span data-stu-id="b97cb-944">value1</span></span>                       |
| <span data-ttu-id="b97cb-945">2</span><span class="sxs-lookup"><span data-stu-id="b97cb-945">2</span></span>                            | <span data-ttu-id="b97cb-946">value2</span><span class="sxs-lookup"><span data-stu-id="b97cb-946">value2</span></span>                       |
| <span data-ttu-id="b97cb-947">3</span><span class="sxs-lookup"><span data-stu-id="b97cb-947">3</span></span>                            | <span data-ttu-id="b97cb-948">value3</span><span class="sxs-lookup"><span data-stu-id="b97cb-948">value3</span></span>                       |
| <span data-ttu-id="b97cb-949">4</span><span class="sxs-lookup"><span data-stu-id="b97cb-949">4</span></span>                            | <span data-ttu-id="b97cb-950">value4</span><span class="sxs-lookup"><span data-stu-id="b97cb-950">value4</span></span>                       |
| <span data-ttu-id="b97cb-951">5</span><span class="sxs-lookup"><span data-stu-id="b97cb-951">5</span></span>                            | <span data-ttu-id="b97cb-952">value5</span><span class="sxs-lookup"><span data-stu-id="b97cb-952">value5</span></span>                       |

<span data-ttu-id="b97cb-953">**Elaborazione di matrice JSON**</span><span class="sxs-lookup"><span data-stu-id="b97cb-953">**JSON array processing**</span></span>

<span data-ttu-id="b97cb-954">Se un file JSON contiene una matrice, vengono create chiavi di configurazione per gli elementi della matrice con un indice di sezione in base zero.</span><span class="sxs-lookup"><span data-stu-id="b97cb-954">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="b97cb-955">Nel file di configurazione seguente, `subsection` è una matrice:</span><span class="sxs-lookup"><span data-stu-id="b97cb-955">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="b97cb-956">Il provider di configurazione JSON legge i dati di configurazione nelle coppie chiave-valore seguenti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-956">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="b97cb-957">Chiave</span><span class="sxs-lookup"><span data-stu-id="b97cb-957">Key</span></span>                     | <span data-ttu-id="b97cb-958">valore</span><span class="sxs-lookup"><span data-stu-id="b97cb-958">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="b97cb-959">json_array:key</span><span class="sxs-lookup"><span data-stu-id="b97cb-959">json_array:key</span></span>          | <span data-ttu-id="b97cb-960">valueA</span><span class="sxs-lookup"><span data-stu-id="b97cb-960">valueA</span></span> |
| <span data-ttu-id="b97cb-961">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="b97cb-961">json_array:subsection:0</span></span> | <span data-ttu-id="b97cb-962">valueB</span><span class="sxs-lookup"><span data-stu-id="b97cb-962">valueB</span></span> |
| <span data-ttu-id="b97cb-963">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="b97cb-963">json_array:subsection:1</span></span> | <span data-ttu-id="b97cb-964">valueC</span><span class="sxs-lookup"><span data-stu-id="b97cb-964">valueC</span></span> |
| <span data-ttu-id="b97cb-965">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="b97cb-965">json_array:subsection:2</span></span> | <span data-ttu-id="b97cb-966">valueD</span><span class="sxs-lookup"><span data-stu-id="b97cb-966">valueD</span></span> |

<span data-ttu-id="b97cb-967">Nell'app di esempio, la classe POCO seguente è disponibile per associare le coppie chiave-valore della configurazione:</span><span class="sxs-lookup"><span data-stu-id="b97cb-967">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="b97cb-968">Dopo l'associazione, `JsonArrayExample.Key` contiene il valore `valueA`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-968">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="b97cb-969">I valori di sottosezione vengono archiviati nella proprietà matrice POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-969">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="b97cb-970">Indice di `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="b97cb-970">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="b97cb-971">Valore di `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="b97cb-971">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="b97cb-972">0</span><span class="sxs-lookup"><span data-stu-id="b97cb-972">0</span></span>                                   | <span data-ttu-id="b97cb-973">valueB</span><span class="sxs-lookup"><span data-stu-id="b97cb-973">valueB</span></span>                              |
| <span data-ttu-id="b97cb-974">1</span><span class="sxs-lookup"><span data-stu-id="b97cb-974">1</span></span>                                   | <span data-ttu-id="b97cb-975">valueC</span><span class="sxs-lookup"><span data-stu-id="b97cb-975">valueC</span></span>                              |
| <span data-ttu-id="b97cb-976">2</span><span class="sxs-lookup"><span data-stu-id="b97cb-976">2</span></span>                                   | <span data-ttu-id="b97cb-977">valueD</span><span class="sxs-lookup"><span data-stu-id="b97cb-977">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="b97cb-978">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="b97cb-978">Custom configuration provider</span></span>

<span data-ttu-id="b97cb-979">L'app di esempio dimostra come creare un provider di configurazione di base che legge le coppie chiave-valore di configurazione da un database usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="b97cb-979">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="b97cb-980">Il provider ha le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="b97cb-980">The provider has the following characteristics:</span></span>

* <span data-ttu-id="b97cb-981">Il database in memoria di Entity Framework viene usato a scopo dimostrativo.</span><span class="sxs-lookup"><span data-stu-id="b97cb-981">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="b97cb-982">Per usare un database che richiede una stringa di connessione, implementare un `ConfigurationBuilder` secondario per fornire la stringa di connessione da un altro provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="b97cb-982">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="b97cb-983">Il provider legge una tabella di database in una configurazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="b97cb-983">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="b97cb-984">Il provider non esegue una query sul database per ogni chiave.</span><span class="sxs-lookup"><span data-stu-id="b97cb-984">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="b97cb-985">Il ricaricamento in caso di modifica non è implementato, quindi l'aggiornamento del database dopo l'avvio dell'app non ha alcun effetto sulla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-985">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="b97cb-986">Definire un'entità `EFConfigurationValue` per l'archiviazione dei valori di configurazione nel database.</span><span class="sxs-lookup"><span data-stu-id="b97cb-986">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="b97cb-987">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="b97cb-987">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="b97cb-988">Aggiungere `EFConfigurationContext` per archiviare i valori configurati e accedervi.</span><span class="sxs-lookup"><span data-stu-id="b97cb-988">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="b97cb-989">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="b97cb-989">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="b97cb-990">Creare una classe che implementi <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-990">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="b97cb-991">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="b97cb-991">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="b97cb-992">Creare il provider di configurazione personalizzato ereditando da <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-992">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="b97cb-993">Il provider di configurazione inizializza il database quando è vuoto.</span><span class="sxs-lookup"><span data-stu-id="b97cb-993">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="b97cb-994">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="b97cb-994">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="b97cb-995">Un metodo di estensione `AddEFConfiguration` consente di aggiungere l'origine di configurazione a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-995">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="b97cb-996">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="b97cb-996">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="b97cb-997">L'esempio di codice seguente mostra come usare il `EFConfigurationProvider` personalizzato in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b97cb-997">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="b97cb-998">Accedere alla configurazione durante l'avvio</span><span class="sxs-lookup"><span data-stu-id="b97cb-998">Access configuration during startup</span></span>

<span data-ttu-id="b97cb-999">Inserire `IConfiguration` nel costruttore `Startup` per accedere ai valori di configurazione in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b97cb-999">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b97cb-1000">Per accedere alla configurazione in `Startup.Configure`, inserire `IConfiguration` direttamente nel metodo o usare l'istanza dal costruttore:</span><span class="sxs-lookup"><span data-stu-id="b97cb-1000">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="b97cb-1001">Per un esempio di accesso alla configurazione usando metodi di servizio di avvio, vedere [Avvio dell'applicazione: Metodi pratici](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="b97cb-1001">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="b97cb-1002">Accedere alla configurazione in una Razor pagina di pagine o in una visualizzazione MVC</span><span class="sxs-lookup"><span data-stu-id="b97cb-1002">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="b97cb-1003">Per accedere alle impostazioni di configurazione in una Razor pagina di pagine o in una visualizzazione MVC, aggiungere una [direttiva using](xref:mvc/views/razor#using) ([riferimenti per C#: direttiva using](/dotnet/csharp/language-reference/keywords/using-directive)) per lo [ spazio dei nomiMicrosoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) e inserire <xref:Microsoft.Extensions.Configuration.IConfiguration> nella pagina o nella vista.</span><span class="sxs-lookup"><span data-stu-id="b97cb-1003">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="b97cb-1004">In una Razor pagina di pagine:</span><span class="sxs-lookup"><span data-stu-id="b97cb-1004">In a Razor Pages page:</span></span>

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

<span data-ttu-id="b97cb-1005">In una visualizzazione MVC:</span><span class="sxs-lookup"><span data-stu-id="b97cb-1005">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="b97cb-1006">Aggiungere la configurazione da un assembly esterno</span><span class="sxs-lookup"><span data-stu-id="b97cb-1006">Add configuration from an external assembly</span></span>

<span data-ttu-id="b97cb-1007">Un'implementazione <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> consente l'aggiunta di miglioramenti a un'app all'avvio, da un assembly esterno alla classe `Startup` dell'app.</span><span class="sxs-lookup"><span data-stu-id="b97cb-1007">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="b97cb-1008">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b97cb-1008">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b97cb-1009">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b97cb-1009">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
