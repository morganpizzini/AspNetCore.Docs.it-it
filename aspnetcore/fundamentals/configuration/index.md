---
<span data-ttu-id="d1a34-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-102">'Blazor'</span></span>
- <span data-ttu-id="d1a34-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-103">'Identity'</span></span>
- <span data-ttu-id="d1a34-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-105">'Razor'</span></span>
- <span data-ttu-id="d1a34-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-106">'SignalR' uid:</span></span> 

---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="d1a34-107">Configurazione in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d1a34-107">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="d1a34-108">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="d1a34-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d1a34-109">La configurazione in ASP.NET Core viene eseguita utilizzando uno o più [provider di configurazione](#cp).</span><span class="sxs-lookup"><span data-stu-id="d1a34-109">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="d1a34-110">I provider di configurazione leggono i dati di configurazione da coppie chiave-valore usando un'ampia gamma di origini di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-110">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="d1a34-111">File di impostazioni, ad esempio *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="d1a34-111">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="d1a34-112">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="d1a34-112">Environment variables</span></span>
* <span data-ttu-id="d1a34-113">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="d1a34-113">Azure Key Vault</span></span>
* <span data-ttu-id="d1a34-114">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="d1a34-114">Azure App Configuration</span></span>
* <span data-ttu-id="d1a34-115">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="d1a34-115">Command-line arguments</span></span>
* <span data-ttu-id="d1a34-116">Provider personalizzati, installati o creati</span><span class="sxs-lookup"><span data-stu-id="d1a34-116">Custom providers, installed or created</span></span>
* <span data-ttu-id="d1a34-117">File della directory</span><span class="sxs-lookup"><span data-stu-id="d1a34-117">Directory files</span></span>
* <span data-ttu-id="d1a34-118">Oggetti .NET in memoria</span><span class="sxs-lookup"><span data-stu-id="d1a34-118">In-memory .NET objects</span></span>

<span data-ttu-id="d1a34-119">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d1a34-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="d1a34-120">Configurazione predefinita</span><span class="sxs-lookup"><span data-stu-id="d1a34-120">Default configuration</span></span>

<span data-ttu-id="d1a34-121">ASP.NET Core app Web create con [DotNet New](/dotnet/core/tools/dotnet-new) o Visual Studio generano il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="d1a34-121">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="d1a34-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fornisce la configurazione predefinita per l'app nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="d1a34-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="d1a34-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : aggiunge un oggetto esistente `IConfiguration` come origine.</span><span class="sxs-lookup"><span data-stu-id="d1a34-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="d1a34-124">Nel caso di configurazione predefinita, aggiunge la configurazione [host](#hvac) e la imposta come prima origine per la configurazione dell' _app_ .</span><span class="sxs-lookup"><span data-stu-id="d1a34-124">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="d1a34-125">[appSettings. JSON](#appsettingsjson) con il [provider di configurazione JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d1a34-125">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="d1a34-126">*appSettings.* `Environment` *. JSON* con il [provider di configurazione JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d1a34-126">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="d1a34-127">Ad esempio, *appSettings*. ***Produzione***. *JSON* e *appSettings*. ***Sviluppo***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-127">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="d1a34-128">[Segreti dell'app](xref:security/app-secrets) quando l'app viene eseguita nell' `Development` ambiente.</span><span class="sxs-lookup"><span data-stu-id="d1a34-128">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="d1a34-129">Variabili di ambiente che usano il [provider di configurazione delle variabili di ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="d1a34-129">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="d1a34-130">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line).</span><span class="sxs-lookup"><span data-stu-id="d1a34-130">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="d1a34-131">I provider di configurazione aggiunti successivamente sostituiscono le impostazioni di chiave precedenti.</span><span class="sxs-lookup"><span data-stu-id="d1a34-131">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="d1a34-132">Se, ad esempio, `MyKey` è impostato sia in *appSettings. JSON* che nell'ambiente, viene utilizzato il valore dell'ambiente.</span><span class="sxs-lookup"><span data-stu-id="d1a34-132">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="d1a34-133">Utilizzando i provider di configurazione predefiniti, il [provider di configurazione della riga di comando](#command-line-configuration-provider) esegue l'override di tutti gli altri provider.</span><span class="sxs-lookup"><span data-stu-id="d1a34-133">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="d1a34-134">Per ulteriori informazioni su `CreateDefaultBuilder` , vedere [impostazioni predefinite del generatore](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="d1a34-134">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="d1a34-135">Il codice seguente Visualizza i provider di configurazione abilitati nell'ordine in cui sono stati aggiunti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-135">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="d1a34-136">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="d1a34-136">appsettings.json</span></span>

<span data-ttu-id="d1a34-137">Si consideri il file *appSettings. JSON* seguente:</span><span class="sxs-lookup"><span data-stu-id="d1a34-137">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="d1a34-138">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-138">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d1a34-139">Il valore predefinito <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carica la configurazione nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="d1a34-139">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="d1a34-140">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="d1a34-140">*appsettings.json*</span></span>
1. <span data-ttu-id="d1a34-141">*appSettings.* `Environment` *. JSON* : ad esempio, *appSettings*. ***Produzione***. *JSON* e *appSettings*. ***Sviluppo***. file *JSON* .</span><span class="sxs-lookup"><span data-stu-id="d1a34-141">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="d1a34-142">La versione dell'ambiente del file viene caricata in base a [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="d1a34-142">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="d1a34-143">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-143">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d1a34-144">*appSettings*. `Environment` . i valori *JSON* eseguono l'override delle chiavi in *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-144">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="d1a34-145">Ad esempio, per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="d1a34-145">For example, by default:</span></span>

* <span data-ttu-id="d1a34-146">In fase di sviluppo, *appSettings*. ***Sviluppo***. la configurazione *JSON* sovrascrive i valori trovati in *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-146">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="d1a34-147">In produzione, *appSettings*. ***Produzione***. la configurazione *JSON* sovrascrive i valori trovati in *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-147">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="d1a34-148">Ad esempio, quando si distribuisce l'app in Azure.</span><span class="sxs-lookup"><span data-stu-id="d1a34-148">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="d1a34-149">Associare i dati di configurazione gerarchici usando il modello di opzioni</span><span class="sxs-lookup"><span data-stu-id="d1a34-149">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="d1a34-150">Utilizzando la configurazione [predefinita](#default) , *appSettings. JSON* e *appSettings.* `Environment` i file con *estensione JSON* sono abilitati con [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="d1a34-150">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="d1a34-151">Modifiche apportate a *appSettings. JSON* e *appSettings.* `Environment` il file con *estensione JSON* ***dopo*** l'avvio dell'app viene letto dal [provider di configurazione JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="d1a34-151">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="d1a34-152">Per informazioni sull'aggiunta di altri file di configurazione JSON, vedere [provider di configurazione JSON](#jcp) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="d1a34-152">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="d1a34-153">Gestione della sicurezza e del segreto</span><span class="sxs-lookup"><span data-stu-id="d1a34-153">Security and secret manager</span></span>

<span data-ttu-id="d1a34-154">Linee guida sui dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-154">Configuration data guidelines:</span></span>

* <span data-ttu-id="d1a34-155">Non archiviare mai la password o altri dati sensibili nel codice del provider di configurazione o in file di configurazione di testo normale.</span><span class="sxs-lookup"><span data-stu-id="d1a34-155">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="d1a34-156">Il [gestore del segreto](xref:security/app-secrets) può essere usato per archiviare i segreti in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-156">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="d1a34-157">Non usare i segreti di produzione in ambienti di sviluppo o di test.</span><span class="sxs-lookup"><span data-stu-id="d1a34-157">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="d1a34-158">Specificare i segreti all'esterno del progetto in modo che non possano essere inavvertitamente inviati a un repository del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="d1a34-158">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="d1a34-159">Per [impostazione predefinita](#default), [Secret Manager](xref:security/app-secrets) legge le impostazioni di configurazione dopo *appSettings. JSON* e *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-159">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="d1a34-160">Per ulteriori informazioni sull'archiviazione di password o altri dati sensibili:</span><span class="sxs-lookup"><span data-stu-id="d1a34-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="d1a34-161"><xref:security/app-secrets>: Include consigli sull'uso delle variabili di ambiente per archiviare dati riservati.</span><span class="sxs-lookup"><span data-stu-id="d1a34-161"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="d1a34-162">Il gestore dei segreti USA il [provider di configurazione file](#fcp) per archiviare i segreti utente in un file JSON nel sistema locale.</span><span class="sxs-lookup"><span data-stu-id="d1a34-162">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="d1a34-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) archivia in modo sicuro i segreti delle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d1a34-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="d1a34-164">Per altre informazioni, vedere <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="d1a34-165">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="d1a34-165">Environment variables</span></span>

<span data-ttu-id="d1a34-166">Usando la configurazione [predefinita](#default) , <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carica la configurazione dalle coppie chiave-valore della variabile di ambiente dopo aver letto *appSettings. JSON*, *appSettings.* `Environment` *. JSON*e [gestione segreta](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="d1a34-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="d1a34-167">Pertanto, i valori di chiave letti dall'ambiente eseguono l'override dei valori letti da *appSettings. JSON*, *appSettings.* `Environment` *. JSON*e gestione segreta.</span><span class="sxs-lookup"><span data-stu-id="d1a34-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="d1a34-168">I `set` comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-168">The following `set` commands:</span></span>

* <span data-ttu-id="d1a34-169">Impostare le chiavi e i valori di ambiente dell' [esempio precedente](#appsettingsjson) in Windows.</span><span class="sxs-lookup"><span data-stu-id="d1a34-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="d1a34-170">Testare le impostazioni quando si usa il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="d1a34-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="d1a34-171">Il `dotnet run` comando deve essere eseguito nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="d1a34-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="d1a34-172">Le impostazioni di ambiente precedenti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-172">The preceding environment settings:</span></span>

* <span data-ttu-id="d1a34-173">Vengono impostati solo nei processi avviati dalla finestra di comando in cui sono stati impostati.</span><span class="sxs-lookup"><span data-stu-id="d1a34-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="d1a34-174">Non verrà letto dai browser avviati con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d1a34-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="d1a34-175">Per impostare le chiavi e i valori di ambiente in Windows, è possibile usare i comandi [Setx](/windows-server/administration/windows-commands/setx) seguenti.</span><span class="sxs-lookup"><span data-stu-id="d1a34-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="d1a34-176">Diversamente da `set` , `setx` le impostazioni sono rese permanente.</span><span class="sxs-lookup"><span data-stu-id="d1a34-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="d1a34-177">`/M`imposta la variabile nell'ambiente di sistema.</span><span class="sxs-lookup"><span data-stu-id="d1a34-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="d1a34-178">Se l' `/M` opzione non viene usata, viene impostata una variabile di ambiente utente.</span><span class="sxs-lookup"><span data-stu-id="d1a34-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="d1a34-179">Per verificare che i comandi precedenti eseguano l'override di *appSettings. JSON* e *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="d1a34-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="d1a34-180">Con Visual Studio: chiudere e riavviare Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d1a34-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="d1a34-181">Con l'interfaccia della riga di comando: avviare una nuova finestra di comando e immettere `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="d1a34-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="d1a34-182">Chiamare <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> con una stringa per specificare un prefisso per le variabili di ambiente:</span><span class="sxs-lookup"><span data-stu-id="d1a34-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="d1a34-183">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="d1a34-183">In the preceding code:</span></span>

* <span data-ttu-id="d1a34-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`viene aggiunto dopo i [provider di configurazione predefiniti](#default).</span><span class="sxs-lookup"><span data-stu-id="d1a34-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="d1a34-185">Per un esempio di ordinamento dei provider di configurazione, vedere [provider di configurazione JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="d1a34-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="d1a34-186">Le variabili di ambiente impostate con il `MyCustomPrefix_` prefisso sostituiscono i [provider di configurazione predefiniti](#default).</span><span class="sxs-lookup"><span data-stu-id="d1a34-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="d1a34-187">Sono incluse le variabili di ambiente senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="d1a34-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="d1a34-188">Il prefisso viene rimosso quando vengono lette le coppie chiave-valore di configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="d1a34-189">I comandi seguenti verificano il prefisso personalizzato:</span><span class="sxs-lookup"><span data-stu-id="d1a34-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="d1a34-190">La [configurazione predefinita](#default) carica le variabili di ambiente e gli argomenti della riga di comando preceduti da `DOTNET_` e `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="d1a34-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="d1a34-191">I `DOTNET_` `ASPNETCORE_` prefissi e vengono usati da ASP.NET Core per la [configurazione dell'host e dell'app](xref:fundamentals/host/generic-host#host-configuration), ma non per la configurazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="d1a34-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="d1a34-192">Per ulteriori informazioni sulla configurazione dell'host e dell'app, vedere [.NET Generic Host](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="d1a34-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="d1a34-193">In [app Azure servizio](https://azure.microsoft.com/services/app-service/)selezionare **impostazione nuova applicazione** nella pagina **Impostazioni > configurazione** .</span><span class="sxs-lookup"><span data-stu-id="d1a34-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="d1a34-194">App Azure impostazioni dell'applicazione del servizio sono:</span><span class="sxs-lookup"><span data-stu-id="d1a34-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="d1a34-195">Crittografati a riposo e trasmessi su un canale crittografato.</span><span class="sxs-lookup"><span data-stu-id="d1a34-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="d1a34-196">Esposto come variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="d1a34-196">Exposed as environment variables.</span></span>

<span data-ttu-id="d1a34-197">Per altre informazioni, vedere [App di Azure: Eseguire l'override della configurazione delle app usando il portale di Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="d1a34-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="d1a34-198">Per informazioni sulle stringhe di connessione del database di Azure, vedere [prefissi della stringa di connessione](#constr) .</span><span class="sxs-lookup"><span data-stu-id="d1a34-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="d1a34-199">Riga di comando</span><span class="sxs-lookup"><span data-stu-id="d1a34-199">Command-line</span></span>

<span data-ttu-id="d1a34-200">Utilizzando la configurazione [predefinita](#default) , <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carica la configurazione dalle coppie chiave-valore dell'argomento della riga di comando dopo le origini di configurazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-200">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="d1a34-201">*appSettings. JSON* e *appSettings*. `Environment` . file *JSON* .</span><span class="sxs-lookup"><span data-stu-id="d1a34-201">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="d1a34-202">[Segreti dell'app (gestione segreto)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-202">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d1a34-203">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="d1a34-203">Environment variables.</span></span>

<span data-ttu-id="d1a34-204">Per [impostazione predefinita](#default), i valori di configurazione impostati nei valori di configurazione di override della riga di comando impostati con tutti gli altri provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-204">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="d1a34-205">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="d1a34-205">Command-line arguments</span></span>

<span data-ttu-id="d1a34-206">Il comando seguente imposta le chiavi e i valori usando `=` :</span><span class="sxs-lookup"><span data-stu-id="d1a34-206">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="d1a34-207">Il comando seguente imposta le chiavi e i valori usando `/` :</span><span class="sxs-lookup"><span data-stu-id="d1a34-207">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="d1a34-208">Il comando seguente imposta le chiavi e i valori usando `--` :</span><span class="sxs-lookup"><span data-stu-id="d1a34-208">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="d1a34-209">Valore chiave:</span><span class="sxs-lookup"><span data-stu-id="d1a34-209">The key value:</span></span>

* <span data-ttu-id="d1a34-210">Deve seguire `=` oppure la chiave deve avere un prefisso `--` o `/` quando il valore segue uno spazio.</span><span class="sxs-lookup"><span data-stu-id="d1a34-210">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="d1a34-211">Non è obbligatorio se `=` si usa.</span><span class="sxs-lookup"><span data-stu-id="d1a34-211">Isn't required if `=` is used.</span></span> <span data-ttu-id="d1a34-212">Ad esempio: `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-212">For example, `MySetting=`.</span></span>

<span data-ttu-id="d1a34-213">All'interno dello stesso comando, non combinare coppie chiave-valore dell'argomento della riga di comando che usano `=` con coppie chiave-valore che usano uno spazio.</span><span class="sxs-lookup"><span data-stu-id="d1a34-213">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="d1a34-214">Mapping di sostituzione</span><span class="sxs-lookup"><span data-stu-id="d1a34-214">Switch mappings</span></span>

<span data-ttu-id="d1a34-215">I mapping switch consentono la logica di sostituzione del nome **chiave** .</span><span class="sxs-lookup"><span data-stu-id="d1a34-215">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="d1a34-216">Fornire un dizionario di sostituzioni switch al <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-216">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="d1a34-217">Quando viene utilizzato il dizionario dei mapping di sostituzione, nel dizionario viene controllata la presenza di una chiave corrispondente alla chiave fornita da un argomento della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="d1a34-217">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="d1a34-218">Se la chiave della riga di comando viene trovata nel dizionario, il valore del dizionario viene passato di nuovo per impostare la coppia chiave-valore nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-218">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="d1a34-219">Un mapping di sostituzione è necessario per le chiavi della riga di comando con un trattino singolo (`-`) come prefisso.</span><span class="sxs-lookup"><span data-stu-id="d1a34-219">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="d1a34-220">Regole principali del dizionario dei mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-220">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="d1a34-221">Le opzioni devono iniziare con `-` o `--` .</span><span class="sxs-lookup"><span data-stu-id="d1a34-221">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="d1a34-222">Il dizionario dei mapping di sostituzione non deve contenere chiavi duplicate.</span><span class="sxs-lookup"><span data-stu-id="d1a34-222">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="d1a34-223">Per usare un dizionario dei mapping switch, passarlo alla chiamata a `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="d1a34-223">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="d1a34-224">Il codice seguente mostra i valori chiave per le chiavi sostituite:</span><span class="sxs-lookup"><span data-stu-id="d1a34-224">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="d1a34-225">Eseguire il comando seguente per testare la sostituzione della chiave:</span><span class="sxs-lookup"><span data-stu-id="d1a34-225">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="d1a34-226">Nota: attualmente `=` non è possibile usare per impostare i valori di sostituzione delle chiavi con un trattino singolo `-` .</span><span class="sxs-lookup"><span data-stu-id="d1a34-226">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="d1a34-227">Vedere [il problema in GitHub](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="d1a34-227">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="d1a34-228">Il comando seguente funziona per testare la sostituzione della chiave:</span><span class="sxs-lookup"><span data-stu-id="d1a34-228">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="d1a34-229">Per le app che usano i mapping di sostituzione, la chiamata a `CreateDefaultBuilder` non deve passare argomenti.</span><span class="sxs-lookup"><span data-stu-id="d1a34-229">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="d1a34-230">La `CreateDefaultBuilder` chiamata al metodo `AddCommandLine` non include opzioni mappate e non è possibile passare il dizionario di mapping switch a `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d1a34-230">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d1a34-231">La soluzione non passa gli argomenti a `CreateDefaultBuilder` ma per consentire al metodo del `ConfigurationBuilder` metodo `AddCommandLine` di elaborare sia gli argomenti sia il dizionario di mapping delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="d1a34-231">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="d1a34-232">Dati di configurazione gerarchici</span><span class="sxs-lookup"><span data-stu-id="d1a34-232">Hierarchical configuration data</span></span>

<span data-ttu-id="d1a34-233">L'API di configurazione legge i dati di configurazione gerarchici rendendo flat i dati gerarchici con l'uso di un delimitatore nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-233">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="d1a34-234">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il file *appSettings. JSON* seguente:</span><span class="sxs-lookup"><span data-stu-id="d1a34-234">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="d1a34-235">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-235">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d1a34-236">Il modo migliore per leggere i dati di configurazione gerarchici consiste nell'usare il modello di opzioni.</span><span class="sxs-lookup"><span data-stu-id="d1a34-236">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="d1a34-237">Per altre informazioni, vedere [associare dati di configurazione gerarchici](#optpat) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="d1a34-237">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="d1a34-238">I metodi <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sono disponibili per isolare le sezioni e gli elementi figlio di una sezione nei dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="d1a34-239">Questi metodi sono descritti più avanti in [GetSection, GetChildren ed Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="d1a34-239">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="d1a34-240">Chiavi e valori di configurazione</span><span class="sxs-lookup"><span data-stu-id="d1a34-240">Configuration keys and values</span></span>

<span data-ttu-id="d1a34-241">Chiavi di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-241">Configuration keys:</span></span>

* <span data-ttu-id="d1a34-242">Non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="d1a34-242">Are case-insensitive.</span></span> <span data-ttu-id="d1a34-243">Ad esempio, `ConnectionString` e `connectionstring` vengono considerate chiavi equivalenti.</span><span class="sxs-lookup"><span data-stu-id="d1a34-243">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="d1a34-244">Se una chiave e un valore vengono impostati in più provider di configurazione, viene utilizzato il valore dell'ultimo provider aggiunto.</span><span class="sxs-lookup"><span data-stu-id="d1a34-244">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="d1a34-245">Per ulteriori informazioni, vedere [configurazione predefinita](#default).</span><span class="sxs-lookup"><span data-stu-id="d1a34-245">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="d1a34-246">Chiavi gerarchiche</span><span class="sxs-lookup"><span data-stu-id="d1a34-246">Hierarchical keys</span></span>
  * <span data-ttu-id="d1a34-247">Nell'ambito dell'API di configurazione, il separatore due punti (`:`) funziona in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="d1a34-247">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="d1a34-248">Nelle variabili di ambiente, un separatore due punti potrebbe non funzionare in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="d1a34-248">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="d1a34-249">Un doppio carattere di sottolineatura, `__` , è supportato da tutte le piattaforme e viene convertito automaticamente in due punti `:` .</span><span class="sxs-lookup"><span data-stu-id="d1a34-249">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="d1a34-250">In Azure Key Vault, le chiavi gerarchiche utilizzano `--` come separatore.</span><span class="sxs-lookup"><span data-stu-id="d1a34-250">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="d1a34-251">Il [provider di configurazione Azure Key Vault](xref:security/key-vault-configuration) sostituisce automaticamente `--` con un `:` quando i segreti vengono caricati nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-251">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="d1a34-252">Il <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supporta l'associazione di matrici agli oggetti usando gli indici delle matrici nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-252">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d1a34-253">L'associazione di matrici è descritta nella sezione [Associare una matrice a una classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="d1a34-253">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="d1a34-254">Valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-254">Configuration values:</span></span>

* <span data-ttu-id="d1a34-255">Sono stringhe.</span><span class="sxs-lookup"><span data-stu-id="d1a34-255">Are strings.</span></span>
* <span data-ttu-id="d1a34-256">I valori null non possono essere archiviati nella configurazione o associati a oggetti.</span><span class="sxs-lookup"><span data-stu-id="d1a34-256">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="d1a34-257">Provider di configurazione</span><span class="sxs-lookup"><span data-stu-id="d1a34-257">Configuration providers</span></span>

<span data-ttu-id="d1a34-258">La tabella seguente mostra i provider di configurazione disponibili per le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d1a34-258">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="d1a34-259">Provider</span><span class="sxs-lookup"><span data-stu-id="d1a34-259">Provider</span></span> | <span data-ttu-id="d1a34-260">Fornisce la configurazione da</span><span class="sxs-lookup"><span data-stu-id="d1a34-260">Provides configuration from</span></span> |
| ---
<span data-ttu-id="d1a34-261">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-262">'Blazor'</span></span>
- <span data-ttu-id="d1a34-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-263">'Identity'</span></span>
- <span data-ttu-id="d1a34-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-265">'Razor'</span></span>
- <span data-ttu-id="d1a34-266">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-267">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-268">'Blazor'</span></span>
- <span data-ttu-id="d1a34-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-269">'Identity'</span></span>
- <span data-ttu-id="d1a34-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-271">'Razor'</span></span>
- <span data-ttu-id="d1a34-272">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-272">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-273">---- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-274">'Blazor'</span></span>
- <span data-ttu-id="d1a34-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-275">'Identity'</span></span>
- <span data-ttu-id="d1a34-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-277">'Razor'</span></span>
- <span data-ttu-id="d1a34-278">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-279">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-280">'Blazor'</span></span>
- <span data-ttu-id="d1a34-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-281">'Identity'</span></span>
- <span data-ttu-id="d1a34-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-283">'Razor'</span></span>
- <span data-ttu-id="d1a34-284">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-285">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-286">'Blazor'</span></span>
- <span data-ttu-id="d1a34-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-287">'Identity'</span></span>
- <span data-ttu-id="d1a34-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-289">'Razor'</span></span>
- <span data-ttu-id="d1a34-290">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-291">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-292">'Blazor'</span></span>
- <span data-ttu-id="d1a34-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-293">'Identity'</span></span>
- <span data-ttu-id="d1a34-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-295">'Razor'</span></span>
- <span data-ttu-id="d1a34-296">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-297">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-298">'Blazor'</span></span>
- <span data-ttu-id="d1a34-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-299">'Identity'</span></span>
- <span data-ttu-id="d1a34-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-301">'Razor'</span></span>
- <span data-ttu-id="d1a34-302">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-303">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-304">'Blazor'</span></span>
- <span data-ttu-id="d1a34-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-305">'Identity'</span></span>
- <span data-ttu-id="d1a34-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-307">'Razor'</span></span>
- <span data-ttu-id="d1a34-308">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-309">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-310">'Blazor'</span></span>
- <span data-ttu-id="d1a34-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-311">'Identity'</span></span>
- <span data-ttu-id="d1a34-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-313">'Razor'</span></span>
- <span data-ttu-id="d1a34-314">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-315">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-316">'Blazor'</span></span>
- <span data-ttu-id="d1a34-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-317">'Identity'</span></span>
- <span data-ttu-id="d1a34-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-319">'Razor'</span></span>
- <span data-ttu-id="d1a34-320">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-321">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-322">'Blazor'</span></span>
- <span data-ttu-id="d1a34-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-323">'Identity'</span></span>
- <span data-ttu-id="d1a34-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-325">'Razor'</span></span>
- <span data-ttu-id="d1a34-326">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-327">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-328">'Blazor'</span></span>
- <span data-ttu-id="d1a34-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-329">'Identity'</span></span>
- <span data-ttu-id="d1a34-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-331">'Razor'</span></span>
- <span data-ttu-id="d1a34-332">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-333">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-334">'Blazor'</span></span>
- <span data-ttu-id="d1a34-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-335">'Identity'</span></span>
- <span data-ttu-id="d1a34-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-337">'Razor'</span></span>
- <span data-ttu-id="d1a34-338">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-339">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-340">'Blazor'</span></span>
- <span data-ttu-id="d1a34-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-341">'Identity'</span></span>
- <span data-ttu-id="d1a34-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-343">'Razor'</span></span>
- <span data-ttu-id="d1a34-344">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-345">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-346">'Blazor'</span></span>
- <span data-ttu-id="d1a34-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-347">'Identity'</span></span>
- <span data-ttu-id="d1a34-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-349">'Razor'</span></span>
- <span data-ttu-id="d1a34-350">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-351">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-352">'Blazor'</span></span>
- <span data-ttu-id="d1a34-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-353">'Identity'</span></span>
- <span data-ttu-id="d1a34-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-355">'Razor'</span></span>
- <span data-ttu-id="d1a34-356">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-357">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-358">'Blazor'</span></span>
- <span data-ttu-id="d1a34-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-359">'Identity'</span></span>
- <span data-ttu-id="d1a34-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-361">'Razor'</span></span>
- <span data-ttu-id="d1a34-362">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-362">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-363">------------------ | | [Provider di configurazione Azure Key Vault](xref:security/key-vault-configuration) | Azure Key Vault | | [Provider di configurazione app Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Configurazione app Azure | | [Provider di configurazione della riga di comando](#clcp) | Parametri della riga di comando | | [Provider di configurazione personalizzato](#custom-configuration-provider) | Origine personalizzata | | [Provider di configurazione delle variabili di ambiente](#evcp) | Variabili di ambiente | | [Provider di configurazione file](#file-configuration-provider) | File INI, JSON e XML | | [Provider di configurazione chiave per file](#key-per-file-configuration-provider) | File di directory | | [Provider di configurazione della memoria](#memory-configuration-provider) | Raccolte in memoria | | [Gestore Secret](xref:security/app-secrets) | File nella directory del profilo utente |</span><span class="sxs-lookup"><span data-stu-id="d1a34-363">------------------ | | [Azure Key Vault configuration provider](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Command-line configuration provider](#clcp) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables configuration provider](#evcp) | Environment variables | | [File configuration provider](#file-configuration-provider) | INI, JSON, and XML files | | [Key-per-file configuration provider](#key-per-file-configuration-provider) | Directory files | | [Memory configuration provider](#memory-configuration-provider) | In-memory collections | | [Secret Manager](xref:security/app-secrets)  | File in the user profile directory |</span></span>

<span data-ttu-id="d1a34-364">Le origini di configurazione vengono lette nell'ordine in cui sono specificati i provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-364">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="d1a34-365">Ordinare i provider di configurazione nel codice in base alle priorità per le origini di configurazione sottostanti richieste dall'app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-365">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="d1a34-366">Una sequenza tipica di provider di configurazione è:</span><span class="sxs-lookup"><span data-stu-id="d1a34-366">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="d1a34-367">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="d1a34-367">*appsettings.json*</span></span>
1. <span data-ttu-id="d1a34-368">*appSettings*. `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="d1a34-368">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="d1a34-369">Gestione segreta</span><span class="sxs-lookup"><span data-stu-id="d1a34-369">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="d1a34-370">Variabili di ambiente che usano il [provider di configurazione delle variabili di ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="d1a34-370">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="d1a34-371">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d1a34-371">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="d1a34-372">Una procedura comune consiste nell'aggiungere il provider di configurazione della riga di comando per ultimo in una serie di provider per consentire agli argomenti della riga di comando di eseguire l'override del set di configurazione da parte degli altri provider.</span><span class="sxs-lookup"><span data-stu-id="d1a34-372">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="d1a34-373">La sequenza di provider precedente viene utilizzata nella [configurazione predefinita](#default).</span><span class="sxs-lookup"><span data-stu-id="d1a34-373">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="d1a34-374">Prefissi della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="d1a34-374">Connection string prefixes</span></span>

<span data-ttu-id="d1a34-375">L'API di configurazione dispone di regole di elaborazione speciali per quattro variabili di ambiente della stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-375">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="d1a34-376">Queste stringhe di connessione sono necessarie per configurare le stringhe di connessione di Azure per l'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-376">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="d1a34-377">Le variabili di ambiente con i prefissi visualizzati nella tabella vengono caricate nell'app con la [configurazione predefinita](#default) o quando non viene fornito alcun prefisso a `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="d1a34-377">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="d1a34-378">Prefisso della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="d1a34-378">Connection string prefix</span></span> | <span data-ttu-id="d1a34-379">Provider</span><span class="sxs-lookup"><span data-stu-id="d1a34-379">Provider</span></span> |
| ---
<span data-ttu-id="d1a34-380">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-381">'Blazor'</span></span>
- <span data-ttu-id="d1a34-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-382">'Identity'</span></span>
- <span data-ttu-id="d1a34-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-384">'Razor'</span></span>
- <span data-ttu-id="d1a34-385">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-386">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-387">'Blazor'</span></span>
- <span data-ttu-id="d1a34-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-388">'Identity'</span></span>
- <span data-ttu-id="d1a34-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-390">'Razor'</span></span>
- <span data-ttu-id="d1a34-391">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-392">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-393">'Blazor'</span></span>
- <span data-ttu-id="d1a34-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-394">'Identity'</span></span>
- <span data-ttu-id="d1a34-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-396">'Razor'</span></span>
- <span data-ttu-id="d1a34-397">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-397">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-398">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-399">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-399">'Blazor'</span></span>
- <span data-ttu-id="d1a34-400">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-400">'Identity'</span></span>
- <span data-ttu-id="d1a34-401">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-401">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-402">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-402">'Razor'</span></span>
- <span data-ttu-id="d1a34-403">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-403">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-404">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-405">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-405">'Blazor'</span></span>
- <span data-ttu-id="d1a34-406">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-406">'Identity'</span></span>
- <span data-ttu-id="d1a34-407">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-407">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-408">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-408">'Razor'</span></span>
- <span data-ttu-id="d1a34-409">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-409">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-410">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-411">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-411">'Blazor'</span></span>
- <span data-ttu-id="d1a34-412">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-412">'Identity'</span></span>
- <span data-ttu-id="d1a34-413">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-413">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-414">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-414">'Razor'</span></span>
- <span data-ttu-id="d1a34-415">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-415">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-416">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-417">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-417">'Blazor'</span></span>
- <span data-ttu-id="d1a34-418">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-418">'Identity'</span></span>
- <span data-ttu-id="d1a34-419">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-419">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-420">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-420">'Razor'</span></span>
- <span data-ttu-id="d1a34-421">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-421">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-422">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-423">'Blazor'</span></span>
- <span data-ttu-id="d1a34-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-424">'Identity'</span></span>
- <span data-ttu-id="d1a34-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-426">'Razor'</span></span>
- <span data-ttu-id="d1a34-427">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-428">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-429">'Blazor'</span></span>
- <span data-ttu-id="d1a34-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-430">'Identity'</span></span>
- <span data-ttu-id="d1a34-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-432">'Razor'</span></span>
- <span data-ttu-id="d1a34-433">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-434">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-435">'Blazor'</span></span>
- <span data-ttu-id="d1a34-436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-436">'Identity'</span></span>
- <span data-ttu-id="d1a34-437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-437">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-438">'Razor'</span></span>
- <span data-ttu-id="d1a34-439">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-439">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-440">------------ | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-441">'Blazor'</span></span>
- <span data-ttu-id="d1a34-442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-442">'Identity'</span></span>
- <span data-ttu-id="d1a34-443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-443">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-444">'Razor'</span></span>
- <span data-ttu-id="d1a34-445">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-446">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-447">'Blazor'</span></span>
- <span data-ttu-id="d1a34-448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-448">'Identity'</span></span>
- <span data-ttu-id="d1a34-449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-449">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-450">'Razor'</span></span>
- <span data-ttu-id="d1a34-451">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-451">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-452">---- | | `CUSTOMCONNSTR_` | Provider personalizzato | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
 MySQL | `SQLAZURECONNSTR_` | SQL Server del [database SQL di Azure](https://azure.microsoft.com/services/sql-database/) |
 | `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/)|</span><span class="sxs-lookup"><span data-stu-id="d1a34-452">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="d1a34-453">Quando una variabile di ambiente viene individuata e caricata nella configurazione con uno qualsiasi dei quattro prefissi indicati nella tabella:</span><span class="sxs-lookup"><span data-stu-id="d1a34-453">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="d1a34-454">La chiave di configurazione viene creata rimuovendo il prefisso della variabile di ambiente e aggiungendo una sezione per la chiave di configurazione (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="d1a34-454">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="d1a34-455">Viene creata una nuova coppia chiave-valore della configurazione che rappresenta il provider di connessione al database (ad eccezione di `CUSTOMCONNSTR_`, che non ha un provider dichiarato).</span><span class="sxs-lookup"><span data-stu-id="d1a34-455">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="d1a34-456">Chiave della variabile di ambiente</span><span class="sxs-lookup"><span data-stu-id="d1a34-456">Environment variable key</span></span> | <span data-ttu-id="d1a34-457">Chiave di configurazione convertita</span><span class="sxs-lookup"><span data-stu-id="d1a34-457">Converted configuration key</span></span> | <span data-ttu-id="d1a34-458">Voce di configurazione del provider</span><span class="sxs-lookup"><span data-stu-id="d1a34-458">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="d1a34-459">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-460">'Blazor'</span></span>
- <span data-ttu-id="d1a34-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-461">'Identity'</span></span>
- <span data-ttu-id="d1a34-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-463">'Razor'</span></span>
- <span data-ttu-id="d1a34-464">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-465">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-466">'Blazor'</span></span>
- <span data-ttu-id="d1a34-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-467">'Identity'</span></span>
- <span data-ttu-id="d1a34-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-469">'Razor'</span></span>
- <span data-ttu-id="d1a34-470">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-471">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-472">'Blazor'</span></span>
- <span data-ttu-id="d1a34-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-473">'Identity'</span></span>
- <span data-ttu-id="d1a34-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-475">'Razor'</span></span>
- <span data-ttu-id="d1a34-476">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-477">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-478">'Blazor'</span></span>
- <span data-ttu-id="d1a34-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-479">'Identity'</span></span>
- <span data-ttu-id="d1a34-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-481">'Razor'</span></span>
- <span data-ttu-id="d1a34-482">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-483">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-484">'Blazor'</span></span>
- <span data-ttu-id="d1a34-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-485">'Identity'</span></span>
- <span data-ttu-id="d1a34-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-487">'Razor'</span></span>
- <span data-ttu-id="d1a34-488">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-489">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-490">'Blazor'</span></span>
- <span data-ttu-id="d1a34-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-491">'Identity'</span></span>
- <span data-ttu-id="d1a34-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-493">'Razor'</span></span>
- <span data-ttu-id="d1a34-494">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-495">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-496">'Blazor'</span></span>
- <span data-ttu-id="d1a34-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-497">'Identity'</span></span>
- <span data-ttu-id="d1a34-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-499">'Razor'</span></span>
- <span data-ttu-id="d1a34-500">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-501">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-502">'Blazor'</span></span>
- <span data-ttu-id="d1a34-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-503">'Identity'</span></span>
- <span data-ttu-id="d1a34-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-505">'Razor'</span></span>
- <span data-ttu-id="d1a34-506">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-507">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-508">'Blazor'</span></span>
- <span data-ttu-id="d1a34-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-509">'Identity'</span></span>
- <span data-ttu-id="d1a34-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-511">'Razor'</span></span>
- <span data-ttu-id="d1a34-512">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-513">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-514">'Blazor'</span></span>
- <span data-ttu-id="d1a34-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-515">'Identity'</span></span>
- <span data-ttu-id="d1a34-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-517">'Razor'</span></span>
- <span data-ttu-id="d1a34-518">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-518">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-519">------------ | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-520">'Blazor'</span></span>
- <span data-ttu-id="d1a34-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-521">'Identity'</span></span>
- <span data-ttu-id="d1a34-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-523">'Razor'</span></span>
- <span data-ttu-id="d1a34-524">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-525">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-526">'Blazor'</span></span>
- <span data-ttu-id="d1a34-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-527">'Identity'</span></span>
- <span data-ttu-id="d1a34-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-529">'Razor'</span></span>
- <span data-ttu-id="d1a34-530">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-531">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-532">'Blazor'</span></span>
- <span data-ttu-id="d1a34-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-533">'Identity'</span></span>
- <span data-ttu-id="d1a34-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-535">'Razor'</span></span>
- <span data-ttu-id="d1a34-536">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-537">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-538">'Blazor'</span></span>
- <span data-ttu-id="d1a34-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-539">'Identity'</span></span>
- <span data-ttu-id="d1a34-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-541">'Razor'</span></span>
- <span data-ttu-id="d1a34-542">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-543">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-544">'Blazor'</span></span>
- <span data-ttu-id="d1a34-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-545">'Identity'</span></span>
- <span data-ttu-id="d1a34-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-547">'Razor'</span></span>
- <span data-ttu-id="d1a34-548">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-549">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-550">'Blazor'</span></span>
- <span data-ttu-id="d1a34-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-551">'Identity'</span></span>
- <span data-ttu-id="d1a34-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-553">'Razor'</span></span>
- <span data-ttu-id="d1a34-554">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-555">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-556">'Blazor'</span></span>
- <span data-ttu-id="d1a34-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-557">'Identity'</span></span>
- <span data-ttu-id="d1a34-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-559">'Razor'</span></span>
- <span data-ttu-id="d1a34-560">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-561">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-562">'Blazor'</span></span>
- <span data-ttu-id="d1a34-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-563">'Identity'</span></span>
- <span data-ttu-id="d1a34-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-565">'Razor'</span></span>
- <span data-ttu-id="d1a34-566">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-567">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-568">'Blazor'</span></span>
- <span data-ttu-id="d1a34-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-569">'Identity'</span></span>
- <span data-ttu-id="d1a34-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-571">'Razor'</span></span>
- <span data-ttu-id="d1a34-572">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-573">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-574">'Blazor'</span></span>
- <span data-ttu-id="d1a34-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-575">'Identity'</span></span>
- <span data-ttu-id="d1a34-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-577">'Razor'</span></span>
- <span data-ttu-id="d1a34-578">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-579">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-580">'Blazor'</span></span>
- <span data-ttu-id="d1a34-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-581">'Identity'</span></span>
- <span data-ttu-id="d1a34-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-583">'Razor'</span></span>
- <span data-ttu-id="d1a34-584">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-584">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-585">-------------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-586">'Blazor'</span></span>
- <span data-ttu-id="d1a34-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-587">'Identity'</span></span>
- <span data-ttu-id="d1a34-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-589">'Razor'</span></span>
- <span data-ttu-id="d1a34-590">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-591">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-592">'Blazor'</span></span>
- <span data-ttu-id="d1a34-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-593">'Identity'</span></span>
- <span data-ttu-id="d1a34-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-595">'Razor'</span></span>
- <span data-ttu-id="d1a34-596">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-597">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-598">'Blazor'</span></span>
- <span data-ttu-id="d1a34-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-599">'Identity'</span></span>
- <span data-ttu-id="d1a34-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-601">'Razor'</span></span>
- <span data-ttu-id="d1a34-602">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-603">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-604">'Blazor'</span></span>
- <span data-ttu-id="d1a34-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-605">'Identity'</span></span>
- <span data-ttu-id="d1a34-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-607">'Razor'</span></span>
- <span data-ttu-id="d1a34-608">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-609">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-610">'Blazor'</span></span>
- <span data-ttu-id="d1a34-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-611">'Identity'</span></span>
- <span data-ttu-id="d1a34-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-613">'Razor'</span></span>
- <span data-ttu-id="d1a34-614">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-615">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-616">'Blazor'</span></span>
- <span data-ttu-id="d1a34-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-617">'Identity'</span></span>
- <span data-ttu-id="d1a34-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-619">'Razor'</span></span>
- <span data-ttu-id="d1a34-620">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-621">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-622">'Blazor'</span></span>
- <span data-ttu-id="d1a34-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-623">'Identity'</span></span>
- <span data-ttu-id="d1a34-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-625">'Razor'</span></span>
- <span data-ttu-id="d1a34-626">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-627">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-628">'Blazor'</span></span>
- <span data-ttu-id="d1a34-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-629">'Identity'</span></span>
- <span data-ttu-id="d1a34-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-631">'Razor'</span></span>
- <span data-ttu-id="d1a34-632">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-633">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-634">'Blazor'</span></span>
- <span data-ttu-id="d1a34-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-635">'Identity'</span></span>
- <span data-ttu-id="d1a34-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-637">'Razor'</span></span>
- <span data-ttu-id="d1a34-638">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-639">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-640">'Blazor'</span></span>
- <span data-ttu-id="d1a34-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-641">'Identity'</span></span>
- <span data-ttu-id="d1a34-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-643">'Razor'</span></span>
- <span data-ttu-id="d1a34-644">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-645">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-646">'Blazor'</span></span>
- <span data-ttu-id="d1a34-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-647">'Identity'</span></span>
- <span data-ttu-id="d1a34-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-649">'Razor'</span></span>
- <span data-ttu-id="d1a34-650">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-651">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-652">'Blazor'</span></span>
- <span data-ttu-id="d1a34-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-653">'Identity'</span></span>
- <span data-ttu-id="d1a34-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-655">'Razor'</span></span>
- <span data-ttu-id="d1a34-656">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-657">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-658">'Blazor'</span></span>
- <span data-ttu-id="d1a34-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-659">'Identity'</span></span>
- <span data-ttu-id="d1a34-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-661">'Razor'</span></span>
- <span data-ttu-id="d1a34-662">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-663">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-664">'Blazor'</span></span>
- <span data-ttu-id="d1a34-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-665">'Identity'</span></span>
- <span data-ttu-id="d1a34-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-667">'Razor'</span></span>
- <span data-ttu-id="d1a34-668">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-669">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-670">'Blazor'</span></span>
- <span data-ttu-id="d1a34-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-671">'Identity'</span></span>
- <span data-ttu-id="d1a34-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-673">'Razor'</span></span>
- <span data-ttu-id="d1a34-674">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-675">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-676">'Blazor'</span></span>
- <span data-ttu-id="d1a34-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-677">'Identity'</span></span>
- <span data-ttu-id="d1a34-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-679">'Razor'</span></span>
- <span data-ttu-id="d1a34-680">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-681">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-682">'Blazor'</span></span>
- <span data-ttu-id="d1a34-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-683">'Identity'</span></span>
- <span data-ttu-id="d1a34-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-685">'Razor'</span></span>
- <span data-ttu-id="d1a34-686">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-687">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-688">'Blazor'</span></span>
- <span data-ttu-id="d1a34-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-689">'Identity'</span></span>
- <span data-ttu-id="d1a34-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-691">'Razor'</span></span>
- <span data-ttu-id="d1a34-692">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-693">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-694">'Blazor'</span></span>
- <span data-ttu-id="d1a34-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-695">'Identity'</span></span>
- <span data-ttu-id="d1a34-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-697">'Razor'</span></span>
- <span data-ttu-id="d1a34-698">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-699">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-700">'Blazor'</span></span>
- <span data-ttu-id="d1a34-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-701">'Identity'</span></span>
- <span data-ttu-id="d1a34-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-703">'Razor'</span></span>
- <span data-ttu-id="d1a34-704">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-705">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-706">'Blazor'</span></span>
- <span data-ttu-id="d1a34-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-707">'Identity'</span></span>
- <span data-ttu-id="d1a34-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-709">'Razor'</span></span>
- <span data-ttu-id="d1a34-710">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-711">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-712">'Blazor'</span></span>
- <span data-ttu-id="d1a34-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-713">'Identity'</span></span>
- <span data-ttu-id="d1a34-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-715">'Razor'</span></span>
- <span data-ttu-id="d1a34-716">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-717">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-718">'Blazor'</span></span>
- <span data-ttu-id="d1a34-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-719">'Identity'</span></span>
- <span data-ttu-id="d1a34-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-721">'Razor'</span></span>
- <span data-ttu-id="d1a34-722">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-723">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-724">'Blazor'</span></span>
- <span data-ttu-id="d1a34-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-725">'Identity'</span></span>
- <span data-ttu-id="d1a34-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-727">'Razor'</span></span>
- <span data-ttu-id="d1a34-728">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-729">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-730">'Blazor'</span></span>
- <span data-ttu-id="d1a34-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-731">'Identity'</span></span>
- <span data-ttu-id="d1a34-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-733">'Razor'</span></span>
- <span data-ttu-id="d1a34-734">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-735">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-736">'Blazor'</span></span>
- <span data-ttu-id="d1a34-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-737">'Identity'</span></span>
- <span data-ttu-id="d1a34-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-739">'Razor'</span></span>
- <span data-ttu-id="d1a34-740">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-741">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-742">'Blazor'</span></span>
- <span data-ttu-id="d1a34-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-743">'Identity'</span></span>
- <span data-ttu-id="d1a34-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-745">'Razor'</span></span>
- <span data-ttu-id="d1a34-746">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-747">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-748">'Blazor'</span></span>
- <span data-ttu-id="d1a34-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-749">'Identity'</span></span>
- <span data-ttu-id="d1a34-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-751">'Razor'</span></span>
- <span data-ttu-id="d1a34-752">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-753">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-754">'Blazor'</span></span>
- <span data-ttu-id="d1a34-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-755">'Identity'</span></span>
- <span data-ttu-id="d1a34-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-757">'Razor'</span></span>
- <span data-ttu-id="d1a34-758">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-759">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-760">'Blazor'</span></span>
- <span data-ttu-id="d1a34-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-761">'Identity'</span></span>
- <span data-ttu-id="d1a34-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-763">'Razor'</span></span>
- <span data-ttu-id="d1a34-764">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-765">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-766">'Blazor'</span></span>
- <span data-ttu-id="d1a34-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-767">'Identity'</span></span>
- <span data-ttu-id="d1a34-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-769">'Razor'</span></span>
- <span data-ttu-id="d1a34-770">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-771">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-772">'Blazor'</span></span>
- <span data-ttu-id="d1a34-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-773">'Identity'</span></span>
- <span data-ttu-id="d1a34-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-775">'Razor'</span></span>
- <span data-ttu-id="d1a34-776">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-777">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-778">'Blazor'</span></span>
- <span data-ttu-id="d1a34-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-779">'Identity'</span></span>
- <span data-ttu-id="d1a34-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-781">'Razor'</span></span>
- <span data-ttu-id="d1a34-782">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-783">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-784">'Blazor'</span></span>
- <span data-ttu-id="d1a34-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-785">'Identity'</span></span>
- <span data-ttu-id="d1a34-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-787">'Razor'</span></span>
- <span data-ttu-id="d1a34-788">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-789">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-790">'Blazor'</span></span>
- <span data-ttu-id="d1a34-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-791">'Identity'</span></span>
- <span data-ttu-id="d1a34-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-793">'Razor'</span></span>
- <span data-ttu-id="d1a34-794">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-795">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-796">'Blazor'</span></span>
- <span data-ttu-id="d1a34-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-797">'Identity'</span></span>
- <span data-ttu-id="d1a34-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-799">'Razor'</span></span>
- <span data-ttu-id="d1a34-800">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-801">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-802">'Blazor'</span></span>
- <span data-ttu-id="d1a34-803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-803">'Identity'</span></span>
- <span data-ttu-id="d1a34-804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-804">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-805">'Razor'</span></span>
- <span data-ttu-id="d1a34-806">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-806">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Voce di configurazione non creata.</span><span class="sxs-lookup"><span data-stu-id="d1a34-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="d1a34-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Chiave: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="d1a34-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d1a34-809">Valore: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`   |  `ConnectionStrings:{KEY}`   | Chiave: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="d1a34-809">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d1a34-810">Valore: `System.Data.SqlClient` | | `SQLCONNSTR_{KEY}`        |  `ConnectionStrings:{KEY}`   | Chiave: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="d1a34-810">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d1a34-811">Valore`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="d1a34-811">Value: `System.Data.SqlClient`  |</span></span>

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="d1a34-812">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="d1a34-812">JSON configuration provider</span></span>

<span data-ttu-id="d1a34-813"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Carica la configurazione dalle coppie chiave-valore del file JSON.</span><span class="sxs-lookup"><span data-stu-id="d1a34-813">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="d1a34-814">Gli overload possono specificare:</span><span class="sxs-lookup"><span data-stu-id="d1a34-814">Overloads can specify:</span></span>

* <span data-ttu-id="d1a34-815">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-815">Whether the file is optional.</span></span>
* <span data-ttu-id="d1a34-816">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="d1a34-816">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="d1a34-817">Esaminare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="d1a34-817">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="d1a34-818">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="d1a34-818">The preceding code:</span></span>

* <span data-ttu-id="d1a34-819">Configura il provider di configurazione JSON per caricare il file *config. JSON* con le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-819">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="d1a34-820">`optional: true`: Il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-820">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="d1a34-821">`reloadOnChange: true`: Il file viene ricaricato quando vengono salvate le modifiche.</span><span class="sxs-lookup"><span data-stu-id="d1a34-821">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="d1a34-822">Legge i [provider di configurazione predefiniti](#default) prima del file *config. JSON* .</span><span class="sxs-lookup"><span data-stu-id="d1a34-822">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="d1a34-823">Impostazioni nell'impostazione di sostituzione del file *config. JSON* nei provider di configurazione predefiniti, tra cui il [provider di configurazione delle variabili di ambiente](#evcp) e il provider di configurazione della riga di [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="d1a34-823">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d1a34-824">In genere ***non*** si vuole che un file JSON personalizzato esegua l'override dei valori impostati nel [provider di configurazione delle variabili di ambiente](#evcp) e nel provider di configurazione della riga di [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="d1a34-824">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d1a34-825">Il codice seguente cancella tutti i provider di configurazione e aggiunge diversi provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-825">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="d1a34-826">Nel codice precedente, Settings in *config. JSON* e *config*. `Environment` . file *JSON* :</span><span class="sxs-lookup"><span data-stu-id="d1a34-826">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="d1a34-827">Eseguire l'override delle impostazioni nel file *appSettings. JSON* e *appSettings*. `Environment` . file *JSON* .</span><span class="sxs-lookup"><span data-stu-id="d1a34-827">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="d1a34-828">Viene sottoposto a override dalle impostazioni del [provider di configurazione delle variabili di ambiente](#evcp) e del provider di configurazione della riga di [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="d1a34-828">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d1a34-829">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il file *config. JSON* seguente:</span><span class="sxs-lookup"><span data-stu-id="d1a34-829">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="d1a34-830">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-830">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="d1a34-831">Provider di configurazione file</span><span class="sxs-lookup"><span data-stu-id="d1a34-831">File configuration provider</span></span>

<span data-ttu-id="d1a34-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> è la classe base per il caricamento della configurazione dal file system.</span><span class="sxs-lookup"><span data-stu-id="d1a34-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="d1a34-833">I provider di configurazione seguenti derivano da `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="d1a34-833">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="d1a34-834">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="d1a34-834">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="d1a34-835">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="d1a34-835">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="d1a34-836">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="d1a34-836">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="d1a34-837">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="d1a34-837">INI configuration provider</span></span>

<span data-ttu-id="d1a34-838"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carica la configurazione da coppie chiave-valore di file INI in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-838">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="d1a34-839">Il codice seguente cancella tutti i provider di configurazione e aggiunge diversi provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-839">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="d1a34-840">Nel codice precedente, le impostazioni in *MyIniConfig. ini* e *MyIniConfig*. `Environment` . i file *ini* vengono sottoposti a override dalle impostazioni nel:</span><span class="sxs-lookup"><span data-stu-id="d1a34-840">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="d1a34-841">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="d1a34-841">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="d1a34-842">[Provider di configurazione della riga di comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="d1a34-842">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d1a34-843">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il seguente file *MyIniConfig. ini* :</span><span class="sxs-lookup"><span data-stu-id="d1a34-843">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="d1a34-844">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-844">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="d1a34-845">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="d1a34-845">XML configuration provider</span></span>

<span data-ttu-id="d1a34-846">Il <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carica la configurazione da coppie chiave-valore di file XML in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-846">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="d1a34-847">Il codice seguente cancella tutti i provider di configurazione e aggiunge diversi provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-847">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="d1a34-848">Nel codice precedente, le impostazioni in *MyXMLFile. XML* e *MyXMLFile*. `Environment` . i file *XML* vengono sottoposti a override dalle impostazioni in:</span><span class="sxs-lookup"><span data-stu-id="d1a34-848">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="d1a34-849">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="d1a34-849">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="d1a34-850">[Provider di configurazione della riga di comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="d1a34-850">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d1a34-851">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il seguente file *MyXMLFile. XML* :</span><span class="sxs-lookup"><span data-stu-id="d1a34-851">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="d1a34-852">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-852">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d1a34-853">La ripetizione di elementi che usano lo stesso nome di elemento funziona se si usa l'attributo `name` per distinguere gli elementi:</span><span class="sxs-lookup"><span data-stu-id="d1a34-853">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="d1a34-854">Il codice seguente legge il file di configurazione precedente e visualizza le chiavi e i valori:</span><span class="sxs-lookup"><span data-stu-id="d1a34-854">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="d1a34-855">È possibile usare attributi per fornire valori:</span><span class="sxs-lookup"><span data-stu-id="d1a34-855">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="d1a34-856">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="d1a34-856">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d1a34-857">key:attribute</span><span class="sxs-lookup"><span data-stu-id="d1a34-857">key:attribute</span></span>
* <span data-ttu-id="d1a34-858">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="d1a34-858">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="d1a34-859">Provider di configurazione chiave per file</span><span class="sxs-lookup"><span data-stu-id="d1a34-859">Key-per-file configuration provider</span></span>

<span data-ttu-id="d1a34-860">Il <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa i file di una directory come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-860">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="d1a34-861">La chiave è il nome del file.</span><span class="sxs-lookup"><span data-stu-id="d1a34-861">The key is the file name.</span></span> <span data-ttu-id="d1a34-862">Il valore contiene il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="d1a34-862">The value contains the file's contents.</span></span> <span data-ttu-id="d1a34-863">Il provider di configurazione chiave per file viene usato negli scenari di hosting di Docker.</span><span class="sxs-lookup"><span data-stu-id="d1a34-863">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="d1a34-864">Per attivare la configurazione chiave-per-file, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-864">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="d1a34-865">Il `directoryPath` per i file deve essere un percorso assoluto.</span><span class="sxs-lookup"><span data-stu-id="d1a34-865">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="d1a34-866">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="d1a34-866">Overloads permit specifying:</span></span>

* <span data-ttu-id="d1a34-867">Un delegato `Action<KeyPerFileConfigurationSource>` che configura l'origine.</span><span class="sxs-lookup"><span data-stu-id="d1a34-867">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="d1a34-868">Se la directory è facoltativa e il percorso della directory.</span><span class="sxs-lookup"><span data-stu-id="d1a34-868">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="d1a34-869">Il doppio carattere di sottolineatura (`__`) viene usato come delimitatore per le chiavi di configurazione nei nomi dei file.</span><span class="sxs-lookup"><span data-stu-id="d1a34-869">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="d1a34-870">Ad esempio, il nome di file `Logging__LogLevel__System` produce la chiave di configurazione `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-870">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="d1a34-871">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="d1a34-871">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="d1a34-872">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="d1a34-872">Memory configuration provider</span></span>

<span data-ttu-id="d1a34-873">Il <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una raccolta in memoria come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-873">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="d1a34-874">Il codice seguente aggiunge una raccolta di memoria al sistema di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-874">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="d1a34-875">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza le impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-875">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d1a34-876">Nel codice precedente, `config.AddInMemoryCollection(Dict)` viene aggiunto dopo i [provider di configurazione predefiniti](#default).</span><span class="sxs-lookup"><span data-stu-id="d1a34-876">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="d1a34-877">Per un esempio di ordinamento dei provider di configurazione, vedere [provider di configurazione JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="d1a34-877">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="d1a34-878">Vedere [associare una matrice](#boa) per un altro esempio di utilizzo di `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="d1a34-878">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="d1a34-879">GetValue</span><span class="sxs-lookup"><span data-stu-id="d1a34-879">GetValue</span></span>

<span data-ttu-id="d1a34-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)estrae un singolo valore dalla configurazione con una chiave specificata e lo converte nel tipo specificato:</span><span class="sxs-lookup"><span data-stu-id="d1a34-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="d1a34-881">Nel codice precedente, se `NumberKey` non è stato trovato nella configurazione, viene usato il valore predefinito di `99` .</span><span class="sxs-lookup"><span data-stu-id="d1a34-881">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="d1a34-882">GetSection, GetChildren ed Exists</span><span class="sxs-lookup"><span data-stu-id="d1a34-882">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="d1a34-883">Per gli esempi che seguono, prendere in considerazione il seguente file *MySubsection. JSON* :</span><span class="sxs-lookup"><span data-stu-id="d1a34-883">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="d1a34-884">Il codice seguente aggiunge *MySubsection. JSON* ai provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-884">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="d1a34-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="d1a34-885">GetSection</span></span>

<span data-ttu-id="d1a34-886">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) restituisce una sottosezione di configurazione con la chiave della sottosezione specificata.</span><span class="sxs-lookup"><span data-stu-id="d1a34-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="d1a34-887">Il codice seguente restituisce i valori per `section1` :</span><span class="sxs-lookup"><span data-stu-id="d1a34-887">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="d1a34-888">Il codice seguente restituisce i valori per `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="d1a34-888">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="d1a34-889">`GetSection` non restituisce mai `null`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-889">`GetSection` never returns `null`.</span></span> <span data-ttu-id="d1a34-890">Se non viene trovata una sezione corrispondente, viene restituita una `IConfigurationSection` vuota.</span><span class="sxs-lookup"><span data-stu-id="d1a34-890">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="d1a34-891">Quando `GetSection` restituisce una sezione corrispondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> non viene compilata.</span><span class="sxs-lookup"><span data-stu-id="d1a34-891">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="d1a34-892">Quando la sezione esiste, vengono restituiti <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-892">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="d1a34-893">GetChildren ed EXISTS</span><span class="sxs-lookup"><span data-stu-id="d1a34-893">GetChildren and Exists</span></span>

<span data-ttu-id="d1a34-894">Il codice seguente chiama [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) e restituisce i valori per `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="d1a34-894">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="d1a34-895">Il codice precedente chiama [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) per verificare che la sezione esista:</span><span class="sxs-lookup"><span data-stu-id="d1a34-895">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="d1a34-896">Associare una matrice</span><span class="sxs-lookup"><span data-stu-id="d1a34-896">Bind an array</span></span>

<span data-ttu-id="d1a34-897">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supporta l'associazione di matrici a oggetti usando gli indici di matrice nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-897">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d1a34-898">Qualsiasi formato di matrice che espone un segmento di chiave numerica è in grado di associare array a una matrice di classi [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="d1a34-898">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="d1a34-899">Si consideri il file con *estensione JSON* dal [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="d1a34-899">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="d1a34-900">Il codice seguente aggiunge il file *Array. JSON* ai provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-900">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="d1a34-901">Il codice seguente legge la configurazione e Visualizza i valori:</span><span class="sxs-lookup"><span data-stu-id="d1a34-901">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="d1a34-902">Il codice precedente restituisce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="d1a34-902">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="d1a34-903">Nell'output precedente, index 3 ha un valore `value40` , corrispondente a `"4": "value40",` in *ArrayList. JSON*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-903">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="d1a34-904">Gli indici di matrice associati sono continui e non sono associati all'indice della chiave di configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-904">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="d1a34-905">Il binder di configurazione non è in grado di associare valori null o di creare voci null negli oggetti associati</span><span class="sxs-lookup"><span data-stu-id="d1a34-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="d1a34-906">Il codice seguente carica la `array:entries` configurazione con il <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-906">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="d1a34-907">Il codice seguente legge la configurazione in `arrayDict` `Dictionary` e Visualizza i valori:</span><span class="sxs-lookup"><span data-stu-id="d1a34-907">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="d1a34-908">Il codice precedente restituisce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="d1a34-908">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="d1a34-909">L'indice &num;3 nell'oggetto associato contiene i dati di configurazione per la chiave di configurazione `array:4` e il relativo valore `value4`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-909">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="d1a34-910">Quando i dati di configurazione contenenti una matrice sono associati, gli indici di matrice nelle chiavi di configurazione vengono usati per scorrere i dati di configurazione durante la creazione dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="d1a34-910">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="d1a34-911">Un valore null non può essere mantenuto nei dati di configurazione e una voce con valore null non viene creata in un oggetto associato quando una matrice nelle chiavi di configurazione ignora uno o più indici.</span><span class="sxs-lookup"><span data-stu-id="d1a34-911">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="d1a34-912">È possibile specificare l'elemento di configurazione mancante per &num; l'indice 3 prima di eseguire il binding all' `ArrayExample` istanza da qualsiasi provider di configurazione che legga la &num; coppia chiave/valore dell'indice 3.</span><span class="sxs-lookup"><span data-stu-id="d1a34-912">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="d1a34-913">Si consideri il seguente file *valore3. JSON* dal Download di esempio:</span><span class="sxs-lookup"><span data-stu-id="d1a34-913">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="d1a34-914">Il codice seguente include la configurazione per *valore3. JSON* e `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="d1a34-914">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="d1a34-915">Il codice seguente legge la configurazione precedente e Visualizza i valori:</span><span class="sxs-lookup"><span data-stu-id="d1a34-915">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="d1a34-916">Il codice precedente restituisce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="d1a34-916">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="d1a34-917">I provider di configurazione personalizzati non devono implementare l'associazione di matrici.</span><span class="sxs-lookup"><span data-stu-id="d1a34-917">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="d1a34-918">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="d1a34-918">Custom configuration provider</span></span>

<span data-ttu-id="d1a34-919">L'app di esempio dimostra come creare un provider di configurazione di base che legge le coppie chiave-valore di configurazione da un database usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="d1a34-919">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="d1a34-920">Il provider ha le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-920">The provider has the following characteristics:</span></span>

* <span data-ttu-id="d1a34-921">Il database in memoria di Entity Framework viene usato a scopo dimostrativo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-921">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="d1a34-922">Per usare un database che richiede una stringa di connessione, implementare un `ConfigurationBuilder` secondario per fornire la stringa di connessione da un altro provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-922">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="d1a34-923">Il provider legge una tabella di database in una configurazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="d1a34-923">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="d1a34-924">Il provider non esegue una query sul database per ogni chiave.</span><span class="sxs-lookup"><span data-stu-id="d1a34-924">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="d1a34-925">Il ricaricamento in caso di modifica non è implementato, quindi l'aggiornamento del database dopo l'avvio dell'app non ha alcun effetto sulla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-925">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="d1a34-926">Definire un'entità `EFConfigurationValue` per l'archiviazione dei valori di configurazione nel database.</span><span class="sxs-lookup"><span data-stu-id="d1a34-926">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="d1a34-927">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="d1a34-927">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="d1a34-928">Aggiungere `EFConfigurationContext` per archiviare i valori configurati e accedervi.</span><span class="sxs-lookup"><span data-stu-id="d1a34-928">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="d1a34-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="d1a34-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="d1a34-930">Creare una classe che implementi <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-930">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="d1a34-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="d1a34-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="d1a34-932">Creare il provider di configurazione personalizzato ereditando da <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-932">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="d1a34-933">Il provider di configurazione inizializza il database quando è vuoto.</span><span class="sxs-lookup"><span data-stu-id="d1a34-933">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="d1a34-934">Poiché le [chiavi di configurazione non fanno distinzione tra maiuscole](#keys)e minuscole, il dizionario utilizzato per inizializzare il database viene creato con l'operatore di confronto senza distinzione tra maiuscole e minuscole ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="d1a34-934">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="d1a34-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="d1a34-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="d1a34-936">Un metodo di estensione `AddEFConfiguration` consente di aggiungere l'origine di configurazione a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-936">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="d1a34-937">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="d1a34-937">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="d1a34-938">L'esempio di codice seguente mostra come usare il `EFConfigurationProvider` personalizzato in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d1a34-938">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="d1a34-939">Accedi alla configurazione all'avvio</span><span class="sxs-lookup"><span data-stu-id="d1a34-939">Access configuration in Startup</span></span>

<span data-ttu-id="d1a34-940">Il codice seguente consente di visualizzare i dati di configurazione nei `Startup` metodi:</span><span class="sxs-lookup"><span data-stu-id="d1a34-940">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="d1a34-941">Per un esempio di accesso alla configurazione usando metodi di servizio di avvio, vedere [Avvio dell'applicazione: Metodi pratici](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="d1a34-941">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="d1a34-942">Accedi alla configurazione nelle Razor pagine</span><span class="sxs-lookup"><span data-stu-id="d1a34-942">Access configuration in Razor Pages</span></span>

<span data-ttu-id="d1a34-943">Il codice seguente consente di visualizzare i dati di configurazione in una Razor pagina:</span><span class="sxs-lookup"><span data-stu-id="d1a34-943">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="d1a34-944">Nel codice seguente, `MyOptions` viene aggiunto al contenitore del servizio con <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associato alla configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-944">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="d1a34-945">Il markup seguente utilizza la [`@inject`](xref:mvc/views/razor#inject) Razor direttiva per risolvere e visualizzare i valori delle opzioni:</span><span class="sxs-lookup"><span data-stu-id="d1a34-945">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="d1a34-946">Accedere alla configurazione in un file di visualizzazione MVC</span><span class="sxs-lookup"><span data-stu-id="d1a34-946">Access configuration in a MVC view file</span></span>

<span data-ttu-id="d1a34-947">Il codice seguente consente di visualizzare i dati di configurazione in una visualizzazione MVC:</span><span class="sxs-lookup"><span data-stu-id="d1a34-947">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="d1a34-948">Configurare le opzioni con un delegato</span><span class="sxs-lookup"><span data-stu-id="d1a34-948">Configure options with a delegate</span></span>

<span data-ttu-id="d1a34-949">Le opzioni configurate in un delegato eseguono l'override dei valori impostati nei provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-949">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="d1a34-950">La configurazione delle opzioni con un delegato è illustrata nell'esempio 2 nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="d1a34-950">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="d1a34-951">Nel codice seguente <xref:Microsoft.Extensions.Options.IConfigureOptions%601> viene aggiunto un servizio al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="d1a34-951">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="d1a34-952">Usa un delegato per configurare i valori per `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="d1a34-952">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="d1a34-953">Il codice seguente consente di visualizzare i valori delle opzioni:</span><span class="sxs-lookup"><span data-stu-id="d1a34-953">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="d1a34-954">Nell'esempio precedente, i valori di `Option1` e `Option2` vengono specificati in *appSettings. JSON* e quindi sottoposti a override dal delegato configurato.</span><span class="sxs-lookup"><span data-stu-id="d1a34-954">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="d1a34-955">Host e configurazione delle app</span><span class="sxs-lookup"><span data-stu-id="d1a34-955">Host versus app configuration</span></span>

<span data-ttu-id="d1a34-956">Prima che l'app venga configurata e avviata, viene configurato e avviato un *host*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-956">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="d1a34-957">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-957">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="d1a34-958">Sia l'app che l'host vengono configurati tramite i provider di configurazione descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="d1a34-958">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="d1a34-959">Anche le coppie chiave-valore di configurazione dell'host sono incluse nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-959">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="d1a34-960">Per altre informazioni su come vengono usati i provider di configurazione quando viene creato l'host e sugli effetti delle origini di configurazione sull'host di configurazione, vedere <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-960">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="d1a34-961">Configurazione host predefinita</span><span class="sxs-lookup"><span data-stu-id="d1a34-961">Default host configuration</span></span>

<span data-ttu-id="d1a34-962">Per informazioni dettagliate sulla configurazione predefinita quando viene usato l'[host Web](xref:fundamentals/host/web-host), vedere la [versione di questo argomento per ASP.NET Core 2.2](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="d1a34-962">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="d1a34-963">La configurazione dell'host viene specificata da:</span><span class="sxs-lookup"><span data-stu-id="d1a34-963">Host configuration is provided from:</span></span>
  * <span data-ttu-id="d1a34-964">Variabili di ambiente con prefisso `DOTNET_` (ad esempio, `DOTNET_ENVIRONMENT` ) utilizzando il [provider di configurazione delle variabili di ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d1a34-964">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="d1a34-965">Il prefisso (`DOTNET_`) viene rimosso al caricamento delle coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-965">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="d1a34-966">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d1a34-966">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="d1a34-967">La configurazione predefinita dell'host Web viene stabilita (`ConfigureWebHostDefaults`) nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="d1a34-967">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="d1a34-968">Kestrel viene usato come server Web e configurato con i provider di configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-968">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="d1a34-969">Aggiungere il middleware di filtro host.</span><span class="sxs-lookup"><span data-stu-id="d1a34-969">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="d1a34-970">Aggiungere il middleware delle intestazioni inoltrate se la variabile di ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è impostata su `true`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-970">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="d1a34-971">Abilitare l'integrazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="d1a34-971">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="d1a34-972">Altra configurazione</span><span class="sxs-lookup"><span data-stu-id="d1a34-972">Other configuration</span></span>

<span data-ttu-id="d1a34-973">Questo argomento riguarda solo la *configurazione dell'app*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-973">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="d1a34-974">Altri aspetti dell'esecuzione e dell'hosting di app ASP.NET Core sono configurati usando i file di configurazione non trattati in questo argomento:</span><span class="sxs-lookup"><span data-stu-id="d1a34-974">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="d1a34-975">*Launch. JSON* / *launchSettings. JSON* sono i file di configurazione degli strumenti per l'ambiente di sviluppo, descritti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-975">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="d1a34-976">In <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="d1a34-976">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="d1a34-977">Nella documentazione in cui vengono usati i file per configurare ASP.NET Core app per scenari di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-977">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="d1a34-978">*Web. config* è un file di configurazione del server, descritto negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-978">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="d1a34-979">Per ulteriori informazioni sulla migrazione della configurazione dell'app da versioni precedenti di ASP.NET, vedere <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="d1a34-979">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="d1a34-980">Aggiungere la configurazione da un assembly esterno</span><span class="sxs-lookup"><span data-stu-id="d1a34-980">Add configuration from an external assembly</span></span>

<span data-ttu-id="d1a34-981">Un'implementazione <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> consente l'aggiunta di miglioramenti a un'app all'avvio, da un assembly esterno alla classe `Startup` dell'app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-981">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="d1a34-982">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-982">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d1a34-983">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d1a34-983">Additional resources</span></span>

* [<span data-ttu-id="d1a34-984">Codice sorgente configurazione</span><span class="sxs-lookup"><span data-stu-id="d1a34-984">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d1a34-985">La configurazione delle app in ASP.NET Core si basa su coppie chiave-valore stabilite dai *provider di configurazione*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-985">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="d1a34-986">I provider di configurazione leggono i dati di configurazione in coppie chiave-valore da un'ampia gamma di origini di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-986">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="d1a34-987">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="d1a34-987">Azure Key Vault</span></span>
* <span data-ttu-id="d1a34-988">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="d1a34-988">Azure App Configuration</span></span>
* <span data-ttu-id="d1a34-989">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="d1a34-989">Command-line arguments</span></span>
* <span data-ttu-id="d1a34-990">Provider personalizzati (installati o creati)</span><span class="sxs-lookup"><span data-stu-id="d1a34-990">Custom providers (installed or created)</span></span>
* <span data-ttu-id="d1a34-991">File della directory</span><span class="sxs-lookup"><span data-stu-id="d1a34-991">Directory files</span></span>
* <span data-ttu-id="d1a34-992">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="d1a34-992">Environment variables</span></span>
* <span data-ttu-id="d1a34-993">Oggetti .NET in memoria</span><span class="sxs-lookup"><span data-stu-id="d1a34-993">In-memory .NET objects</span></span>
* <span data-ttu-id="d1a34-994">File di impostazioni</span><span class="sxs-lookup"><span data-stu-id="d1a34-994">Settings files</span></span>

<span data-ttu-id="d1a34-995">I pacchetti di configurazione per gli scenari di provider di configurazione comuni ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sono inclusi nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="d1a34-995">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d1a34-996">Gli esempi di codice che seguono e quelli inclusi nell'app di esempio usano lo spazio dei nomi <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="d1a34-996">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="d1a34-997">Il *modello di opzioni* è un'estensione dei concetti di configurazione descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="d1a34-997">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="d1a34-998">Le opzioni usano le classi per rappresentare i gruppi di impostazioni correlate.</span><span class="sxs-lookup"><span data-stu-id="d1a34-998">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="d1a34-999">Per altre informazioni, vedere <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-999">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="d1a34-1000">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d1a34-1000">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="d1a34-1001">Host e configurazione delle app</span><span class="sxs-lookup"><span data-stu-id="d1a34-1001">Host versus app configuration</span></span>

<span data-ttu-id="d1a34-1002">Prima che l'app venga configurata e avviata, viene configurato e avviato un *host*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1002">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="d1a34-1003">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1003">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="d1a34-1004">Sia l'app che l'host vengono configurati tramite i provider di configurazione descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1004">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="d1a34-1005">Anche le coppie chiave-valore di configurazione dell'host sono incluse nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1005">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="d1a34-1006">Per altre informazioni su come vengono usati i provider di configurazione quando viene creato l'host e sugli effetti delle origini di configurazione sull'host di configurazione, vedere <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1006">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="d1a34-1007">Altra configurazione</span><span class="sxs-lookup"><span data-stu-id="d1a34-1007">Other configuration</span></span>

<span data-ttu-id="d1a34-1008">Questo argomento riguarda solo la *configurazione dell'app*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1008">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="d1a34-1009">Altri aspetti dell'esecuzione e dell'hosting di app ASP.NET Core sono configurati usando i file di configurazione non trattati in questo argomento:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1009">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="d1a34-1010">*Launch. JSON* / *launchSettings. JSON* sono i file di configurazione degli strumenti per l'ambiente di sviluppo, descritti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1010">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="d1a34-1011">In <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="d1a34-1011">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="d1a34-1012">Nella documentazione in cui vengono usati i file per configurare ASP.NET Core app per scenari di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1012">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="d1a34-1013">*Web. config* è un file di configurazione del server, descritto negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1013">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="d1a34-1014">Per ulteriori informazioni sulla migrazione della configurazione dell'app da versioni precedenti di ASP.NET, vedere <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="d1a34-1014">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="d1a34-1015">Configurazione predefinita</span><span class="sxs-lookup"><span data-stu-id="d1a34-1015">Default configuration</span></span>

<span data-ttu-id="d1a34-1016">Le app basate sui modelli [dotnet new](/dotnet/core/tools/dotnet-new) di ASP.NET Core chiamano <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> durante la compilazione di un host.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1016">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="d1a34-1017">`CreateDefaultBuilder` fornisce la configurazione predefinita per l'app nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1017">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="d1a34-1018">La configurazione seguente si applica alle app che usano l'[host Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1018">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="d1a34-1019">Per informazioni dettagliate sulla configurazione predefinita quando viene usato l'[host generico](xref:fundamentals/host/generic-host), vedere la [versione più recente di questo argomento](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1019">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="d1a34-1020">La configurazione dell'host viene specificata da:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1020">Host configuration is provided from:</span></span>
  * <span data-ttu-id="d1a34-1021">Variabili di ambiente con prefisso `ASPNETCORE_` (ad esempio `ASPNETCORE_ENVIRONMENT`) che usano il [provider di configurazione delle variabili di ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1021">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="d1a34-1022">Il prefisso (`ASPNETCORE_`) viene rimosso al caricamento delle coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1022">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="d1a34-1023">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1023">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="d1a34-1024">La configurazione dell'app viene fornita da:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1024">App configuration is provided from:</span></span>
  * <span data-ttu-id="d1a34-1025">*appsettings.json* mediante il [provider di configurazione dei file](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1025">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="d1a34-1026">*appsettings.{Ambiente}.json* mediante il [provider di configurazione dei file](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1026">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="d1a34-1027">[Strumento di gestione dei segreti](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development` usando l'assembly di ingresso.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1027">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="d1a34-1028">Variabili di ambiente che usano il [provider di configurazione delle variabili di ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1028">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="d1a34-1029">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1029">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="d1a34-1030">Sicurezza</span><span class="sxs-lookup"><span data-stu-id="d1a34-1030">Security</span></span>

<span data-ttu-id="d1a34-1031">Per proteggere i dati di configurazione sensibili, adottare le pratiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1031">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="d1a34-1032">Non archiviare mai la password o altri dati sensibili nel codice del provider di configurazione o in file di configurazione di testo normale.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1032">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="d1a34-1033">Non usare i segreti di produzione in ambienti di sviluppo o di test.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1033">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="d1a34-1034">Specificare i segreti all'esterno del progetto in modo che non possano essere inavvertitamente inviati a un repository del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1034">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="d1a34-1035">Per altre informazioni, vedere gli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1035">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="d1a34-1036"><xref:security/app-secrets>: Include consigli sull'uso delle variabili di ambiente per archiviare dati riservati.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1036"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="d1a34-1037">Secret Manager usa il provider di configurazione dei file per archiviare i segreti utente in un file JSON nel sistema locale.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1037">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="d1a34-1038">Il provider di configurazione dei file è descritto più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1038">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="d1a34-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) archivia in modo sicuro i segreti delle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="d1a34-1040">Per altre informazioni, vedere <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1040">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="d1a34-1041">Dati di configurazione gerarchici</span><span class="sxs-lookup"><span data-stu-id="d1a34-1041">Hierarchical configuration data</span></span>

<span data-ttu-id="d1a34-1042">L'API di configurazione è in grado di mantenere i dati di configurazione gerarchici rendendoli flat grazie all'uso di un delimitatore nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1042">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="d1a34-1043">Nel file JSON seguente, esistono quattro chiavi in una gerarchia strutturata di due sezioni:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1043">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="d1a34-1044">Quando il file viene letto nella configurazione, vengono create chiavi univoche per mantenere la struttura di dati gerarchici originale dell'origine di configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1044">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="d1a34-1045">Le sezioni e le chiavi vengono rese flat usando due punti (`:`) per mantenere la struttura originale:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1045">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="d1a34-1046">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d1a34-1046">section0:key0</span></span>
* <span data-ttu-id="d1a34-1047">section0:key1</span><span class="sxs-lookup"><span data-stu-id="d1a34-1047">section0:key1</span></span>
* <span data-ttu-id="d1a34-1048">section1:key0</span><span class="sxs-lookup"><span data-stu-id="d1a34-1048">section1:key0</span></span>
* <span data-ttu-id="d1a34-1049">section1:key1</span><span class="sxs-lookup"><span data-stu-id="d1a34-1049">section1:key1</span></span>

<span data-ttu-id="d1a34-1050">I metodi <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sono disponibili per isolare le sezioni e gli elementi figlio di una sezione nei dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="d1a34-1051">Questi metodi sono descritti più avanti in [GetSection, GetChildren ed Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1051">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="d1a34-1052">Convenzioni</span><span class="sxs-lookup"><span data-stu-id="d1a34-1052">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="d1a34-1053">Origini e provider</span><span class="sxs-lookup"><span data-stu-id="d1a34-1053">Sources and providers</span></span>

<span data-ttu-id="d1a34-1054">All'avvio dell'app, le origini di configurazione vengono lette nell'ordine con cui sono specificati i rispettivi provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1054">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="d1a34-1055">I provider di configurazione che implementano il rilevamento delle modifiche sono in grado di ricaricare la configurazione quando viene modificata un'impostazione sottostante.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1055">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="d1a34-1056">Ad esempio, il provider di configurazione dei file (descritto più avanti in questo argomento) e il [provider di configurazione di Azure Key Vault](xref:security/key-vault-configuration) implementano il rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1056">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="d1a34-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> è disponibile nel contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) dell'app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d1a34-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration>può essere inserito in una Razor pagina <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> o MVC <xref:Microsoft.AspNetCore.Mvc.Controller> per ottenere la configurazione per la classe.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="d1a34-1059">Negli esempi seguenti il `_config` campo viene usato per accedere ai valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1059">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="d1a34-1060">I provider di configurazione non possono usare l'inserimento delle dipendenze, perché non è disponibile quando vengono configurati dall'host.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1060">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="d1a34-1061">Chiavi</span><span class="sxs-lookup"><span data-stu-id="d1a34-1061">Keys</span></span>

<span data-ttu-id="d1a34-1062">Le chiavi di configurazione adottano le convenzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1062">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="d1a34-1063">Per le chiavi non viene fatta distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1063">Keys are case-insensitive.</span></span> <span data-ttu-id="d1a34-1064">Ad esempio, `ConnectionString` e `connectionstring` vengono considerate chiavi equivalenti.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1064">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="d1a34-1065">Se un valore per la stessa chiave viene impostato dallo stesso provider di configurazione o da provider diversi, viene usato l'ultimo valore impostato per la chiave.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1065">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="d1a34-1066">Chiavi gerarchiche</span><span class="sxs-lookup"><span data-stu-id="d1a34-1066">Hierarchical keys</span></span>
  * <span data-ttu-id="d1a34-1067">Nell'ambito dell'API di configurazione, il separatore due punti (`:`) funziona in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1067">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="d1a34-1068">Nelle variabili di ambiente, un separatore due punti potrebbe non funzionare in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1068">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="d1a34-1069">Il doppio carattere di sottolineatura (`__`) è supportato da tutte le piattaforme e viene convertito automaticamente nei due punti.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1069">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="d1a34-1070">In Azure Key Vault, le chiavi gerarchiche usano `--` (due trattini) come separatore.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1070">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="d1a34-1071">Scrivere il codice per sostituire i trattini con i due punti quando i segreti vengono caricati nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1071">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="d1a34-1072">Il <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supporta l'associazione di matrici agli oggetti usando gli indici delle matrici nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1072">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d1a34-1073">L'associazione di matrici è descritta nella sezione [Associare una matrice a una classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1073">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="d1a34-1074">Valori</span><span class="sxs-lookup"><span data-stu-id="d1a34-1074">Values</span></span>

<span data-ttu-id="d1a34-1075">I valori di configurazione adottano le convenzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1075">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="d1a34-1076">I valori sono stringhe.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1076">Values are strings.</span></span>
* <span data-ttu-id="d1a34-1077">I valori null non possono essere archiviati nella configurazione o associati a oggetti.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1077">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="d1a34-1078">Providers</span><span class="sxs-lookup"><span data-stu-id="d1a34-1078">Providers</span></span>

<span data-ttu-id="d1a34-1079">La tabella seguente mostra i provider di configurazione disponibili per le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1079">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="d1a34-1080">Provider</span><span class="sxs-lookup"><span data-stu-id="d1a34-1080">Provider</span></span> | <span data-ttu-id="d1a34-1081">Fornisce la configurazione da&hellip;</span><span class="sxs-lookup"><span data-stu-id="d1a34-1081">Provides configuration from&hellip;</span></span> |
| ---
<span data-ttu-id="d1a34-1082">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1083">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1084">'Identity'</span></span>
- <span data-ttu-id="d1a34-1085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1085">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1086">'Razor'</span></span>
- <span data-ttu-id="d1a34-1087">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1088">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1089">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1090">'Identity'</span></span>
- <span data-ttu-id="d1a34-1091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1091">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1092">'Razor'</span></span>
- <span data-ttu-id="d1a34-1093">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1093">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-1094">---- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1095">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1096">'Identity'</span></span>
- <span data-ttu-id="d1a34-1097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1097">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1098">'Razor'</span></span>
- <span data-ttu-id="d1a34-1099">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1099">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1100">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1101">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1102">'Identity'</span></span>
- <span data-ttu-id="d1a34-1103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1103">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1104">'Razor'</span></span>
- <span data-ttu-id="d1a34-1105">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1106">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1107">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1108">'Identity'</span></span>
- <span data-ttu-id="d1a34-1109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1109">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1110">'Razor'</span></span>
- <span data-ttu-id="d1a34-1111">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1112">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1113">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1114">'Identity'</span></span>
- <span data-ttu-id="d1a34-1115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1115">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1116">'Razor'</span></span>
- <span data-ttu-id="d1a34-1117">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1118">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1119">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1120">'Identity'</span></span>
- <span data-ttu-id="d1a34-1121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1121">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1122">'Razor'</span></span>
- <span data-ttu-id="d1a34-1123">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1124">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1125">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1126">'Identity'</span></span>
- <span data-ttu-id="d1a34-1127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1127">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1128">'Razor'</span></span>
- <span data-ttu-id="d1a34-1129">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1130">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1131">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1132">'Identity'</span></span>
- <span data-ttu-id="d1a34-1133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1133">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1134">'Razor'</span></span>
- <span data-ttu-id="d1a34-1135">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1136">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1137">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1138">'Identity'</span></span>
- <span data-ttu-id="d1a34-1139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1139">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1140">'Razor'</span></span>
- <span data-ttu-id="d1a34-1141">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1141">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1142">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1143">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1143">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1144">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1144">'Identity'</span></span>
- <span data-ttu-id="d1a34-1145">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1145">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1146">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1146">'Razor'</span></span>
- <span data-ttu-id="d1a34-1147">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1147">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1148">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1149">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1150">'Identity'</span></span>
- <span data-ttu-id="d1a34-1151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1151">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1152">'Razor'</span></span>
- <span data-ttu-id="d1a34-1153">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1154">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1155">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1156">'Identity'</span></span>
- <span data-ttu-id="d1a34-1157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1157">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1158">'Razor'</span></span>
- <span data-ttu-id="d1a34-1159">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1160">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1161">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1162">'Identity'</span></span>
- <span data-ttu-id="d1a34-1163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1163">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1164">'Razor'</span></span>
- <span data-ttu-id="d1a34-1165">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1166">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1167">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1168">'Identity'</span></span>
- <span data-ttu-id="d1a34-1169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1169">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1170">'Razor'</span></span>
- <span data-ttu-id="d1a34-1171">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1172">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1173">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1173">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1174">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1174">'Identity'</span></span>
- <span data-ttu-id="d1a34-1175">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1175">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1176">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1176">'Razor'</span></span>
- <span data-ttu-id="d1a34-1177">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1177">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1178">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1179">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1179">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1180">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1180">'Identity'</span></span>
- <span data-ttu-id="d1a34-1181">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1181">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1182">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1182">'Razor'</span></span>
- <span data-ttu-id="d1a34-1183">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1183">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-1184">------------------ | | [Provider di configurazione Azure Key Vault](xref:security/key-vault-configuration) (argomenti*sulla sicurezza* ) | Azure Key Vault | | [Provider di configurazione app Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentazione di Azure) | Configurazione app Azure | | [Provider di configurazione della riga di comando](#command-line-configuration-provider) | Parametri della riga di comando | | [Provider di configurazione personalizzato](#custom-configuration-provider) | Origine personalizzata | | [Provider di configurazione delle variabili di ambiente](#environment-variables-configuration-provider) | Variabili di ambiente | | [Provider di configurazione file](#file-configuration-provider) | File (INI, JSON, XML) | | [Provider di configurazione chiave per file](#key-per-file-configuration-provider) | File di directory | | [Provider di configurazione della memoria](#memory-configuration-provider) | Raccolte in memoria | | [Segreti utente (gestione Secret)](xref:security/app-secrets) (argomenti*sulla sicurezza* ) | File nella directory del profilo utente |</span><span class="sxs-lookup"><span data-stu-id="d1a34-1184">------------------ | | [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics) | Azure Key Vault | | [Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation) | Azure App Configuration | | [Command-line Configuration Provider](#command-line-configuration-provider) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables Configuration Provider](#environment-variables-configuration-provider) | Environment variables | | [File Configuration Provider](#file-configuration-provider) | Files (INI, JSON, XML) | | [Key-per-file Configuration Provider](#key-per-file-configuration-provider) | Directory files | | [Memory Configuration Provider](#memory-configuration-provider) | In-memory collections | | [User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics) | File in the user profile directory |</span></span>

<span data-ttu-id="d1a34-1185">Le origini di configurazione vengono lette nell'ordine in cui vengono specificati i rispetti provider di configurazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1185">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="d1a34-1186">I provider di configurazione descritti in questo argomento sono descritti in ordine alfabetico, non nell'ordine in cui il codice li dispone.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1186">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="d1a34-1187">Ordinare i provider di configurazione nel codice in base alle priorità per le origini di configurazione sottostanti richieste dall'app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1187">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="d1a34-1188">Una sequenza tipica di provider di configurazione è:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1188">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="d1a34-1189">Files (*appSettings. JSON*, *appSettings. { Environment}. JSON*, dove `{Environment}` è l'ambiente host corrente dell'app)</span><span class="sxs-lookup"><span data-stu-id="d1a34-1189">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="d1a34-1190">Insieme di credenziali chiave Azure</span><span class="sxs-lookup"><span data-stu-id="d1a34-1190">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="d1a34-1191">[Segreti utente (Secret Manager)](xref:security/app-secrets) (solo nell'ambiente di sviluppo)</span><span class="sxs-lookup"><span data-stu-id="d1a34-1191">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="d1a34-1192">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="d1a34-1192">Environment variables</span></span>
1. <span data-ttu-id="d1a34-1193">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="d1a34-1193">Command-line arguments</span></span>

<span data-ttu-id="d1a34-1194">È pratica comune posizionare il provider di configurazione della riga di comando per ultimo in una serie di provider per consentire agli argomenti della riga di comando di sostituire la configurazione impostata da altri provider.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1194">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="d1a34-1195">La sequenza di provider precedente viene utilizzata quando viene inizializzato un nuovo generatore host con `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d1a34-1195">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d1a34-1196">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1196">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="d1a34-1197">Configurare il generatore di host con UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="d1a34-1197">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="d1a34-1198">Per configurare il generatore di host, chiamare <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> sul generatore di host con la configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1198">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="d1a34-1199">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="d1a34-1199">ConfigureAppConfiguration</span></span>

<span data-ttu-id="d1a34-1200">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare i provider di configurazione dell'app, oltre a quelli aggiunti automaticamente da `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1200">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="d1a34-1201">Sostituire la configurazione precedente con argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="d1a34-1201">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="d1a34-1202">Per fornire la configurazione dell'app che può essere sostituita con argomenti della riga di comando, chiamare `AddCommandLine` per ultimo:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1202">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="d1a34-1203">Rimuovere i provider aggiunti da CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="d1a34-1203">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="d1a34-1204">Per rimuovere i provider aggiunti da `CreateDefaultBuilder` , chiamare prima [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) in [IConfigurationBuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="d1a34-1204">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="d1a34-1205">Utilizzare la configurazione durante l'avvio dell'app</span><span class="sxs-lookup"><span data-stu-id="d1a34-1205">Consume configuration during app startup</span></span>

<span data-ttu-id="d1a34-1206">La configurazione fornita all'app in `ConfigureAppConfiguration` è disponibile durante l'avvio dell'app, incluso `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1206">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d1a34-1207">Per altre informazioni, vedere la sezione [Accedere alla configurazione durante l'avvio](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1207">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="d1a34-1208">Provider di configurazione della riga di comando</span><span class="sxs-lookup"><span data-stu-id="d1a34-1208">Command-line Configuration Provider</span></span>

<span data-ttu-id="d1a34-1209"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carica la configurazione da coppie chiave-valore di argomenti della riga di comando in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1209">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="d1a34-1210">Per attivare la configurazione della riga di comando, metodo di estensione <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> viene chiamato su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1210">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d1a34-1211">`AddCommandLine` viene chiamato automaticamente quando viene chiamato il metodo `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1211">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="d1a34-1212">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1212">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d1a34-1213">`CreateDefaultBuilder` carica anche:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1213">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="d1a34-1214">Una configurazione facoltativa dai file *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1214">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="d1a34-1215">[Segreti utente (Secret Manager)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1215">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d1a34-1216">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1216">Environment variables.</span></span>

<span data-ttu-id="d1a34-1217">`CreateDefaultBuilder` aggiunge il provider di configurazione della riga di comando per ultimo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1217">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="d1a34-1218">Gli argomenti della riga di comando passati in fase di esecuzione sostituiscono la configurazione impostata dagli altri provider.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1218">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="d1a34-1219">`CreateDefaultBuilder` opera durante la creazione dell'host.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1219">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="d1a34-1220">Pertanto, la configurazione della riga di comando attivata da `CreateDefaultBuilder` può influire sul modo in cui l'host viene configurato.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1220">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="d1a34-1221">Per le app basate sui modelli di ASP.NET Core, `AddCommandLine` è già stato chiamato da `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1221">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d1a34-1222">Per aggiungere altri provider di configurazione e mantenere la possibilità di sostituire la configurazione di tali provider con argomenti della riga di comando, chiamare i provider aggiuntivi dell'app in `ConfigureAppConfiguration` e quindi chiamare `AddCommandLine` per ultimo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1222">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="d1a34-1223">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="d1a34-1223">**Example**</span></span>

<span data-ttu-id="d1a34-1224">L'app di esempio consente di sfruttare il metodo di servizio statico `CreateDefaultBuilder` per creare l'host, che include una chiamata a <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1224">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="d1a34-1225">Aprire un prompt dei comandi nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1225">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="d1a34-1226">Fornire un argomento della riga di comando per il comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1226">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="d1a34-1227">Dopo che l'app è in esecuzione, aprire un browser per l'app all'indirizzo `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1227">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="d1a34-1228">Notare che l'output contiene la coppia chiave-valore per l'argomento della riga di comando di configurazione fornito per `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1228">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="d1a34-1229">Argomenti</span><span class="sxs-lookup"><span data-stu-id="d1a34-1229">Arguments</span></span>

<span data-ttu-id="d1a34-1230">Il valore deve seguire un segno di uguale (`=`) o la chiave deve avere un prefisso (`--` o `/`) quando il valore segue uno spazio.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1230">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="d1a34-1231">Il valore non è necessario se viene usato un segno di uguale, ad esempio `CommandLineKey=`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1231">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="d1a34-1232">Prefisso chiave</span><span class="sxs-lookup"><span data-stu-id="d1a34-1232">Key prefix</span></span>               | <span data-ttu-id="d1a34-1233">Esempio</span><span class="sxs-lookup"><span data-stu-id="d1a34-1233">Example</span></span>                                                |
| ---
<span data-ttu-id="d1a34-1234">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1235">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1236">'Identity'</span></span>
- <span data-ttu-id="d1a34-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1238">'Razor'</span></span>
- <span data-ttu-id="d1a34-1239">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1240">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1241">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1242">'Identity'</span></span>
- <span data-ttu-id="d1a34-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1244">'Razor'</span></span>
- <span data-ttu-id="d1a34-1245">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1246">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1247">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1248">'Identity'</span></span>
- <span data-ttu-id="d1a34-1249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1249">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1250">'Razor'</span></span>
- <span data-ttu-id="d1a34-1251">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1252">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1253">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1254">'Identity'</span></span>
- <span data-ttu-id="d1a34-1255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1255">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1256">'Razor'</span></span>
- <span data-ttu-id="d1a34-1257">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1258">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1259">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1260">'Identity'</span></span>
- <span data-ttu-id="d1a34-1261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1261">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1262">'Razor'</span></span>
- <span data-ttu-id="d1a34-1263">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1264">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1265">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1266">'Identity'</span></span>
- <span data-ttu-id="d1a34-1267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1267">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1268">'Razor'</span></span>
- <span data-ttu-id="d1a34-1269">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1270">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1271">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1272">'Identity'</span></span>
- <span data-ttu-id="d1a34-1273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1273">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1274">'Razor'</span></span>
- <span data-ttu-id="d1a34-1275">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1276">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1277">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1278">'Identity'</span></span>
- <span data-ttu-id="d1a34-1279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1279">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1280">'Razor'</span></span>
- <span data-ttu-id="d1a34-1281">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1282">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1283">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1284">'Identity'</span></span>
- <span data-ttu-id="d1a34-1285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1285">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1286">'Razor'</span></span>
- <span data-ttu-id="d1a34-1287">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1288">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1289">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1290">'Identity'</span></span>
- <span data-ttu-id="d1a34-1291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1291">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1292">'Razor'</span></span>
- <span data-ttu-id="d1a34-1293">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1293">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-1294">------------ | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1295">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1296">'Identity'</span></span>
- <span data-ttu-id="d1a34-1297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1297">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1298">'Razor'</span></span>
- <span data-ttu-id="d1a34-1299">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1300">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1301">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1302">'Identity'</span></span>
- <span data-ttu-id="d1a34-1303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1303">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1304">'Razor'</span></span>
- <span data-ttu-id="d1a34-1305">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1306">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1307">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1308">'Identity'</span></span>
- <span data-ttu-id="d1a34-1309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1309">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1310">'Razor'</span></span>
- <span data-ttu-id="d1a34-1311">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1312">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1313">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1314">'Identity'</span></span>
- <span data-ttu-id="d1a34-1315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1315">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1316">'Razor'</span></span>
- <span data-ttu-id="d1a34-1317">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1318">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1319">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1320">'Identity'</span></span>
- <span data-ttu-id="d1a34-1321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1321">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1322">'Razor'</span></span>
- <span data-ttu-id="d1a34-1323">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1324">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1325">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1326">'Identity'</span></span>
- <span data-ttu-id="d1a34-1327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1327">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1328">'Razor'</span></span>
- <span data-ttu-id="d1a34-1329">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1330">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1331">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1332">'Identity'</span></span>
- <span data-ttu-id="d1a34-1333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1333">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1334">'Razor'</span></span>
- <span data-ttu-id="d1a34-1335">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1336">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1337">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1338">'Identity'</span></span>
- <span data-ttu-id="d1a34-1339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1339">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1340">'Razor'</span></span>
- <span data-ttu-id="d1a34-1341">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1342">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1343">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1344">'Identity'</span></span>
- <span data-ttu-id="d1a34-1345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1345">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1346">'Razor'</span></span>
- <span data-ttu-id="d1a34-1347">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1348">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1349">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1350">'Identity'</span></span>
- <span data-ttu-id="d1a34-1351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1351">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1352">'Razor'</span></span>
- <span data-ttu-id="d1a34-1353">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1354">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1355">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1356">'Identity'</span></span>
- <span data-ttu-id="d1a34-1357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1357">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1358">'Razor'</span></span>
- <span data-ttu-id="d1a34-1359">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1360">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1361">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1362">'Identity'</span></span>
- <span data-ttu-id="d1a34-1363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1363">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1364">'Razor'</span></span>
- <span data-ttu-id="d1a34-1365">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1366">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1367">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1368">'Identity'</span></span>
- <span data-ttu-id="d1a34-1369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1369">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1370">'Razor'</span></span>
- <span data-ttu-id="d1a34-1371">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1372">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1373">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1374">'Identity'</span></span>
- <span data-ttu-id="d1a34-1375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1375">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1376">'Razor'</span></span>
- <span data-ttu-id="d1a34-1377">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1378">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1379">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1380">'Identity'</span></span>
- <span data-ttu-id="d1a34-1381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1381">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1382">'Razor'</span></span>
- <span data-ttu-id="d1a34-1383">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1384">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1385">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1386">'Identity'</span></span>
- <span data-ttu-id="d1a34-1387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1387">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1388">'Razor'</span></span>
- <span data-ttu-id="d1a34-1389">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1390">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1391">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1392">'Identity'</span></span>
- <span data-ttu-id="d1a34-1393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1393">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1394">'Razor'</span></span>
- <span data-ttu-id="d1a34-1395">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1396">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1397">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1398">'Identity'</span></span>
- <span data-ttu-id="d1a34-1399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1399">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1400">'Razor'</span></span>
- <span data-ttu-id="d1a34-1401">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1402">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1403">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1404">'Identity'</span></span>
- <span data-ttu-id="d1a34-1405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1405">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1406">'Razor'</span></span>
- <span data-ttu-id="d1a34-1407">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1408">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1409">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1410">'Identity'</span></span>
- <span data-ttu-id="d1a34-1411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1411">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1412">'Razor'</span></span>
- <span data-ttu-id="d1a34-1413">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1414">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1415">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1416">'Identity'</span></span>
- <span data-ttu-id="d1a34-1417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1417">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1418">'Razor'</span></span>
- <span data-ttu-id="d1a34-1419">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1420">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1421">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1422">'Identity'</span></span>
- <span data-ttu-id="d1a34-1423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1423">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1424">'Razor'</span></span>
- <span data-ttu-id="d1a34-1425">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1426">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1427">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1428">'Identity'</span></span>
- <span data-ttu-id="d1a34-1429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1429">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1430">'Razor'</span></span>
- <span data-ttu-id="d1a34-1431">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1432">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1433">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1434">'Identity'</span></span>
- <span data-ttu-id="d1a34-1435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1435">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1436">'Razor'</span></span>
- <span data-ttu-id="d1a34-1437">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1438">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1439">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1440">'Identity'</span></span>
- <span data-ttu-id="d1a34-1441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1441">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1442">'Razor'</span></span>
- <span data-ttu-id="d1a34-1443">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1443">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-1444">--------------------------- | | Nessun prefisso | `CommandLineKey1=value1`                               |
| Due trattini ( `--` ) | `--CommandLineKey2=value2` , `--CommandLineKey2 value2` |
 | Barra ( `/` ) | `/CommandLineKey3=value3` ,`/CommandLineKey3 value3`   |</span><span class="sxs-lookup"><span data-stu-id="d1a34-1444">--------------------------- | | No prefix                | `CommandLineKey1=value1`                               |
| Two dashes (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Forward slash (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span></span>

<span data-ttu-id="d1a34-1445">Nello stesso comando, non mischiare coppie chiave-valore di argomenti della riga di comando che usano un segno di uguale con coppie chiave-valore che usano uno spazio.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1445">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="d1a34-1446">Comandi di esempio:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1446">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="d1a34-1447">Mapping di sostituzione</span><span class="sxs-lookup"><span data-stu-id="d1a34-1447">Switch mappings</span></span>

<span data-ttu-id="d1a34-1448">I mapping di sostituzione consentono di usare la logica di sostituzione del nome della chiave.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1448">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="d1a34-1449">Quando si compila manualmente la configurazione con un oggetto <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , fornire un dizionario di sostituzioni switch al <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1449">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="d1a34-1450">Quando viene utilizzato il dizionario dei mapping di sostituzione, nel dizionario viene controllata la presenza di una chiave corrispondente alla chiave fornita da un argomento della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1450">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="d1a34-1451">Se la chiave della riga di comando viene trovata nel dizionario, il valore del dizionario (la sostituzione della chiave) viene passato nuovamente per impostare la coppia chiave-valore nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1451">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="d1a34-1452">Un mapping di sostituzione è necessario per le chiavi della riga di comando con un trattino singolo (`-`) come prefisso.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1452">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="d1a34-1453">Regole principali del dizionario dei mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1453">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="d1a34-1454">Le sostituzioni devono iniziare con un trattino (`-`) o un trattino doppio (`--`).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1454">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="d1a34-1455">Il dizionario dei mapping di sostituzione non deve contenere chiavi duplicate.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1455">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="d1a34-1456">Creare un dizionario dei mapping di sostituzione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1456">Create a switch mappings dictionary.</span></span> <span data-ttu-id="d1a34-1457">Nell'esempio seguente vengono creati due mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1457">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="d1a34-1458">Quando viene creato l'host, chiamare `AddCommandLine` con il dizionario dei mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1458">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="d1a34-1459">Per le app che usano i mapping di sostituzione, la chiamata a `CreateDefaultBuilder` non deve passare argomenti.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1459">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="d1a34-1460">La chiamata `AddCommandLine` del metodo `CreateDefaultBuilder` non include sostituzioni mappate e non è possibile passare il dizionario dei mapping di sostituzione a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1460">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d1a34-1461">La soluzione consiste nel non passare gli argomenti a `CreateDefaultBuilder` consentendo invece al metodo `AddCommandLine` del metodo `ConfigurationBuilder` di elaborare entrambi gli argomenti e il dizionario dei mapping di sostituzione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1461">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="d1a34-1462">Il dizionario dei mapping di sostituzione creato contiene i dati visualizzati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1462">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="d1a34-1463">Chiave</span><span class="sxs-lookup"><span data-stu-id="d1a34-1463">Key</span></span>       | <span data-ttu-id="d1a34-1464">Valore</span><span class="sxs-lookup"><span data-stu-id="d1a34-1464">Value</span></span>             |
| ---
<span data-ttu-id="d1a34-1465">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1466">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1467">'Identity'</span></span>
- <span data-ttu-id="d1a34-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1469">'Razor'</span></span>
- <span data-ttu-id="d1a34-1470">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1471">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1472">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1473">'Identity'</span></span>
- <span data-ttu-id="d1a34-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1475">'Razor'</span></span>
- <span data-ttu-id="d1a34-1476">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1476">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-1477">----- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1478">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1479">'Identity'</span></span>
- <span data-ttu-id="d1a34-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1481">'Razor'</span></span>
- <span data-ttu-id="d1a34-1482">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1483">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1484">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1485">'Identity'</span></span>
- <span data-ttu-id="d1a34-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1487">'Razor'</span></span>
- <span data-ttu-id="d1a34-1488">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1489">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1490">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1491">'Identity'</span></span>
- <span data-ttu-id="d1a34-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1493">'Razor'</span></span>
- <span data-ttu-id="d1a34-1494">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1495">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1496">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1497">'Identity'</span></span>
- <span data-ttu-id="d1a34-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1499">'Razor'</span></span>
- <span data-ttu-id="d1a34-1500">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1501">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1502">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1503">'Identity'</span></span>
- <span data-ttu-id="d1a34-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1505">'Razor'</span></span>
- <span data-ttu-id="d1a34-1506">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1507">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1508">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1509">'Identity'</span></span>
- <span data-ttu-id="d1a34-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1511">'Razor'</span></span>
- <span data-ttu-id="d1a34-1512">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1512">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span><span class="sxs-lookup"><span data-stu-id="d1a34-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span></span>

<span data-ttu-id="d1a34-1514">Se le chiavi con mapping di sostituzione vengono usate all'avvio dell'app, la configurazione riceve il valore di configurazione per la chiave fornita dal dizionario:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1514">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="d1a34-1515">Dopo aver eseguito il comando precedente, la configurazione contiene i valori mostrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1515">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="d1a34-1516">Chiave</span><span class="sxs-lookup"><span data-stu-id="d1a34-1516">Key</span></span>               | <span data-ttu-id="d1a34-1517">Valore</span><span class="sxs-lookup"><span data-stu-id="d1a34-1517">Value</span></span>    |
| ---
<span data-ttu-id="d1a34-1518">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1519">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1520">'Identity'</span></span>
- <span data-ttu-id="d1a34-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1522">'Razor'</span></span>
- <span data-ttu-id="d1a34-1523">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1523">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1524">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1525">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1526">'Identity'</span></span>
- <span data-ttu-id="d1a34-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1528">'Razor'</span></span>
- <span data-ttu-id="d1a34-1529">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1530">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1531">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1532">'Identity'</span></span>
- <span data-ttu-id="d1a34-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1534">'Razor'</span></span>
- <span data-ttu-id="d1a34-1535">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1536">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1537">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1538">'Identity'</span></span>
- <span data-ttu-id="d1a34-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1540">'Razor'</span></span>
- <span data-ttu-id="d1a34-1541">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1542">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1543">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1544">'Identity'</span></span>
- <span data-ttu-id="d1a34-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1546">'Razor'</span></span>
- <span data-ttu-id="d1a34-1547">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1548">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1549">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1550">'Identity'</span></span>
- <span data-ttu-id="d1a34-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1552">'Razor'</span></span>
- <span data-ttu-id="d1a34-1553">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1553">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-1554">--------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1555">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1556">'Identity'</span></span>
- <span data-ttu-id="d1a34-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1558">'Razor'</span></span>
- <span data-ttu-id="d1a34-1559">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1560">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1561">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1562">'Identity'</span></span>
- <span data-ttu-id="d1a34-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1564">'Razor'</span></span>
- <span data-ttu-id="d1a34-1565">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1565">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span><span class="sxs-lookup"><span data-stu-id="d1a34-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span></span>

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="d1a34-1567">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="d1a34-1567">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="d1a34-1568"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carica la configurazione da coppie chiave-valore di variabili di ambiente in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1568">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="d1a34-1569">Per attivare la configurazione delle variabili di ambiente, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1569">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="d1a34-1570">[App Azure servizio](https://azure.microsoft.com/services/app-service/) consente di impostare le variabili di ambiente nel portale di Azure che possono eseguire l'override della configurazione dell'app usando il provider di configurazione delle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="d1a34-1571">Per altre informazioni, vedere [App di Azure: Eseguire l'override della configurazione delle app usando il portale di Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1571">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="d1a34-1572">`AddEnvironmentVariables` consente di caricare variabili di ambiente con prefisso `ASPNETCORE_` per la [configurazione host](#host-versus-app-configuration) quando viene inizializzato un nuovo generatore di host con l'[host Web](xref:fundamentals/host/web-host) e viene chiamato `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1572">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="d1a34-1573">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1573">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d1a34-1574">`CreateDefaultBuilder` carica anche:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1574">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="d1a34-1575">Configurazione delle app dalle variabili di ambiente senza prefisso chiamando `AddEnvironmentVariables` senza prefisso.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1575">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="d1a34-1576">Una configurazione facoltativa dai file *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1576">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="d1a34-1577">[Segreti utente (Secret Manager)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1577">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d1a34-1578">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1578">Command-line arguments.</span></span>

<span data-ttu-id="d1a34-1579">Il provider di configurazione delle variabili di ambiente viene chiamato dopo aver stabilito la configurazione dai segreti utente e dai file *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1579">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="d1a34-1580">La chiamata del provider in questa posizione consente alle variabili di ambiente lette in fase di esecuzione di sostituire la configurazione impostata dai segreti utente e dai file *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1580">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="d1a34-1581">Per fornire la configurazione dell'app da altre variabili di ambiente, chiamare i provider aggiuntivi dell'app in `ConfigureAppConfiguration` e chiamare `AddEnvironmentVariables` con il prefisso:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1581">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="d1a34-1582">Chiamare `AddEnvironmentVariables` Last per consentire alle variabili di ambiente con il prefisso specificato di eseguire l'override dei valori di altri provider.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1582">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="d1a34-1583">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="d1a34-1583">**Example**</span></span>

<span data-ttu-id="d1a34-1584">L'app di esempio consente di sfruttare il metodo di servizio statico `CreateDefaultBuilder` per creare l'host, che include una chiamata a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1584">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="d1a34-1585">Eseguire l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1585">Run the sample app.</span></span> <span data-ttu-id="d1a34-1586">Aprire un browser per l'app all'indirizzo `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1586">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="d1a34-1587">Notare che l'output contiene la coppia chiave-valore per la variabile di ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1587">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="d1a34-1588">Il valore riflette l'ambiente in cui l'app è in esecuzione, in genere `Development` durante l'esecuzione locale.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1588">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="d1a34-1589">Per limitare l'elenco delle variabili di ambiente restituito dall'app, l'app filtra le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1589">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="d1a34-1590">Vedere il file *Pages/Index.cshtml.cs* dell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1590">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="d1a34-1591">Per esporre tutte le variabili di ambiente disponibili per l'app, modificare `FilteredConfiguration` in *pages/index. cshtml. cs* come riportato di seguito:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1591">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="d1a34-1592">Prefissi</span><span class="sxs-lookup"><span data-stu-id="d1a34-1592">Prefixes</span></span>

<span data-ttu-id="d1a34-1593">Le variabili di ambiente caricate nella configurazione dell'app vengono filtrate quando si specifica un prefisso per il `AddEnvironmentVariables` metodo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1593">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="d1a34-1594">Ad esempio, per filtrare le variabili di ambiente in base al prefisso `CUSTOM_`, fornire il prefisso al provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1594">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="d1a34-1595">Il prefisso viene rimosso quando vengono create le coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1595">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="d1a34-1596">Quando viene creato il generatore di host, la configurazione dell'host viene fornita dalle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1596">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="d1a34-1597">Per altre informazioni sul prefisso usato per queste variabili di ambiente, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1597">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d1a34-1598">**Prefissi della stringa di connessione**</span><span class="sxs-lookup"><span data-stu-id="d1a34-1598">**Connection string prefixes**</span></span>

<span data-ttu-id="d1a34-1599">L'API di configurazione prevede regole di elaborazione speciali per quattro variabili di ambiente di stringa di connessione coinvolte nella configurazione di stringhe di connessione di Azure per l'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1599">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="d1a34-1600">Le variabili di ambiente con i prefissi indicati nella tabella vengono caricate nell'app se non viene fornito alcun prefisso a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-1600">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="d1a34-1601">Prefisso della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="d1a34-1601">Connection string prefix</span></span> | <span data-ttu-id="d1a34-1602">Provider</span><span class="sxs-lookup"><span data-stu-id="d1a34-1602">Provider</span></span> |
| ---
<span data-ttu-id="d1a34-1603">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1604">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1605">'Identity'</span></span>
- <span data-ttu-id="d1a34-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1607">'Razor'</span></span>
- <span data-ttu-id="d1a34-1608">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1609">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1610">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1611">'Identity'</span></span>
- <span data-ttu-id="d1a34-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1613">'Razor'</span></span>
- <span data-ttu-id="d1a34-1614">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1615">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1616">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1617">'Identity'</span></span>
- <span data-ttu-id="d1a34-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1619">'Razor'</span></span>
- <span data-ttu-id="d1a34-1620">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1621">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1622">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1623">'Identity'</span></span>
- <span data-ttu-id="d1a34-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1625">'Razor'</span></span>
- <span data-ttu-id="d1a34-1626">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1627">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1628">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1629">'Identity'</span></span>
- <span data-ttu-id="d1a34-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1631">'Razor'</span></span>
- <span data-ttu-id="d1a34-1632">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1633">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1634">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1635">'Identity'</span></span>
- <span data-ttu-id="d1a34-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1637">'Razor'</span></span>
- <span data-ttu-id="d1a34-1638">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1639">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1640">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1641">'Identity'</span></span>
- <span data-ttu-id="d1a34-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1643">'Razor'</span></span>
- <span data-ttu-id="d1a34-1644">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1645">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1646">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1647">'Identity'</span></span>
- <span data-ttu-id="d1a34-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1649">'Razor'</span></span>
- <span data-ttu-id="d1a34-1650">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1651">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1652">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1653">'Identity'</span></span>
- <span data-ttu-id="d1a34-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1655">'Razor'</span></span>
- <span data-ttu-id="d1a34-1656">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1657">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1658">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1659">'Identity'</span></span>
- <span data-ttu-id="d1a34-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1661">'Razor'</span></span>
- <span data-ttu-id="d1a34-1662">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-1663">------------ | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1664">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1665">'Identity'</span></span>
- <span data-ttu-id="d1a34-1666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1666">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1667">'Razor'</span></span>
- <span data-ttu-id="d1a34-1668">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1669">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1670">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1671">'Identity'</span></span>
- <span data-ttu-id="d1a34-1672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1672">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1673">'Razor'</span></span>
- <span data-ttu-id="d1a34-1674">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1674">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-1675">---- | | `CUSTOMCONNSTR_` | Provider personalizzato | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
 MySQL | `SQLAZURECONNSTR_` | SQL Server del [database SQL di Azure](https://azure.microsoft.com/services/sql-database/) |
 | `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/)|</span><span class="sxs-lookup"><span data-stu-id="d1a34-1675">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="d1a34-1676">Quando una variabile di ambiente viene individuata e caricata nella configurazione con uno qualsiasi dei quattro prefissi indicati nella tabella:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1676">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="d1a34-1677">La chiave di configurazione viene creata rimuovendo il prefisso della variabile di ambiente e aggiungendo una sezione per la chiave di configurazione (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1677">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="d1a34-1678">Viene creata una nuova coppia chiave-valore della configurazione che rappresenta il provider di connessione al database (ad eccezione di `CUSTOMCONNSTR_`, che non ha un provider dichiarato).</span><span class="sxs-lookup"><span data-stu-id="d1a34-1678">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="d1a34-1679">Chiave della variabile di ambiente</span><span class="sxs-lookup"><span data-stu-id="d1a34-1679">Environment variable key</span></span> | <span data-ttu-id="d1a34-1680">Chiave di configurazione convertita</span><span class="sxs-lookup"><span data-stu-id="d1a34-1680">Converted configuration key</span></span> | <span data-ttu-id="d1a34-1681">Voce di configurazione del provider</span><span class="sxs-lookup"><span data-stu-id="d1a34-1681">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="d1a34-1682">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1683">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1684">'Identity'</span></span>
- <span data-ttu-id="d1a34-1685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1685">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1686">'Razor'</span></span>
- <span data-ttu-id="d1a34-1687">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1688">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1689">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1690">'Identity'</span></span>
- <span data-ttu-id="d1a34-1691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1691">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1692">'Razor'</span></span>
- <span data-ttu-id="d1a34-1693">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1694">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1695">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1696">'Identity'</span></span>
- <span data-ttu-id="d1a34-1697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1697">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1698">'Razor'</span></span>
- <span data-ttu-id="d1a34-1699">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1700">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1701">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1702">'Identity'</span></span>
- <span data-ttu-id="d1a34-1703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1703">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1704">'Razor'</span></span>
- <span data-ttu-id="d1a34-1705">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1706">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1707">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1708">'Identity'</span></span>
- <span data-ttu-id="d1a34-1709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1709">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1710">'Razor'</span></span>
- <span data-ttu-id="d1a34-1711">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1712">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1713">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1714">'Identity'</span></span>
- <span data-ttu-id="d1a34-1715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1715">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1716">'Razor'</span></span>
- <span data-ttu-id="d1a34-1717">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1718">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1719">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1720">'Identity'</span></span>
- <span data-ttu-id="d1a34-1721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1721">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1722">'Razor'</span></span>
- <span data-ttu-id="d1a34-1723">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1724">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1725">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1726">'Identity'</span></span>
- <span data-ttu-id="d1a34-1727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1727">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1728">'Razor'</span></span>
- <span data-ttu-id="d1a34-1729">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1730">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1731">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1732">'Identity'</span></span>
- <span data-ttu-id="d1a34-1733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1733">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1734">'Razor'</span></span>
- <span data-ttu-id="d1a34-1735">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1736">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1737">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1738">'Identity'</span></span>
- <span data-ttu-id="d1a34-1739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1739">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1740">'Razor'</span></span>
- <span data-ttu-id="d1a34-1741">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1741">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-1742">------------ | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1743">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1744">'Identity'</span></span>
- <span data-ttu-id="d1a34-1745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1745">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1746">'Razor'</span></span>
- <span data-ttu-id="d1a34-1747">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1748">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1749">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1750">'Identity'</span></span>
- <span data-ttu-id="d1a34-1751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1751">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1752">'Razor'</span></span>
- <span data-ttu-id="d1a34-1753">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1754">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1755">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1756">'Identity'</span></span>
- <span data-ttu-id="d1a34-1757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1757">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1758">'Razor'</span></span>
- <span data-ttu-id="d1a34-1759">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1760">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1761">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1762">'Identity'</span></span>
- <span data-ttu-id="d1a34-1763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1763">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1764">'Razor'</span></span>
- <span data-ttu-id="d1a34-1765">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1766">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1767">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1768">'Identity'</span></span>
- <span data-ttu-id="d1a34-1769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1769">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1770">'Razor'</span></span>
- <span data-ttu-id="d1a34-1771">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1772">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1773">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1774">'Identity'</span></span>
- <span data-ttu-id="d1a34-1775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1775">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1776">'Razor'</span></span>
- <span data-ttu-id="d1a34-1777">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1778">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1779">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1780">'Identity'</span></span>
- <span data-ttu-id="d1a34-1781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1781">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1782">'Razor'</span></span>
- <span data-ttu-id="d1a34-1783">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1784">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1785">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1786">'Identity'</span></span>
- <span data-ttu-id="d1a34-1787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1787">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1788">'Razor'</span></span>
- <span data-ttu-id="d1a34-1789">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1790">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1791">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1792">'Identity'</span></span>
- <span data-ttu-id="d1a34-1793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1793">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1794">'Razor'</span></span>
- <span data-ttu-id="d1a34-1795">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1796">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1797">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1798">'Identity'</span></span>
- <span data-ttu-id="d1a34-1799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1799">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1800">'Razor'</span></span>
- <span data-ttu-id="d1a34-1801">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1802">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1803">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1804">'Identity'</span></span>
- <span data-ttu-id="d1a34-1805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1805">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1806">'Razor'</span></span>
- <span data-ttu-id="d1a34-1807">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1807">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-1808">-------------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1809">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1810">'Identity'</span></span>
- <span data-ttu-id="d1a34-1811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1811">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1812">'Razor'</span></span>
- <span data-ttu-id="d1a34-1813">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1814">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1815">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1816">'Identity'</span></span>
- <span data-ttu-id="d1a34-1817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1817">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1818">'Razor'</span></span>
- <span data-ttu-id="d1a34-1819">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1820">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1821">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1822">'Identity'</span></span>
- <span data-ttu-id="d1a34-1823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1823">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1824">'Razor'</span></span>
- <span data-ttu-id="d1a34-1825">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1826">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1827">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1828">'Identity'</span></span>
- <span data-ttu-id="d1a34-1829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1829">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1830">'Razor'</span></span>
- <span data-ttu-id="d1a34-1831">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1832">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1833">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1834">'Identity'</span></span>
- <span data-ttu-id="d1a34-1835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1835">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1836">'Razor'</span></span>
- <span data-ttu-id="d1a34-1837">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1838">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1839">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1840">'Identity'</span></span>
- <span data-ttu-id="d1a34-1841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1841">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1842">'Razor'</span></span>
- <span data-ttu-id="d1a34-1843">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1844">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1845">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1846">'Identity'</span></span>
- <span data-ttu-id="d1a34-1847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1847">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1848">'Razor'</span></span>
- <span data-ttu-id="d1a34-1849">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1850">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1851">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1852">'Identity'</span></span>
- <span data-ttu-id="d1a34-1853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1853">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1854">'Razor'</span></span>
- <span data-ttu-id="d1a34-1855">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1856">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1857">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1858">'Identity'</span></span>
- <span data-ttu-id="d1a34-1859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1859">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1860">'Razor'</span></span>
- <span data-ttu-id="d1a34-1861">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1862">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1863">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1864">'Identity'</span></span>
- <span data-ttu-id="d1a34-1865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1865">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1866">'Razor'</span></span>
- <span data-ttu-id="d1a34-1867">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1868">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1869">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1870">'Identity'</span></span>
- <span data-ttu-id="d1a34-1871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1871">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1872">'Razor'</span></span>
- <span data-ttu-id="d1a34-1873">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1874">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1875">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1876">'Identity'</span></span>
- <span data-ttu-id="d1a34-1877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1877">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1878">'Razor'</span></span>
- <span data-ttu-id="d1a34-1879">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1879">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1880">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1881">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1881">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1882">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1882">'Identity'</span></span>
- <span data-ttu-id="d1a34-1883">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1883">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1884">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1884">'Razor'</span></span>
- <span data-ttu-id="d1a34-1885">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1885">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1886">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1887">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1887">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1888">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1888">'Identity'</span></span>
- <span data-ttu-id="d1a34-1889">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1889">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1890">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1890">'Razor'</span></span>
- <span data-ttu-id="d1a34-1891">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1891">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1892">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1893">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1893">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1894">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1894">'Identity'</span></span>
- <span data-ttu-id="d1a34-1895">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1895">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1896">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1896">'Razor'</span></span>
- <span data-ttu-id="d1a34-1897">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1897">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1898">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1899">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1899">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1900">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1900">'Identity'</span></span>
- <span data-ttu-id="d1a34-1901">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1901">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1902">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1902">'Razor'</span></span>
- <span data-ttu-id="d1a34-1903">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1903">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1904">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1905">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1905">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1906">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1906">'Identity'</span></span>
- <span data-ttu-id="d1a34-1907">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1907">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1908">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1908">'Razor'</span></span>
- <span data-ttu-id="d1a34-1909">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1909">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1910">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1911">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1911">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1912">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1912">'Identity'</span></span>
- <span data-ttu-id="d1a34-1913">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1913">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1914">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1914">'Razor'</span></span>
- <span data-ttu-id="d1a34-1915">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1915">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1916">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1917">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1917">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1918">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1918">'Identity'</span></span>
- <span data-ttu-id="d1a34-1919">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1919">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1920">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1920">'Razor'</span></span>
- <span data-ttu-id="d1a34-1921">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1921">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1922">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1923">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1923">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1924">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1924">'Identity'</span></span>
- <span data-ttu-id="d1a34-1925">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1925">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1926">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1926">'Razor'</span></span>
- <span data-ttu-id="d1a34-1927">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1927">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1928">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1929">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1929">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1930">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1930">'Identity'</span></span>
- <span data-ttu-id="d1a34-1931">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1931">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1932">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1932">'Razor'</span></span>
- <span data-ttu-id="d1a34-1933">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1933">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1934">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1935">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1935">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1936">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1936">'Identity'</span></span>
- <span data-ttu-id="d1a34-1937">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1937">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1938">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1938">'Razor'</span></span>
- <span data-ttu-id="d1a34-1939">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1939">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1940">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1941">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1941">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1942">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1942">'Identity'</span></span>
- <span data-ttu-id="d1a34-1943">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1943">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1944">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1944">'Razor'</span></span>
- <span data-ttu-id="d1a34-1945">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1945">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1946">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1947">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1947">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1948">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1948">'Identity'</span></span>
- <span data-ttu-id="d1a34-1949">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1949">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1950">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1950">'Razor'</span></span>
- <span data-ttu-id="d1a34-1951">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1951">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1952">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1953">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1953">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1954">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1954">'Identity'</span></span>
- <span data-ttu-id="d1a34-1955">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1955">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1956">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1956">'Razor'</span></span>
- <span data-ttu-id="d1a34-1957">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1957">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1958">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1959">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1959">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1960">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1960">'Identity'</span></span>
- <span data-ttu-id="d1a34-1961">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1961">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1962">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1962">'Razor'</span></span>
- <span data-ttu-id="d1a34-1963">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1963">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1964">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1965">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1965">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1966">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1966">'Identity'</span></span>
- <span data-ttu-id="d1a34-1967">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1967">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1968">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1968">'Razor'</span></span>
- <span data-ttu-id="d1a34-1969">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1969">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1970">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1971">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1971">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1972">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1972">'Identity'</span></span>
- <span data-ttu-id="d1a34-1973">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1973">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1974">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1974">'Razor'</span></span>
- <span data-ttu-id="d1a34-1975">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1975">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1976">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1977">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1977">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1978">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1978">'Identity'</span></span>
- <span data-ttu-id="d1a34-1979">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1979">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1980">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1980">'Razor'</span></span>
- <span data-ttu-id="d1a34-1981">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1981">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1982">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1983">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1983">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1984">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1984">'Identity'</span></span>
- <span data-ttu-id="d1a34-1985">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1985">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1986">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1986">'Razor'</span></span>
- <span data-ttu-id="d1a34-1987">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1987">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1988">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1989">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1989">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1990">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1990">'Identity'</span></span>
- <span data-ttu-id="d1a34-1991">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1991">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1992">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1992">'Razor'</span></span>
- <span data-ttu-id="d1a34-1993">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1993">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-1994">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-1995">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1995">'Blazor'</span></span>
- <span data-ttu-id="d1a34-1996">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1996">'Identity'</span></span>
- <span data-ttu-id="d1a34-1997">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1997">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-1998">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-1998">'Razor'</span></span>
- <span data-ttu-id="d1a34-1999">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-1999">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2000">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2001">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2001">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2002">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2002">'Identity'</span></span>
- <span data-ttu-id="d1a34-2003">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2003">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2004">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2004">'Razor'</span></span>
- <span data-ttu-id="d1a34-2005">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2005">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2006">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2007">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2007">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2008">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2008">'Identity'</span></span>
- <span data-ttu-id="d1a34-2009">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2009">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2010">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2010">'Razor'</span></span>
- <span data-ttu-id="d1a34-2011">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2011">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2012">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2013">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2013">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2014">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2014">'Identity'</span></span>
- <span data-ttu-id="d1a34-2015">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2015">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2016">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2016">'Razor'</span></span>
- <span data-ttu-id="d1a34-2017">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2017">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2018">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2019">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2019">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2020">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2020">'Identity'</span></span>
- <span data-ttu-id="d1a34-2021">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2021">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2022">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2022">'Razor'</span></span>
- <span data-ttu-id="d1a34-2023">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2023">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2024">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2025">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2025">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2026">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2026">'Identity'</span></span>
- <span data-ttu-id="d1a34-2027">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2027">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2028">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2028">'Razor'</span></span>
- <span data-ttu-id="d1a34-2029">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2029">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Voce di configurazione non creata.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="d1a34-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Chiave: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="d1a34-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d1a34-2032">Valore: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`   |  `ConnectionStrings:{KEY}`   | Chiave: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="d1a34-2032">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d1a34-2033">Valore: `System.Data.SqlClient` | | `SQLCONNSTR_{KEY}`        |  `ConnectionStrings:{KEY}`   | Chiave: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="d1a34-2033">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d1a34-2034">Valore`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="d1a34-2034">Value: `System.Data.SqlClient`  |</span></span>

<span data-ttu-id="d1a34-2035">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="d1a34-2035">**Example**</span></span>

<span data-ttu-id="d1a34-2036">Nel server viene creata una variabile di ambiente della stringa di connessione personalizzata:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2036">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="d1a34-2037">Nome: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="d1a34-2037">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="d1a34-2038">Valore: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="d1a34-2038">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="d1a34-2039">Se `IConfiguration` viene inserito e assegnato a un campo denominato `_config` , leggere il valore:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2039">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="d1a34-2040">Provider di configurazione dei file</span><span class="sxs-lookup"><span data-stu-id="d1a34-2040">File Configuration Provider</span></span>

<span data-ttu-id="d1a34-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> è la classe base per il caricamento della configurazione dal file system.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="d1a34-2042">I provider di configurazione seguenti sono dedicati per specifici tipi di file:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2042">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="d1a34-2043">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="d1a34-2043">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="d1a34-2044">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="d1a34-2044">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="d1a34-2045">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="d1a34-2045">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="d1a34-2046">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="d1a34-2046">INI Configuration Provider</span></span>

<span data-ttu-id="d1a34-2047"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carica la configurazione da coppie chiave-valore di file INI in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2047">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="d1a34-2048">Per attivare la configurazione di file INI, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2048">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d1a34-2049">I due punti possono essere usati come delimitatore di sezione nella configurazione di file INI.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2049">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="d1a34-2050">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2050">Overloads permit specifying:</span></span>

* <span data-ttu-id="d1a34-2051">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2051">Whether the file is optional.</span></span>
* <span data-ttu-id="d1a34-2052">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2052">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="d1a34-2053">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider> usato per accedere al file.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2053">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="d1a34-2054">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2054">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="d1a34-2055">Un esempio generico di un file di configurazione INI:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2055">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="d1a34-2056">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2056">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d1a34-2057">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d1a34-2057">section0:key0</span></span>
* <span data-ttu-id="d1a34-2058">section0:key1</span><span class="sxs-lookup"><span data-stu-id="d1a34-2058">section0:key1</span></span>
* <span data-ttu-id="d1a34-2059">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="d1a34-2059">section1:subsection:key</span></span>
* <span data-ttu-id="d1a34-2060">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="d1a34-2060">section2:subsection0:key</span></span>
* <span data-ttu-id="d1a34-2061">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="d1a34-2061">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="d1a34-2062">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="d1a34-2062">JSON Configuration Provider</span></span>

<span data-ttu-id="d1a34-2063">Il <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carica la configurazione da coppie chiave-valore di file JSON in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2063">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="d1a34-2064">Per attivare la configurazione di file JSON, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2064">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d1a34-2065">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2065">Overloads permit specifying:</span></span>

* <span data-ttu-id="d1a34-2066">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2066">Whether the file is optional.</span></span>
* <span data-ttu-id="d1a34-2067">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2067">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="d1a34-2068">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider> usato per accedere al file.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2068">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="d1a34-2069">`AddJsonFile`viene chiamato automaticamente due volte quando viene inizializzato un nuovo generatore host con `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d1a34-2069">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d1a34-2070">Il metodo viene chiamato per caricare la configurazione da:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2070">The method is called to load configuration from:</span></span>

* <span data-ttu-id="d1a34-2071">*appSettings. JSON*: questo file viene letto per primo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2071">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="d1a34-2072">La versione dell'ambiente del file può sostituire i valori forniti dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2072">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="d1a34-2073">*appSettings. {Environment}. JSON*: la versione dell'ambiente del file è stata caricata in base a [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="d1a34-2073">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="d1a34-2074">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="d1a34-2074">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d1a34-2075">`CreateDefaultBuilder` carica anche:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2075">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="d1a34-2076">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2076">Environment variables.</span></span>
* <span data-ttu-id="d1a34-2077">[Segreti utente (Secret Manager)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2077">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d1a34-2078">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2078">Command-line arguments.</span></span>

<span data-ttu-id="d1a34-2079">Il provider di configurazione JSON viene stabilito per primo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2079">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="d1a34-2080">I segreti utente, le variabili di ambiente e gli argomenti della riga di comando sostituiscono quindi la configurazione impostata dai file *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2080">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="d1a34-2081">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app per file diversi da *appsettings.json* e *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2081">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="d1a34-2082">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="d1a34-2082">**Example**</span></span>

<span data-ttu-id="d1a34-2083">L'app di esempio sfrutta il metodo di convenienza statica `CreateDefaultBuilder` per compilare l'host, che include due chiamate a `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="d1a34-2083">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="d1a34-2084">La prima chiamata a `AddJsonFile` carica la configurazione da *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2084">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="d1a34-2085">La seconda chiamata a `AddJsonFile` carica la configurazione da *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2085">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="d1a34-2086">Per *appSettings. Development. JSON* nell'app di esempio, viene caricato il file seguente:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2086">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="d1a34-2087">Eseguire l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2087">Run the sample app.</span></span> <span data-ttu-id="d1a34-2088">Aprire un browser per l'app all'indirizzo `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2088">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="d1a34-2089">L'output contiene coppie chiave-valore per la configurazione basata sull'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2089">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="d1a34-2090">Il livello di registrazione della chiave `Logging:LogLevel:Default` è `Debug` quando si esegue l'app nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2090">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="d1a34-2091">Eseguire di nuovo l'app di esempio nell'ambiente di produzione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2091">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="d1a34-2092">Aprire il file *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="d1a34-2092">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="d1a34-2093">Nel `ConfigurationSample` profilo, modificare il valore della variabile di `ASPNETCORE_ENVIRONMENT` ambiente in `Production` .</span><span class="sxs-lookup"><span data-stu-id="d1a34-2093">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="d1a34-2094">Salvare il file ed eseguire l'app con `dotnet run` in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2094">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="d1a34-2095">Impostazioni in *appSettings. Development. JSON* non sostituisce più le impostazioni in *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2095">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="d1a34-2096">Il livello di registrazione per la chiave `Logging:LogLevel:Default` è `Warning` .</span><span class="sxs-lookup"><span data-stu-id="d1a34-2096">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="d1a34-2097">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="d1a34-2097">XML Configuration Provider</span></span>

<span data-ttu-id="d1a34-2098">Il <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carica la configurazione da coppie chiave-valore di file XML in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2098">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="d1a34-2099">Per attivare la configurazione di file XML, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2099">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d1a34-2100">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2100">Overloads permit specifying:</span></span>

* <span data-ttu-id="d1a34-2101">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2101">Whether the file is optional.</span></span>
* <span data-ttu-id="d1a34-2102">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2102">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="d1a34-2103">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider> usato per accedere al file.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2103">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="d1a34-2104">Il nodo radice del file di configurazione viene ignorato quando vengono create le coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2104">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="d1a34-2105">Non specificare una definizione DTD (Document Type Definition) o uno spazio dei nomi nel file.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2105">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="d1a34-2106">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2106">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="d1a34-2107">I file di configurazione XML possono usare nomi di elementi distinti per le sezioni ripetute:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2107">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="d1a34-2108">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2108">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d1a34-2109">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d1a34-2109">section0:key0</span></span>
* <span data-ttu-id="d1a34-2110">section0:key1</span><span class="sxs-lookup"><span data-stu-id="d1a34-2110">section0:key1</span></span>
* <span data-ttu-id="d1a34-2111">section1:key0</span><span class="sxs-lookup"><span data-stu-id="d1a34-2111">section1:key0</span></span>
* <span data-ttu-id="d1a34-2112">section1:key1</span><span class="sxs-lookup"><span data-stu-id="d1a34-2112">section1:key1</span></span>

<span data-ttu-id="d1a34-2113">La ripetizione di elementi che usano lo stesso nome di elemento funziona se si usa l'attributo `name` per distinguere gli elementi:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2113">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="d1a34-2114">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2114">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d1a34-2115">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="d1a34-2115">section:section0:key:key0</span></span>
* <span data-ttu-id="d1a34-2116">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="d1a34-2116">section:section0:key:key1</span></span>
* <span data-ttu-id="d1a34-2117">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="d1a34-2117">section:section1:key:key0</span></span>
* <span data-ttu-id="d1a34-2118">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="d1a34-2118">section:section1:key:key1</span></span>

<span data-ttu-id="d1a34-2119">È possibile usare attributi per fornire valori:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2119">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="d1a34-2120">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2120">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d1a34-2121">key:attribute</span><span class="sxs-lookup"><span data-stu-id="d1a34-2121">key:attribute</span></span>
* <span data-ttu-id="d1a34-2122">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="d1a34-2122">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="d1a34-2123">Provider di configurazione KeyPerFile</span><span class="sxs-lookup"><span data-stu-id="d1a34-2123">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="d1a34-2124">Il <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa i file di una directory come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2124">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="d1a34-2125">La chiave è il nome del file.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2125">The key is the file name.</span></span> <span data-ttu-id="d1a34-2126">Il valore contiene il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2126">The value contains the file's contents.</span></span> <span data-ttu-id="d1a34-2127">Il provider di configurazione KeyPerFile viene usato in scenari di hosting Docker.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2127">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="d1a34-2128">Per attivare la configurazione chiave-per-file, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2128">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="d1a34-2129">Il `directoryPath` per i file deve essere un percorso assoluto.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2129">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="d1a34-2130">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2130">Overloads permit specifying:</span></span>

* <span data-ttu-id="d1a34-2131">Un delegato `Action<KeyPerFileConfigurationSource>` che configura l'origine.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2131">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="d1a34-2132">Se la directory è facoltativa e il percorso della directory.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2132">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="d1a34-2133">Il doppio carattere di sottolineatura (`__`) viene usato come delimitatore per le chiavi di configurazione nei nomi dei file.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2133">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="d1a34-2134">Ad esempio, il nome di file `Logging__LogLevel__System` produce la chiave di configurazione `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2134">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="d1a34-2135">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2135">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="d1a34-2136">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="d1a34-2136">Memory Configuration Provider</span></span>

<span data-ttu-id="d1a34-2137">Il <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una raccolta in memoria come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2137">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="d1a34-2138">Per attivare la configurazione della raccolta in memoria, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2138">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d1a34-2139">Il provider di configurazione può essere inizializzato con un `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2139">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="d1a34-2140">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2140">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="d1a34-2141">Nell'esempio seguente viene creato un dizionario di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2141">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="d1a34-2142">Il dizionario viene usato con una chiamata a `AddInMemoryCollection` per fornire la configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2142">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="d1a34-2143">GetValue</span><span class="sxs-lookup"><span data-stu-id="d1a34-2143">GetValue</span></span>

<span data-ttu-id="d1a34-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)estrae un singolo valore dalla configurazione con una chiave specificata e lo converte nel tipo non di raccolta specificato.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="d1a34-2145">Un overload accetta un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2145">An overload accepts a default value.</span></span>

<span data-ttu-id="d1a34-2146">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2146">The following example:</span></span>

* <span data-ttu-id="d1a34-2147">Estrae il valore di stringa dalla configurazione con la chiave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2147">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="d1a34-2148">Se `NumberKey` non viene trovato nelle chiavi di configurazione, viene usato il valore predefinito di `99`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2148">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="d1a34-2149">Assegna al valore il tipo `int`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2149">Types the value as an `int`.</span></span>
* <span data-ttu-id="d1a34-2150">Memorizza il valore nella proprietà `NumberConfig` per l'uso da parte della pagina.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2150">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="d1a34-2151">GetSection, GetChildren ed Exists</span><span class="sxs-lookup"><span data-stu-id="d1a34-2151">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="d1a34-2152">Per gli esempi che seguono, prendere in considerazione il file JSON seguente.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2152">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="d1a34-2153">Vengono trovate quattro chiavi in due sezioni, una delle quali include una coppia di sottosezioni:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2153">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="d1a34-2154">Quando il file viene letto nella configurazione, vengono create le chiavi gerarchiche univoche seguenti per contenere i valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2154">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="d1a34-2155">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d1a34-2155">section0:key0</span></span>
* <span data-ttu-id="d1a34-2156">section0:key1</span><span class="sxs-lookup"><span data-stu-id="d1a34-2156">section0:key1</span></span>
* <span data-ttu-id="d1a34-2157">section1:key0</span><span class="sxs-lookup"><span data-stu-id="d1a34-2157">section1:key0</span></span>
* <span data-ttu-id="d1a34-2158">section1:key1</span><span class="sxs-lookup"><span data-stu-id="d1a34-2158">section1:key1</span></span>
* <span data-ttu-id="d1a34-2159">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="d1a34-2159">section2:subsection0:key0</span></span>
* <span data-ttu-id="d1a34-2160">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="d1a34-2160">section2:subsection0:key1</span></span>
* <span data-ttu-id="d1a34-2161">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="d1a34-2161">section2:subsection1:key0</span></span>
* <span data-ttu-id="d1a34-2162">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="d1a34-2162">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="d1a34-2163">GetSection</span><span class="sxs-lookup"><span data-stu-id="d1a34-2163">GetSection</span></span>

<span data-ttu-id="d1a34-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) estrae una sottosezione della configurazione con la chiave di sottosezione specificata.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="d1a34-2165">Per restituire una <xref:Microsoft.Extensions.Configuration.IConfigurationSection> che contiene solo le coppie chiave-valore in `section1`, chiamare `GetSection` e fornire il nome della sezione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2165">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="d1a34-2166">`configSection` non ha un valore, ma solo una chiave e un percorso.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2166">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="d1a34-2167">In modo analogo, per ottenere i valori per le chiavi in `section2:subsection0`, chiamare `GetSection` e fornire il percorso della sezione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2167">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="d1a34-2168">`GetSection` non restituisce mai `null`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2168">`GetSection` never returns `null`.</span></span> <span data-ttu-id="d1a34-2169">Se non viene trovata una sezione corrispondente, viene restituita una `IConfigurationSection` vuota.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2169">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="d1a34-2170">Quando `GetSection` restituisce una sezione corrispondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> non viene compilata.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2170">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="d1a34-2171">Quando la sezione esiste, vengono restituiti <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2171">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="d1a34-2172">GetChildren</span><span class="sxs-lookup"><span data-stu-id="d1a34-2172">GetChildren</span></span>

<span data-ttu-id="d1a34-2173">Una chiamata a [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) per `section2` ottiene una `IEnumerable<IConfigurationSection>` che include:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2173">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="d1a34-2174">Exists</span><span class="sxs-lookup"><span data-stu-id="d1a34-2174">Exists</span></span>

<span data-ttu-id="d1a34-2175">Usare [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) per determinare se esiste una sezione di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="d1a34-2176">Considerati i dati di esempio, `sectionExists` è `false` perché non esiste una sezione `section2:subsection2` nei dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2176">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="d1a34-2177">Associazione a un oggetto grafico</span><span class="sxs-lookup"><span data-stu-id="d1a34-2177">Bind to an object graph</span></span>

<span data-ttu-id="d1a34-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> è in grado di associare un intero oggetto grafico POCO.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="d1a34-2179">Come per l'associazione di un oggetto semplice, vengono associate solo le proprietà di lettura/scrittura pubbliche.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2179">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="d1a34-2180">L'esempio contiene un modello `TvShow` il cui oggetto grafico include `Metadata` e le classi `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="d1a34-2180">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="d1a34-2181">L'app di esempio ha un file *tvshow.xml* che contiene i dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2181">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="d1a34-2182">La configurazione viene associata all'intero oggetto grafico `TvShow` con il metodo `Bind`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2182">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="d1a34-2183">L'istanza associata viene assegnata a una proprietà per il rendering:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2183">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="d1a34-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)associa e restituisce il tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="d1a34-2185">`Get<T>` può essere più comodo che usare `Bind`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2185">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="d1a34-2186">Il codice seguente illustra come usare `Get<T>` con l'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2186">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="d1a34-2187">Associare una matrice a una classe</span><span class="sxs-lookup"><span data-stu-id="d1a34-2187">Bind an array to a class</span></span>

<span data-ttu-id="d1a34-2188">*L'app di esempio dimostra i concetti spiegati in questa sezione.*</span><span class="sxs-lookup"><span data-stu-id="d1a34-2188">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="d1a34-2189">Il <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supporta l'associazione di matrici agli oggetti usando gli indici delle matrici nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2189">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d1a34-2190">Qualsiasi formato di matrice che espone un segmento di chiave numerico ( `:0:` , `:1:` , &hellip; `:{n}:` ) è in grado di associare array a una matrice di classi poco.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2190">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="d1a34-2191">L'associazione viene fornita per convenzione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2191">Binding is provided by convention.</span></span> <span data-ttu-id="d1a34-2192">I provider di configurazione personalizzati non devono implementare l'associazione di matrici.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2192">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="d1a34-2193">**Elaborazione di matrici in memoria**</span><span class="sxs-lookup"><span data-stu-id="d1a34-2193">**In-memory array processing**</span></span>

<span data-ttu-id="d1a34-2194">Prendere in considerazione le chiavi di configurazione e i valori indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2194">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="d1a34-2195">Chiave</span><span class="sxs-lookup"><span data-stu-id="d1a34-2195">Key</span></span>             | <span data-ttu-id="d1a34-2196">Valore</span><span class="sxs-lookup"><span data-stu-id="d1a34-2196">Value</span></span>  |
| :---
<span data-ttu-id="d1a34-2197">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2198">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2199">'Identity'</span></span>
- <span data-ttu-id="d1a34-2200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2200">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2201">'Razor'</span></span>
- <span data-ttu-id="d1a34-2202">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2203">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2204">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2205">'Identity'</span></span>
- <span data-ttu-id="d1a34-2206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2206">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2207">'Razor'</span></span>
- <span data-ttu-id="d1a34-2208">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2209">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2210">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2211">'Identity'</span></span>
- <span data-ttu-id="d1a34-2212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2212">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2213">'Razor'</span></span>
- <span data-ttu-id="d1a34-2214">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2215">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2216">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2217">'Identity'</span></span>
- <span data-ttu-id="d1a34-2218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2218">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2219">'Razor'</span></span>
- <span data-ttu-id="d1a34-2220">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2220">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-2221">-------: | :----: | | matrice: voci: 0 | value0 | | matrice: voci: 1 | value1 | | matrice: voci: 2 | Value2 | | matrice: voci: 4 | Value4 | | matrice: voci: 5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="d1a34-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span></span>

<span data-ttu-id="d1a34-2222">Queste chiavi e i valori vengono caricati nell'app di esempio usando il provider di configurazione della memoria:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2222">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="d1a34-2223">La matrice ignora un valore per l'indice &num;3.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2223">The array skips a value for index &num;3.</span></span> <span data-ttu-id="d1a34-2224">Lo strumento di associazione di configurazione non è in grado di associare valori null o di creare voci null negli oggetti associati, situazione che diventerà chiara tra poco quando viene illustrato il risultato dell'associazione di questa matrice a un oggetto.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2224">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="d1a34-2225">Nell'app di esempio, è disponibile una classe POCO per i dati di configurazione associati:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2225">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="d1a34-2226">I dati di configurazione sono associati all'oggetto:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2226">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="d1a34-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)è anche possibile usare la sintassi, che comporta un codice più compatto:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="d1a34-2228">L'oggetto associato, un'istanza di `ArrayExample`, riceve i dati di matrice dalla configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2228">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="d1a34-2229">Indice di `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="d1a34-2229">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="d1a34-2230">`ArrayExample.Entries` Valore</span><span class="sxs-lookup"><span data-stu-id="d1a34-2230">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="d1a34-2231">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2232">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2233">'Identity'</span></span>
- <span data-ttu-id="d1a34-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2235">'Razor'</span></span>
- <span data-ttu-id="d1a34-2236">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2237">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2238">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2239">'Identity'</span></span>
- <span data-ttu-id="d1a34-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2241">'Razor'</span></span>
- <span data-ttu-id="d1a34-2242">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2243">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2244">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2245">'Identity'</span></span>
- <span data-ttu-id="d1a34-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2247">'Razor'</span></span>
- <span data-ttu-id="d1a34-2248">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2249">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2250">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2251">'Identity'</span></span>
- <span data-ttu-id="d1a34-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2253">'Razor'</span></span>
- <span data-ttu-id="d1a34-2254">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2255">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2256">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2257">'Identity'</span></span>
- <span data-ttu-id="d1a34-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2259">'Razor'</span></span>
- <span data-ttu-id="d1a34-2260">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2261">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2262">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2263">'Identity'</span></span>
- <span data-ttu-id="d1a34-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2265">'Razor'</span></span>
- <span data-ttu-id="d1a34-2266">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2267">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2268">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2269">'Identity'</span></span>
- <span data-ttu-id="d1a34-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2271">'Razor'</span></span>
- <span data-ttu-id="d1a34-2272">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2273">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2274">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2275">'Identity'</span></span>
- <span data-ttu-id="d1a34-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2277">'Razor'</span></span>
- <span data-ttu-id="d1a34-2278">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2279">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2280">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2281">'Identity'</span></span>
- <span data-ttu-id="d1a34-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2283">'Razor'</span></span>
- <span data-ttu-id="d1a34-2284">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2285">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2286">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2287">'Identity'</span></span>
- <span data-ttu-id="d1a34-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2289">'Razor'</span></span>
- <span data-ttu-id="d1a34-2290">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2291">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2292">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2293">'Identity'</span></span>
- <span data-ttu-id="d1a34-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2295">'Razor'</span></span>
- <span data-ttu-id="d1a34-2296">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2296">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-2297">-------------: | :---title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2298">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2299">'Identity'</span></span>
- <span data-ttu-id="d1a34-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2301">'Razor'</span></span>
- <span data-ttu-id="d1a34-2302">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2303">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2304">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2305">'Identity'</span></span>
- <span data-ttu-id="d1a34-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2307">'Razor'</span></span>
- <span data-ttu-id="d1a34-2308">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2309">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2310">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2311">'Identity'</span></span>
- <span data-ttu-id="d1a34-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2313">'Razor'</span></span>
- <span data-ttu-id="d1a34-2314">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2315">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2316">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2317">'Identity'</span></span>
- <span data-ttu-id="d1a34-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2319">'Razor'</span></span>
- <span data-ttu-id="d1a34-2320">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2321">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2322">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2323">'Identity'</span></span>
- <span data-ttu-id="d1a34-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2325">'Razor'</span></span>
- <span data-ttu-id="d1a34-2326">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2327">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2328">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2329">'Identity'</span></span>
- <span data-ttu-id="d1a34-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2331">'Razor'</span></span>
- <span data-ttu-id="d1a34-2332">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2333">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2334">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2335">'Identity'</span></span>
- <span data-ttu-id="d1a34-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2337">'Razor'</span></span>
- <span data-ttu-id="d1a34-2338">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2339">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2340">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2341">'Identity'</span></span>
- <span data-ttu-id="d1a34-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2343">'Razor'</span></span>
- <span data-ttu-id="d1a34-2344">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2345">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2346">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2347">'Identity'</span></span>
- <span data-ttu-id="d1a34-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2349">'Razor'</span></span>
- <span data-ttu-id="d1a34-2350">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2351">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2352">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2353">'Identity'</span></span>
- <span data-ttu-id="d1a34-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2355">'Razor'</span></span>
- <span data-ttu-id="d1a34-2356">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2357">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2358">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2359">'Identity'</span></span>
- <span data-ttu-id="d1a34-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2361">'Razor'</span></span>
- <span data-ttu-id="d1a34-2362">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2362">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-2363">-------------: | | 0 | value0 | | 1 | value1 | | 2 | Value2 | | 3 | Value4 | | 4 | value5 |</span><span class="sxs-lookup"><span data-stu-id="d1a34-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span></span>

<span data-ttu-id="d1a34-2364">L'indice &num;3 nell'oggetto associato contiene i dati di configurazione per la chiave di configurazione `array:4` e il relativo valore `value4`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2364">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="d1a34-2365">Quando i dati di configurazione contenenti una matrice vengono associati, gli indici di matrice nelle chiavi di configurazione vengono usati semplicemente per scorrere i dati di configurazione quando si crea l'oggetto.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2365">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="d1a34-2366">Un valore null non può essere mantenuto nei dati di configurazione e una voce con valore null non viene creata in un oggetto associato quando una matrice nelle chiavi di configurazione ignora uno o più indici.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2366">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="d1a34-2367">L'elemento di configurazione mancante per l'indice &num;3 può essere fornito prima dell'associazione all'istanza `ArrayExample` da qualsiasi provider di configurazione che produce la coppia chiave-valore corretta nella configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2367">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="d1a34-2368">Se il codice di esempio include un provider di configurazione JSON aggiuntivo con la coppia chiave-valore mancante, `ArrayExample.Entries` corrisponde alla matrice di configurazione completa:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2368">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="d1a34-2369">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2369">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="d1a34-2370">In `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2370">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="d1a34-2371">La coppia chiave-valore mostrata nella tabella viene caricata nella configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2371">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="d1a34-2372">Chiave</span><span class="sxs-lookup"><span data-stu-id="d1a34-2372">Key</span></span>             | <span data-ttu-id="d1a34-2373">Valore</span><span class="sxs-lookup"><span data-stu-id="d1a34-2373">Value</span></span>  |
| :---
<span data-ttu-id="d1a34-2374">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2375">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2376">'Identity'</span></span>
- <span data-ttu-id="d1a34-2377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2377">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2378">'Razor'</span></span>
- <span data-ttu-id="d1a34-2379">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2380">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2381">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2382">'Identity'</span></span>
- <span data-ttu-id="d1a34-2383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2383">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2384">'Razor'</span></span>
- <span data-ttu-id="d1a34-2385">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2386">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2387">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2388">'Identity'</span></span>
- <span data-ttu-id="d1a34-2389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2389">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2390">'Razor'</span></span>
- <span data-ttu-id="d1a34-2391">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2392">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2393">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2394">'Identity'</span></span>
- <span data-ttu-id="d1a34-2395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2395">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2396">'Razor'</span></span>
- <span data-ttu-id="d1a34-2397">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2397">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-2398">-------: | :----: | | matrice: voci: 3 | valore3 |</span><span class="sxs-lookup"><span data-stu-id="d1a34-2398">-------: | :----: | | array:entries:3 | value3 |</span></span>

<span data-ttu-id="d1a34-2399">Se l'istanza della classe `ArrayExample` viene associata dopo che il provider di configurazione JSON include la voce per l'indice &num;3, la matrice `ArrayExample.Entries` include il valore.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2399">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="d1a34-2400">Indice di `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="d1a34-2400">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="d1a34-2401">`ArrayExample.Entries` Valore</span><span class="sxs-lookup"><span data-stu-id="d1a34-2401">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="d1a34-2402">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2403">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2404">'Identity'</span></span>
- <span data-ttu-id="d1a34-2405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2405">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2406">'Razor'</span></span>
- <span data-ttu-id="d1a34-2407">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2408">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2409">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2410">'Identity'</span></span>
- <span data-ttu-id="d1a34-2411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2411">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2412">'Razor'</span></span>
- <span data-ttu-id="d1a34-2413">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2414">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2415">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2416">'Identity'</span></span>
- <span data-ttu-id="d1a34-2417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2417">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2418">'Razor'</span></span>
- <span data-ttu-id="d1a34-2419">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2420">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2421">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2422">'Identity'</span></span>
- <span data-ttu-id="d1a34-2423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2423">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2424">'Razor'</span></span>
- <span data-ttu-id="d1a34-2425">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2426">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2427">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2428">'Identity'</span></span>
- <span data-ttu-id="d1a34-2429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2429">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2430">'Razor'</span></span>
- <span data-ttu-id="d1a34-2431">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2432">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2433">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2434">'Identity'</span></span>
- <span data-ttu-id="d1a34-2435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2435">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2436">'Razor'</span></span>
- <span data-ttu-id="d1a34-2437">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2438">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2439">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2440">'Identity'</span></span>
- <span data-ttu-id="d1a34-2441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2441">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2442">'Razor'</span></span>
- <span data-ttu-id="d1a34-2443">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2443">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2444">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2445">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2446">'Identity'</span></span>
- <span data-ttu-id="d1a34-2447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2447">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2448">'Razor'</span></span>
- <span data-ttu-id="d1a34-2449">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2450">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2451">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2452">'Identity'</span></span>
- <span data-ttu-id="d1a34-2453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2453">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2454">'Razor'</span></span>
- <span data-ttu-id="d1a34-2455">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2456">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2457">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2458">'Identity'</span></span>
- <span data-ttu-id="d1a34-2459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2459">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2460">'Razor'</span></span>
- <span data-ttu-id="d1a34-2461">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2462">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2463">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2464">'Identity'</span></span>
- <span data-ttu-id="d1a34-2465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2465">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2466">'Razor'</span></span>
- <span data-ttu-id="d1a34-2467">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2467">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-2468">-------------: | :---title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2469">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2470">'Identity'</span></span>
- <span data-ttu-id="d1a34-2471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2471">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2472">'Razor'</span></span>
- <span data-ttu-id="d1a34-2473">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2474">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2475">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2476">'Identity'</span></span>
- <span data-ttu-id="d1a34-2477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2477">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2478">'Razor'</span></span>
- <span data-ttu-id="d1a34-2479">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2480">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2481">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2482">'Identity'</span></span>
- <span data-ttu-id="d1a34-2483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2483">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2484">'Razor'</span></span>
- <span data-ttu-id="d1a34-2485">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2486">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2487">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2488">'Identity'</span></span>
- <span data-ttu-id="d1a34-2489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2489">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2490">'Razor'</span></span>
- <span data-ttu-id="d1a34-2491">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2492">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2493">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2494">'Identity'</span></span>
- <span data-ttu-id="d1a34-2495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2495">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2496">'Razor'</span></span>
- <span data-ttu-id="d1a34-2497">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2498">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2499">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2500">'Identity'</span></span>
- <span data-ttu-id="d1a34-2501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2501">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2502">'Razor'</span></span>
- <span data-ttu-id="d1a34-2503">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2504">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2505">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2506">'Identity'</span></span>
- <span data-ttu-id="d1a34-2507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2507">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2508">'Razor'</span></span>
- <span data-ttu-id="d1a34-2509">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2510">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2511">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2512">'Identity'</span></span>
- <span data-ttu-id="d1a34-2513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2513">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2514">'Razor'</span></span>
- <span data-ttu-id="d1a34-2515">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2516">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2517">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2518">'Identity'</span></span>
- <span data-ttu-id="d1a34-2519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2519">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2520">'Razor'</span></span>
- <span data-ttu-id="d1a34-2521">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2522">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2523">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2524">'Identity'</span></span>
- <span data-ttu-id="d1a34-2525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2525">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2526">'Razor'</span></span>
- <span data-ttu-id="d1a34-2527">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2527">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2528">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2529">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2530">'Identity'</span></span>
- <span data-ttu-id="d1a34-2531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2531">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2532">'Razor'</span></span>
- <span data-ttu-id="d1a34-2533">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2533">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-2534">-------------: | | 0 | value0 | | 1 | value1 | | 2 | Value2 | | 3 | valore3 | | 4 | Value4 | | 5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="d1a34-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span></span>

<span data-ttu-id="d1a34-2535">**Elaborazione di matrice JSON**</span><span class="sxs-lookup"><span data-stu-id="d1a34-2535">**JSON array processing**</span></span>

<span data-ttu-id="d1a34-2536">Se un file JSON contiene una matrice, vengono create chiavi di configurazione per gli elementi della matrice con un indice di sezione in base zero.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2536">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="d1a34-2537">Nel file di configurazione seguente, `subsection` è una matrice:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2537">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="d1a34-2538">Il provider di configurazione JSON legge i dati di configurazione nelle coppie chiave-valore seguenti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2538">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="d1a34-2539">Chiave</span><span class="sxs-lookup"><span data-stu-id="d1a34-2539">Key</span></span>                     | <span data-ttu-id="d1a34-2540">Valore</span><span class="sxs-lookup"><span data-stu-id="d1a34-2540">Value</span></span>  |
| ---
<span data-ttu-id="d1a34-2541">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2542">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2543">'Identity'</span></span>
- <span data-ttu-id="d1a34-2544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2544">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2545">'Razor'</span></span>
- <span data-ttu-id="d1a34-2546">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2547">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2548">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2549">'Identity'</span></span>
- <span data-ttu-id="d1a34-2550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2550">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2551">'Razor'</span></span>
- <span data-ttu-id="d1a34-2552">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2553">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2554">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2555">'Identity'</span></span>
- <span data-ttu-id="d1a34-2556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2556">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2557">'Razor'</span></span>
- <span data-ttu-id="d1a34-2558">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2559">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2560">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2561">'Identity'</span></span>
- <span data-ttu-id="d1a34-2562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2562">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2563">'Razor'</span></span>
- <span data-ttu-id="d1a34-2564">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2565">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2566">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2567">'Identity'</span></span>
- <span data-ttu-id="d1a34-2568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2568">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2569">'Razor'</span></span>
- <span data-ttu-id="d1a34-2570">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2571">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2572">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2573">'Identity'</span></span>
- <span data-ttu-id="d1a34-2574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2574">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2575">'Razor'</span></span>
- <span data-ttu-id="d1a34-2576">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2577">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2578">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2579">'Identity'</span></span>
- <span data-ttu-id="d1a34-2580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2580">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2581">'Razor'</span></span>
- <span data-ttu-id="d1a34-2582">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2583">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2584">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2585">'Identity'</span></span>
- <span data-ttu-id="d1a34-2586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2586">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2587">'Razor'</span></span>
- <span data-ttu-id="d1a34-2588">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2589">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2590">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2591">'Identity'</span></span>
- <span data-ttu-id="d1a34-2592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2592">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2593">'Razor'</span></span>
- <span data-ttu-id="d1a34-2594">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2594">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-2595">------------ | :----: | | json_array: chiave | valore a | | json_array: sottosezione: 0 | valoreB | | json_array: sottosezione: 1 | Valorec | | json_array: sottosezione: 2 | con valori |</span><span class="sxs-lookup"><span data-stu-id="d1a34-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span></span>

<span data-ttu-id="d1a34-2596">Nell'app di esempio, la classe POCO seguente è disponibile per associare le coppie chiave-valore della configurazione:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2596">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="d1a34-2597">Dopo l'associazione, `JsonArrayExample.Key` contiene il valore `valueA`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2597">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="d1a34-2598">I valori di sottosezione vengono archiviati nella proprietà matrice POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2598">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="d1a34-2599">Indice di `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="d1a34-2599">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="d1a34-2600">`JsonArrayExample.Subsection` Valore</span><span class="sxs-lookup"><span data-stu-id="d1a34-2600">`JsonArrayExample.Subsection` Value</span></span> |
| :---
<span data-ttu-id="d1a34-2601">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2602">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2603">'Identity'</span></span>
- <span data-ttu-id="d1a34-2604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2604">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2605">'Razor'</span></span>
- <span data-ttu-id="d1a34-2606">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2607">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2608">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2609">'Identity'</span></span>
- <span data-ttu-id="d1a34-2610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2610">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2611">'Razor'</span></span>
- <span data-ttu-id="d1a34-2612">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2613">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2614">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2615">'Identity'</span></span>
- <span data-ttu-id="d1a34-2616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2616">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2617">'Razor'</span></span>
- <span data-ttu-id="d1a34-2618">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2618">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2619">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2620">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2621">'Identity'</span></span>
- <span data-ttu-id="d1a34-2622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2622">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2623">'Razor'</span></span>
- <span data-ttu-id="d1a34-2624">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2625">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2626">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2627">'Identity'</span></span>
- <span data-ttu-id="d1a34-2628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2628">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2629">'Razor'</span></span>
- <span data-ttu-id="d1a34-2630">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2631">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2632">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2633">'Identity'</span></span>
- <span data-ttu-id="d1a34-2634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2634">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2635">'Razor'</span></span>
- <span data-ttu-id="d1a34-2636">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2637">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2638">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2639">'Identity'</span></span>
- <span data-ttu-id="d1a34-2640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2640">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2641">'Razor'</span></span>
- <span data-ttu-id="d1a34-2642">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2643">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2644">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2645">'Identity'</span></span>
- <span data-ttu-id="d1a34-2646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2646">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2647">'Razor'</span></span>
- <span data-ttu-id="d1a34-2648">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2649">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2650">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2651">'Identity'</span></span>
- <span data-ttu-id="d1a34-2652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2652">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2653">'Razor'</span></span>
- <span data-ttu-id="d1a34-2654">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2655">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2656">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2657">'Identity'</span></span>
- <span data-ttu-id="d1a34-2658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2658">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2659">'Razor'</span></span>
- <span data-ttu-id="d1a34-2660">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2661">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2662">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2663">'Identity'</span></span>
- <span data-ttu-id="d1a34-2664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2664">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2665">'Razor'</span></span>
- <span data-ttu-id="d1a34-2666">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2667">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2668">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2669">'Identity'</span></span>
- <span data-ttu-id="d1a34-2670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2670">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2671">'Razor'</span></span>
- <span data-ttu-id="d1a34-2672">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2672">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2673">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2674">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2674">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2675">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2675">'Identity'</span></span>
- <span data-ttu-id="d1a34-2676">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2676">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2677">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2677">'Razor'</span></span>
- <span data-ttu-id="d1a34-2678">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2678">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2679">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2680">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2681">'Identity'</span></span>
- <span data-ttu-id="d1a34-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2683">'Razor'</span></span>
- <span data-ttu-id="d1a34-2684">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2684">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-2685">-----------------: | :---title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2686">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2687">'Identity'</span></span>
- <span data-ttu-id="d1a34-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2689">'Razor'</span></span>
- <span data-ttu-id="d1a34-2690">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2691">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2692">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2693">'Identity'</span></span>
- <span data-ttu-id="d1a34-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2695">'Razor'</span></span>
- <span data-ttu-id="d1a34-2696">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2697">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2698">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2699">'Identity'</span></span>
- <span data-ttu-id="d1a34-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2701">'Razor'</span></span>
- <span data-ttu-id="d1a34-2702">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2703">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2704">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2705">'Identity'</span></span>
- <span data-ttu-id="d1a34-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2707">'Razor'</span></span>
- <span data-ttu-id="d1a34-2708">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2709">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2710">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2711">'Identity'</span></span>
- <span data-ttu-id="d1a34-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2713">'Razor'</span></span>
- <span data-ttu-id="d1a34-2714">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2715">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2716">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2717">'Identity'</span></span>
- <span data-ttu-id="d1a34-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2719">'Razor'</span></span>
- <span data-ttu-id="d1a34-2720">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2721">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2722">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2723">'Identity'</span></span>
- <span data-ttu-id="d1a34-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2725">'Razor'</span></span>
- <span data-ttu-id="d1a34-2726">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2727">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2728">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2729">'Identity'</span></span>
- <span data-ttu-id="d1a34-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2731">'Razor'</span></span>
- <span data-ttu-id="d1a34-2732">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2733">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2734">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2735">'Identity'</span></span>
- <span data-ttu-id="d1a34-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2737">'Razor'</span></span>
- <span data-ttu-id="d1a34-2738">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2739">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2740">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2741">'Identity'</span></span>
- <span data-ttu-id="d1a34-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2743">'Razor'</span></span>
- <span data-ttu-id="d1a34-2744">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2745">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2746">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2747">'Identity'</span></span>
- <span data-ttu-id="d1a34-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2749">'Razor'</span></span>
- <span data-ttu-id="d1a34-2750">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2751">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2752">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2753">'Identity'</span></span>
- <span data-ttu-id="d1a34-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2755">'Razor'</span></span>
- <span data-ttu-id="d1a34-2756">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2757">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2758">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2759">'Identity'</span></span>
- <span data-ttu-id="d1a34-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2761">'Razor'</span></span>
- <span data-ttu-id="d1a34-2762">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d1a34-2763">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1a34-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2764">'Blazor'</span></span>
- <span data-ttu-id="d1a34-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2765">'Identity'</span></span>
- <span data-ttu-id="d1a34-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1a34-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1a34-2767">'Razor'</span></span>
- <span data-ttu-id="d1a34-2768">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d1a34-2768">'SignalR' uid:</span></span> 

<span data-ttu-id="d1a34-2769">-----------------: | | 0 | valoreB | | 1 | Valorec | | 2 | con valori |</span><span class="sxs-lookup"><span data-stu-id="d1a34-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="d1a34-2770">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="d1a34-2770">Custom configuration provider</span></span>

<span data-ttu-id="d1a34-2771">L'app di esempio dimostra come creare un provider di configurazione di base che legge le coppie chiave-valore di configurazione da un database usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="d1a34-2771">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="d1a34-2772">Il provider ha le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2772">The provider has the following characteristics:</span></span>

* <span data-ttu-id="d1a34-2773">Il database in memoria di Entity Framework viene usato a scopo dimostrativo.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2773">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="d1a34-2774">Per usare un database che richiede una stringa di connessione, implementare un `ConfigurationBuilder` secondario per fornire la stringa di connessione da un altro provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2774">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="d1a34-2775">Il provider legge una tabella di database in una configurazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2775">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="d1a34-2776">Il provider non esegue una query sul database per ogni chiave.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2776">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="d1a34-2777">Il ricaricamento in caso di modifica non è implementato, quindi l'aggiornamento del database dopo l'avvio dell'app non ha alcun effetto sulla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2777">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="d1a34-2778">Definire un'entità `EFConfigurationValue` per l'archiviazione dei valori di configurazione nel database.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2778">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="d1a34-2779">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2779">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="d1a34-2780">Aggiungere `EFConfigurationContext` per archiviare i valori configurati e accedervi.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2780">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="d1a34-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="d1a34-2782">Creare una classe che implementi <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2782">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="d1a34-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="d1a34-2784">Creare il provider di configurazione personalizzato ereditando da <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2784">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="d1a34-2785">Il provider di configurazione inizializza il database quando è vuoto.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2785">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="d1a34-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="d1a34-2787">Un metodo di estensione `AddEFConfiguration` consente di aggiungere l'origine di configurazione a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2787">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="d1a34-2788">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2788">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="d1a34-2789">L'esempio di codice seguente mostra come usare il `EFConfigurationProvider` personalizzato in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2789">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="d1a34-2790">Accedere alla configurazione durante l'avvio</span><span class="sxs-lookup"><span data-stu-id="d1a34-2790">Access configuration during startup</span></span>

<span data-ttu-id="d1a34-2791">Inserire `IConfiguration` nel costruttore `Startup` per accedere ai valori di configurazione in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2791">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d1a34-2792">Per accedere alla configurazione in `Startup.Configure`, inserire `IConfiguration` direttamente nel metodo o usare l'istanza dal costruttore:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2792">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="d1a34-2793">Per un esempio di accesso alla configurazione usando metodi di servizio di avvio, vedere [Avvio dell'applicazione: Metodi pratici](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="d1a34-2793">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="d1a34-2794">Accedere alla configurazione in una Razor pagina di pagine o in una visualizzazione MVC</span><span class="sxs-lookup"><span data-stu-id="d1a34-2794">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="d1a34-2795">Per accedere alle impostazioni di configurazione in una Razor pagina di pagine o in una visualizzazione MVC, aggiungere una [direttiva using](xref:mvc/views/razor#using) ([riferimenti per C#: direttiva using](/dotnet/csharp/language-reference/keywords/using-directive)) per lo [spazio dei nomi Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) e inserire <xref:Microsoft.Extensions.Configuration.IConfiguration> nella pagina o nella vista.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2795">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="d1a34-2796">In una Razor pagina di pagine:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2796">In a Razor Pages page:</span></span>

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

<span data-ttu-id="d1a34-2797">In una visualizzazione MVC:</span><span class="sxs-lookup"><span data-stu-id="d1a34-2797">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="d1a34-2798">Aggiungere la configurazione da un assembly esterno</span><span class="sxs-lookup"><span data-stu-id="d1a34-2798">Add configuration from an external assembly</span></span>

<span data-ttu-id="d1a34-2799">Un'implementazione <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> consente l'aggiunta di miglioramenti a un'app all'avvio, da un assembly esterno alla classe `Startup` dell'app.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2799">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="d1a34-2800">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d1a34-2800">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d1a34-2801">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d1a34-2801">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
