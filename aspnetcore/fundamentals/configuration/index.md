---
<span data-ttu-id="54933-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-102">'Blazor'</span></span>
- <span data-ttu-id="54933-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-103">'Identity'</span></span>
- <span data-ttu-id="54933-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-105">'Razor'</span></span>
- <span data-ttu-id="54933-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-106">'SignalR' uid:</span></span> 

---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="54933-107">Configurazione in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="54933-107">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="54933-108">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="54933-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="54933-109">La configurazione in ASP.NET Core viene eseguita utilizzando uno o più [provider di configurazione](#cp).</span><span class="sxs-lookup"><span data-stu-id="54933-109">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="54933-110">I provider di configurazione leggono i dati di configurazione da coppie chiave-valore usando un'ampia gamma di origini di configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-110">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="54933-111">File di impostazioni, ad esempio *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="54933-111">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="54933-112">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="54933-112">Environment variables</span></span>
* <span data-ttu-id="54933-113">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="54933-113">Azure Key Vault</span></span>
* <span data-ttu-id="54933-114">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="54933-114">Azure App Configuration</span></span>
* <span data-ttu-id="54933-115">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="54933-115">Command-line arguments</span></span>
* <span data-ttu-id="54933-116">Provider personalizzati, installati o creati</span><span class="sxs-lookup"><span data-stu-id="54933-116">Custom providers, installed or created</span></span>
* <span data-ttu-id="54933-117">File della directory</span><span class="sxs-lookup"><span data-stu-id="54933-117">Directory files</span></span>
* <span data-ttu-id="54933-118">Oggetti .NET in memoria</span><span class="sxs-lookup"><span data-stu-id="54933-118">In-memory .NET objects</span></span>

<span data-ttu-id="54933-119">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54933-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="54933-120">Configurazione predefinita</span><span class="sxs-lookup"><span data-stu-id="54933-120">Default configuration</span></span>

<span data-ttu-id="54933-121">ASP.NET Core app Web create con [DotNet New](/dotnet/core/tools/dotnet-new) o Visual Studio generano il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="54933-121">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="54933-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fornisce la configurazione predefinita per l'app nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="54933-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="54933-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : aggiunge un oggetto esistente `IConfiguration` come origine.</span><span class="sxs-lookup"><span data-stu-id="54933-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="54933-124">Nel caso di configurazione predefinita, aggiunge la configurazione [host](#hvac) e la imposta come prima origine per la configurazione dell' _app_ .</span><span class="sxs-lookup"><span data-stu-id="54933-124">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="54933-125">[appSettings. JSON](#appsettingsjson) con il [provider di configurazione JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="54933-125">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="54933-126">*appSettings.* `Environment` *. JSON* con il [provider di configurazione JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="54933-126">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="54933-127">Ad esempio, *appSettings*. ***Produzione***. *JSON* e *appSettings*. ***Sviluppo***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="54933-127">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="54933-128">[Segreti dell'app](xref:security/app-secrets) quando l'app viene eseguita nell' `Development` ambiente.</span><span class="sxs-lookup"><span data-stu-id="54933-128">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="54933-129">Variabili di ambiente che usano il [provider di configurazione delle variabili di ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="54933-129">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="54933-130">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line).</span><span class="sxs-lookup"><span data-stu-id="54933-130">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="54933-131">I provider di configurazione aggiunti successivamente sostituiscono le impostazioni di chiave precedenti.</span><span class="sxs-lookup"><span data-stu-id="54933-131">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="54933-132">Se, ad esempio, `MyKey` è impostato sia in *appSettings. JSON* che nell'ambiente, viene utilizzato il valore dell'ambiente.</span><span class="sxs-lookup"><span data-stu-id="54933-132">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="54933-133">Utilizzando i provider di configurazione predefiniti, il [provider di configurazione della riga di comando](#command-line-configuration-provider) esegue l'override di tutti gli altri provider.</span><span class="sxs-lookup"><span data-stu-id="54933-133">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="54933-134">Per ulteriori informazioni su `CreateDefaultBuilder` , vedere [impostazioni predefinite del generatore](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="54933-134">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="54933-135">Il codice seguente Visualizza i provider di configurazione abilitati nell'ordine in cui sono stati aggiunti:</span><span class="sxs-lookup"><span data-stu-id="54933-135">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="54933-136">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="54933-136">appsettings.json</span></span>

<span data-ttu-id="54933-137">Si consideri il file *appSettings. JSON* seguente:</span><span class="sxs-lookup"><span data-stu-id="54933-137">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="54933-138">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="54933-138">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="54933-139">Il valore predefinito <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carica la configurazione nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="54933-139">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="54933-140">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="54933-140">*appsettings.json*</span></span>
1. <span data-ttu-id="54933-141">*appSettings.* `Environment` *. JSON* : ad esempio, *appSettings*. ***Produzione***. *JSON* e *appSettings*. ***Sviluppo***. file *JSON* .</span><span class="sxs-lookup"><span data-stu-id="54933-141">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="54933-142">La versione dell'ambiente del file viene caricata in base a [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="54933-142">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="54933-143">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="54933-143">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="54933-144">*appSettings*. `Environment` . i valori *JSON* eseguono l'override delle chiavi in *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="54933-144">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="54933-145">Ad esempio, per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="54933-145">For example, by default:</span></span>

* <span data-ttu-id="54933-146">In fase di sviluppo, *appSettings*. ***Sviluppo***. la configurazione *JSON* sovrascrive i valori trovati in *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="54933-146">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="54933-147">In produzione, *appSettings*. ***Produzione***. la configurazione *JSON* sovrascrive i valori trovati in *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="54933-147">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="54933-148">Ad esempio, quando si distribuisce l'app in Azure.</span><span class="sxs-lookup"><span data-stu-id="54933-148">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="54933-149">Associare i dati di configurazione gerarchici usando il modello di opzioni</span><span class="sxs-lookup"><span data-stu-id="54933-149">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="54933-150">Utilizzando la configurazione [predefinita](#default) , *appSettings. JSON* e *appSettings.* `Environment` i file con *estensione JSON* sono abilitati con [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="54933-150">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="54933-151">Modifiche apportate a *appSettings. JSON* e *appSettings.* `Environment` il file con *estensione JSON* ***dopo*** l'avvio dell'app viene letto dal [provider di configurazione JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="54933-151">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="54933-152">Per informazioni sull'aggiunta di altri file di configurazione JSON, vedere [provider di configurazione JSON](#jcp) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="54933-152">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="54933-153">Gestione della sicurezza e del segreto</span><span class="sxs-lookup"><span data-stu-id="54933-153">Security and secret manager</span></span>

<span data-ttu-id="54933-154">Linee guida sui dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-154">Configuration data guidelines:</span></span>

* <span data-ttu-id="54933-155">Non archiviare mai la password o altri dati sensibili nel codice del provider di configurazione o in file di configurazione di testo normale.</span><span class="sxs-lookup"><span data-stu-id="54933-155">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="54933-156">Il [gestore del segreto](xref:security/app-secrets) può essere usato per archiviare i segreti in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="54933-156">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="54933-157">Non usare i segreti di produzione in ambienti di sviluppo o di test.</span><span class="sxs-lookup"><span data-stu-id="54933-157">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="54933-158">Specificare i segreti all'esterno del progetto in modo che non possano essere inavvertitamente inviati a un repository del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="54933-158">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="54933-159">Per [impostazione predefinita](#default), [Secret Manager](xref:security/app-secrets) legge le impostazioni di configurazione dopo *appSettings. JSON* e *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="54933-159">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="54933-160">Per ulteriori informazioni sull'archiviazione di password o altri dati sensibili:</span><span class="sxs-lookup"><span data-stu-id="54933-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="54933-161"><xref:security/app-secrets>: Include consigli sull'uso delle variabili di ambiente per archiviare dati riservati.</span><span class="sxs-lookup"><span data-stu-id="54933-161"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="54933-162">Il gestore dei segreti USA il [provider di configurazione file](#fcp) per archiviare i segreti utente in un file JSON nel sistema locale.</span><span class="sxs-lookup"><span data-stu-id="54933-162">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="54933-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) archivia in modo sicuro i segreti delle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="54933-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="54933-164">Per altre informazioni, vedere <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="54933-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="54933-165">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="54933-165">Environment variables</span></span>

<span data-ttu-id="54933-166">Usando la configurazione [predefinita](#default) , <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carica la configurazione dalle coppie chiave-valore della variabile di ambiente dopo aver letto *appSettings. JSON*, *appSettings.* `Environment` *. JSON*e [gestione segreta](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="54933-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="54933-167">Pertanto, i valori di chiave letti dall'ambiente eseguono l'override dei valori letti da *appSettings. JSON*, *appSettings.* `Environment` *. JSON*e gestione segreta.</span><span class="sxs-lookup"><span data-stu-id="54933-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="54933-168">I `set` comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="54933-168">The following `set` commands:</span></span>

* <span data-ttu-id="54933-169">Impostare le chiavi e i valori di ambiente dell' [esempio precedente](#appsettingsjson) in Windows.</span><span class="sxs-lookup"><span data-stu-id="54933-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="54933-170">Testare le impostazioni quando si usa il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="54933-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="54933-171">Il `dotnet run` comando deve essere eseguito nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="54933-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="54933-172">Le impostazioni di ambiente precedenti:</span><span class="sxs-lookup"><span data-stu-id="54933-172">The preceding environment settings:</span></span>

* <span data-ttu-id="54933-173">Vengono impostati solo nei processi avviati dalla finestra di comando in cui sono stati impostati.</span><span class="sxs-lookup"><span data-stu-id="54933-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="54933-174">Non verrà letto dai browser avviati con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="54933-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="54933-175">Per impostare le chiavi e i valori di ambiente in Windows, è possibile usare i comandi [Setx](/windows-server/administration/windows-commands/setx) seguenti.</span><span class="sxs-lookup"><span data-stu-id="54933-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="54933-176">Diversamente da `set` , `setx` le impostazioni sono rese permanente.</span><span class="sxs-lookup"><span data-stu-id="54933-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="54933-177">`/M`imposta la variabile nell'ambiente di sistema.</span><span class="sxs-lookup"><span data-stu-id="54933-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="54933-178">Se l' `/M` opzione non viene usata, viene impostata una variabile di ambiente utente.</span><span class="sxs-lookup"><span data-stu-id="54933-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="54933-179">Per verificare che i comandi precedenti eseguano l'override di *appSettings. JSON* e *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="54933-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="54933-180">Con Visual Studio: chiudere e riavviare Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="54933-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="54933-181">Con l'interfaccia della riga di comando: avviare una nuova finestra di comando e immettere `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="54933-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="54933-182">Chiamare <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> con una stringa per specificare un prefisso per le variabili di ambiente:</span><span class="sxs-lookup"><span data-stu-id="54933-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="54933-183">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="54933-183">In the preceding code:</span></span>

* <span data-ttu-id="54933-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`viene aggiunto dopo i [provider di configurazione predefiniti](#default).</span><span class="sxs-lookup"><span data-stu-id="54933-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="54933-185">Per un esempio di ordinamento dei provider di configurazione, vedere [provider di configurazione JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="54933-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="54933-186">Le variabili di ambiente impostate con il `MyCustomPrefix_` prefisso sostituiscono i [provider di configurazione predefiniti](#default).</span><span class="sxs-lookup"><span data-stu-id="54933-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="54933-187">Sono incluse le variabili di ambiente senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="54933-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="54933-188">Il prefisso viene rimosso quando vengono lette le coppie chiave-valore di configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="54933-189">I comandi seguenti verificano il prefisso personalizzato:</span><span class="sxs-lookup"><span data-stu-id="54933-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="54933-190">La [configurazione predefinita](#default) carica le variabili di ambiente e gli argomenti della riga di comando preceduti da `DOTNET_` e `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="54933-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="54933-191">I `DOTNET_` `ASPNETCORE_` prefissi e vengono usati da ASP.NET Core per la [configurazione dell'host e dell'app](xref:fundamentals/host/generic-host#host-configuration), ma non per la configurazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="54933-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="54933-192">Per ulteriori informazioni sulla configurazione dell'host e dell'app, vedere [.NET Generic Host](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="54933-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="54933-193">In [app Azure servizio](https://azure.microsoft.com/services/app-service/)selezionare **impostazione nuova applicazione** nella pagina **Impostazioni > configurazione** .</span><span class="sxs-lookup"><span data-stu-id="54933-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="54933-194">App Azure impostazioni dell'applicazione del servizio sono:</span><span class="sxs-lookup"><span data-stu-id="54933-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="54933-195">Crittografati a riposo e trasmessi su un canale crittografato.</span><span class="sxs-lookup"><span data-stu-id="54933-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="54933-196">Esposto come variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="54933-196">Exposed as environment variables.</span></span>

<span data-ttu-id="54933-197">Per altre informazioni, vedere [App di Azure: Eseguire l'override della configurazione delle app usando il portale di Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="54933-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="54933-198">Per informazioni sulle stringhe di connessione del database di Azure, vedere [prefissi della stringa di connessione](#constr) .</span><span class="sxs-lookup"><span data-stu-id="54933-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="54933-199">Riga di comando</span><span class="sxs-lookup"><span data-stu-id="54933-199">Command-line</span></span>

<span data-ttu-id="54933-200">Utilizzando la configurazione [predefinita](#default) , <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carica la configurazione dalle coppie chiave-valore dell'argomento della riga di comando dopo le origini di configurazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="54933-200">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="54933-201">*appSettings. JSON* e *appSettings*. `Environment` . file *JSON* .</span><span class="sxs-lookup"><span data-stu-id="54933-201">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="54933-202">[Segreti dell'app (gestione segreto)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="54933-202">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="54933-203">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="54933-203">Environment variables.</span></span>

<span data-ttu-id="54933-204">Per [impostazione predefinita](#default), i valori di configurazione impostati nei valori di configurazione di override della riga di comando impostati con tutti gli altri provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-204">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="54933-205">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="54933-205">Command-line arguments</span></span>

<span data-ttu-id="54933-206">Il comando seguente imposta le chiavi e i valori usando `=` :</span><span class="sxs-lookup"><span data-stu-id="54933-206">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="54933-207">Il comando seguente imposta le chiavi e i valori usando `/` :</span><span class="sxs-lookup"><span data-stu-id="54933-207">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="54933-208">Il comando seguente imposta le chiavi e i valori usando `--` :</span><span class="sxs-lookup"><span data-stu-id="54933-208">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="54933-209">Valore chiave:</span><span class="sxs-lookup"><span data-stu-id="54933-209">The key value:</span></span>

* <span data-ttu-id="54933-210">Deve seguire `=` oppure la chiave deve avere un prefisso `--` o `/` quando il valore segue uno spazio.</span><span class="sxs-lookup"><span data-stu-id="54933-210">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="54933-211">Non è obbligatorio se `=` si usa.</span><span class="sxs-lookup"><span data-stu-id="54933-211">Isn't required if `=` is used.</span></span> <span data-ttu-id="54933-212">Ad esempio: `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="54933-212">For example, `MySetting=`.</span></span>

<span data-ttu-id="54933-213">All'interno dello stesso comando, non combinare coppie chiave-valore dell'argomento della riga di comando che usano `=` con coppie chiave-valore che usano uno spazio.</span><span class="sxs-lookup"><span data-stu-id="54933-213">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="54933-214">Mapping di sostituzione</span><span class="sxs-lookup"><span data-stu-id="54933-214">Switch mappings</span></span>

<span data-ttu-id="54933-215">I mapping switch consentono la logica di sostituzione del nome **chiave** .</span><span class="sxs-lookup"><span data-stu-id="54933-215">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="54933-216">Fornire un dizionario di sostituzioni switch al <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodo.</span><span class="sxs-lookup"><span data-stu-id="54933-216">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="54933-217">Quando viene utilizzato il dizionario dei mapping di sostituzione, nel dizionario viene controllata la presenza di una chiave corrispondente alla chiave fornita da un argomento della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="54933-217">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="54933-218">Se la chiave della riga di comando viene trovata nel dizionario, il valore del dizionario viene passato di nuovo per impostare la coppia chiave-valore nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="54933-218">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="54933-219">Un mapping di sostituzione è necessario per le chiavi della riga di comando con un trattino singolo (`-`) come prefisso.</span><span class="sxs-lookup"><span data-stu-id="54933-219">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="54933-220">Regole principali del dizionario dei mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="54933-220">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="54933-221">Le opzioni devono iniziare con `-` o `--` .</span><span class="sxs-lookup"><span data-stu-id="54933-221">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="54933-222">Il dizionario dei mapping di sostituzione non deve contenere chiavi duplicate.</span><span class="sxs-lookup"><span data-stu-id="54933-222">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="54933-223">Per usare un dizionario dei mapping switch, passarlo alla chiamata a `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="54933-223">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="54933-224">Il codice seguente mostra i valori chiave per le chiavi sostituite:</span><span class="sxs-lookup"><span data-stu-id="54933-224">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="54933-225">Eseguire il comando seguente per testare la sostituzione della chiave:</span><span class="sxs-lookup"><span data-stu-id="54933-225">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="54933-226">Nota: attualmente `=` non è possibile usare per impostare i valori di sostituzione delle chiavi con un trattino singolo `-` .</span><span class="sxs-lookup"><span data-stu-id="54933-226">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="54933-227">Vedere [il problema in GitHub](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="54933-227">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="54933-228">Il comando seguente funziona per testare la sostituzione della chiave:</span><span class="sxs-lookup"><span data-stu-id="54933-228">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="54933-229">Per le app che usano i mapping di sostituzione, la chiamata a `CreateDefaultBuilder` non deve passare argomenti.</span><span class="sxs-lookup"><span data-stu-id="54933-229">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="54933-230">La `CreateDefaultBuilder` chiamata al metodo `AddCommandLine` non include opzioni mappate e non è possibile passare il dizionario di mapping switch a `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="54933-230">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="54933-231">La soluzione non passa gli argomenti a `CreateDefaultBuilder` ma per consentire al metodo del `ConfigurationBuilder` metodo `AddCommandLine` di elaborare sia gli argomenti sia il dizionario di mapping delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="54933-231">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="54933-232">Dati di configurazione gerarchici</span><span class="sxs-lookup"><span data-stu-id="54933-232">Hierarchical configuration data</span></span>

<span data-ttu-id="54933-233">L'API di configurazione legge i dati di configurazione gerarchici rendendo flat i dati gerarchici con l'uso di un delimitatore nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-233">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="54933-234">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il file *appSettings. JSON* seguente:</span><span class="sxs-lookup"><span data-stu-id="54933-234">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="54933-235">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-235">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="54933-236">Il modo migliore per leggere i dati di configurazione gerarchici consiste nell'usare il modello di opzioni.</span><span class="sxs-lookup"><span data-stu-id="54933-236">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="54933-237">Per altre informazioni, vedere [associare dati di configurazione gerarchici](#optpat) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="54933-237">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="54933-238">I metodi <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sono disponibili per isolare le sezioni e gli elementi figlio di una sezione nei dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="54933-239">Questi metodi sono descritti più avanti in [GetSection, GetChildren ed Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="54933-239">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="54933-240">Chiavi e valori di configurazione</span><span class="sxs-lookup"><span data-stu-id="54933-240">Configuration keys and values</span></span>

<span data-ttu-id="54933-241">Chiavi di configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-241">Configuration keys:</span></span>

* <span data-ttu-id="54933-242">Non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="54933-242">Are case-insensitive.</span></span> <span data-ttu-id="54933-243">Ad esempio, `ConnectionString` e `connectionstring` vengono considerate chiavi equivalenti.</span><span class="sxs-lookup"><span data-stu-id="54933-243">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="54933-244">Se una chiave e un valore vengono impostati in più provider di configurazione, viene utilizzato il valore dell'ultimo provider aggiunto.</span><span class="sxs-lookup"><span data-stu-id="54933-244">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="54933-245">Per ulteriori informazioni, vedere [configurazione predefinita](#default).</span><span class="sxs-lookup"><span data-stu-id="54933-245">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="54933-246">Chiavi gerarchiche</span><span class="sxs-lookup"><span data-stu-id="54933-246">Hierarchical keys</span></span>
  * <span data-ttu-id="54933-247">Nell'ambito dell'API di configurazione, il separatore due punti (`:`) funziona in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="54933-247">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="54933-248">Nelle variabili di ambiente, un separatore due punti potrebbe non funzionare in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="54933-248">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="54933-249">Un doppio carattere di sottolineatura, `__` , è supportato da tutte le piattaforme e viene convertito automaticamente in due punti `:` .</span><span class="sxs-lookup"><span data-stu-id="54933-249">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="54933-250">In Azure Key Vault, le chiavi gerarchiche utilizzano `--` come separatore.</span><span class="sxs-lookup"><span data-stu-id="54933-250">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="54933-251">Il [provider di configurazione Azure Key Vault](xref:security/key-vault-configuration) sostituisce automaticamente `--` con un `:` quando i segreti vengono caricati nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="54933-251">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="54933-252">Il <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supporta l'associazione di matrici agli oggetti usando gli indici delle matrici nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-252">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="54933-253">L'associazione di matrici è descritta nella sezione [Associare una matrice a una classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="54933-253">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="54933-254">Valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-254">Configuration values:</span></span>

* <span data-ttu-id="54933-255">Sono stringhe.</span><span class="sxs-lookup"><span data-stu-id="54933-255">Are strings.</span></span>
* <span data-ttu-id="54933-256">I valori null non possono essere archiviati nella configurazione o associati a oggetti.</span><span class="sxs-lookup"><span data-stu-id="54933-256">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="54933-257">Provider di configurazione</span><span class="sxs-lookup"><span data-stu-id="54933-257">Configuration providers</span></span>

<span data-ttu-id="54933-258">La tabella seguente mostra i provider di configurazione disponibili per le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="54933-258">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="54933-259">Provider</span><span class="sxs-lookup"><span data-stu-id="54933-259">Provider</span></span> | <span data-ttu-id="54933-260">Fornisce la configurazione da</span><span class="sxs-lookup"><span data-stu-id="54933-260">Provides configuration from</span></span> |
| ---
<span data-ttu-id="54933-261">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-262">'Blazor'</span></span>
- <span data-ttu-id="54933-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-263">'Identity'</span></span>
- <span data-ttu-id="54933-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-265">'Razor'</span></span>
- <span data-ttu-id="54933-266">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-267">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-268">'Blazor'</span></span>
- <span data-ttu-id="54933-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-269">'Identity'</span></span>
- <span data-ttu-id="54933-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-271">'Razor'</span></span>
- <span data-ttu-id="54933-272">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-272">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-273">---- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-274">'Blazor'</span></span>
- <span data-ttu-id="54933-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-275">'Identity'</span></span>
- <span data-ttu-id="54933-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-277">'Razor'</span></span>
- <span data-ttu-id="54933-278">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-279">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-280">'Blazor'</span></span>
- <span data-ttu-id="54933-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-281">'Identity'</span></span>
- <span data-ttu-id="54933-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-283">'Razor'</span></span>
- <span data-ttu-id="54933-284">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-285">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-286">'Blazor'</span></span>
- <span data-ttu-id="54933-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-287">'Identity'</span></span>
- <span data-ttu-id="54933-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-289">'Razor'</span></span>
- <span data-ttu-id="54933-290">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-291">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-292">'Blazor'</span></span>
- <span data-ttu-id="54933-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-293">'Identity'</span></span>
- <span data-ttu-id="54933-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-295">'Razor'</span></span>
- <span data-ttu-id="54933-296">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-297">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-298">'Blazor'</span></span>
- <span data-ttu-id="54933-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-299">'Identity'</span></span>
- <span data-ttu-id="54933-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-301">'Razor'</span></span>
- <span data-ttu-id="54933-302">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-303">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-304">'Blazor'</span></span>
- <span data-ttu-id="54933-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-305">'Identity'</span></span>
- <span data-ttu-id="54933-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-307">'Razor'</span></span>
- <span data-ttu-id="54933-308">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-309">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-310">'Blazor'</span></span>
- <span data-ttu-id="54933-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-311">'Identity'</span></span>
- <span data-ttu-id="54933-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-313">'Razor'</span></span>
- <span data-ttu-id="54933-314">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-315">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-316">'Blazor'</span></span>
- <span data-ttu-id="54933-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-317">'Identity'</span></span>
- <span data-ttu-id="54933-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-319">'Razor'</span></span>
- <span data-ttu-id="54933-320">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-321">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-322">'Blazor'</span></span>
- <span data-ttu-id="54933-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-323">'Identity'</span></span>
- <span data-ttu-id="54933-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-325">'Razor'</span></span>
- <span data-ttu-id="54933-326">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-327">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-328">'Blazor'</span></span>
- <span data-ttu-id="54933-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-329">'Identity'</span></span>
- <span data-ttu-id="54933-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-331">'Razor'</span></span>
- <span data-ttu-id="54933-332">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-333">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-334">'Blazor'</span></span>
- <span data-ttu-id="54933-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-335">'Identity'</span></span>
- <span data-ttu-id="54933-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-337">'Razor'</span></span>
- <span data-ttu-id="54933-338">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-339">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-340">'Blazor'</span></span>
- <span data-ttu-id="54933-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-341">'Identity'</span></span>
- <span data-ttu-id="54933-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-343">'Razor'</span></span>
- <span data-ttu-id="54933-344">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-345">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-346">'Blazor'</span></span>
- <span data-ttu-id="54933-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-347">'Identity'</span></span>
- <span data-ttu-id="54933-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-349">'Razor'</span></span>
- <span data-ttu-id="54933-350">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-351">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-352">'Blazor'</span></span>
- <span data-ttu-id="54933-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-353">'Identity'</span></span>
- <span data-ttu-id="54933-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-355">'Razor'</span></span>
- <span data-ttu-id="54933-356">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-357">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-358">'Blazor'</span></span>
- <span data-ttu-id="54933-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-359">'Identity'</span></span>
- <span data-ttu-id="54933-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-361">'Razor'</span></span>
- <span data-ttu-id="54933-362">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-362">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-363">------------------ | | [Provider di configurazione Azure Key Vault](xref:security/key-vault-configuration) | Azure Key Vault | | [Provider di configurazione app Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Configurazione app Azure | | [Provider di configurazione della riga di comando](#clcp) | Parametri della riga di comando | | [Provider di configurazione personalizzato](#custom-configuration-provider) | Origine personalizzata | | [Provider di configurazione delle variabili di ambiente](#evcp) | Variabili di ambiente | | [Provider di configurazione file](#file-configuration-provider) | File INI, JSON e XML | | [Provider di configurazione chiave per file](#key-per-file-configuration-provider) | File di directory | | [Provider di configurazione della memoria](#memory-configuration-provider) | Raccolte in memoria | | [Gestore Secret](xref:security/app-secrets) | File nella directory del profilo utente |</span><span class="sxs-lookup"><span data-stu-id="54933-363">------------------ | | [Azure Key Vault configuration provider](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Command-line configuration provider](#clcp) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables configuration provider](#evcp) | Environment variables | | [File configuration provider](#file-configuration-provider) | INI, JSON, and XML files | | [Key-per-file configuration provider](#key-per-file-configuration-provider) | Directory files | | [Memory configuration provider](#memory-configuration-provider) | In-memory collections | | [Secret Manager](xref:security/app-secrets)  | File in the user profile directory |</span></span>

<span data-ttu-id="54933-364">Le origini di configurazione vengono lette nell'ordine in cui sono specificati i provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-364">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="54933-365">Ordinare i provider di configurazione nel codice in base alle priorità per le origini di configurazione sottostanti richieste dall'app.</span><span class="sxs-lookup"><span data-stu-id="54933-365">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="54933-366">Una sequenza tipica di provider di configurazione è:</span><span class="sxs-lookup"><span data-stu-id="54933-366">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="54933-367">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="54933-367">*appsettings.json*</span></span>
1. <span data-ttu-id="54933-368">*appSettings*. `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="54933-368">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="54933-369">Gestione segreta</span><span class="sxs-lookup"><span data-stu-id="54933-369">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="54933-370">Variabili di ambiente che usano il [provider di configurazione delle variabili di ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="54933-370">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="54933-371">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="54933-371">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="54933-372">Una procedura comune consiste nell'aggiungere il provider di configurazione della riga di comando per ultimo in una serie di provider per consentire agli argomenti della riga di comando di eseguire l'override del set di configurazione da parte degli altri provider.</span><span class="sxs-lookup"><span data-stu-id="54933-372">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="54933-373">La sequenza di provider precedente viene utilizzata nella [configurazione predefinita](#default).</span><span class="sxs-lookup"><span data-stu-id="54933-373">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="54933-374">Prefissi della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="54933-374">Connection string prefixes</span></span>

<span data-ttu-id="54933-375">L'API di configurazione dispone di regole di elaborazione speciali per quattro variabili di ambiente della stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="54933-375">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="54933-376">Queste stringhe di connessione sono necessarie per configurare le stringhe di connessione di Azure per l'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="54933-376">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="54933-377">Le variabili di ambiente con i prefissi visualizzati nella tabella vengono caricate nell'app con la [configurazione predefinita](#default) o quando non viene fornito alcun prefisso a `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="54933-377">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="54933-378">Prefisso della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="54933-378">Connection string prefix</span></span> | <span data-ttu-id="54933-379">Provider</span><span class="sxs-lookup"><span data-stu-id="54933-379">Provider</span></span> |
| ---
<span data-ttu-id="54933-380">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-381">'Blazor'</span></span>
- <span data-ttu-id="54933-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-382">'Identity'</span></span>
- <span data-ttu-id="54933-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-384">'Razor'</span></span>
- <span data-ttu-id="54933-385">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-386">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-387">'Blazor'</span></span>
- <span data-ttu-id="54933-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-388">'Identity'</span></span>
- <span data-ttu-id="54933-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-390">'Razor'</span></span>
- <span data-ttu-id="54933-391">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-392">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-393">'Blazor'</span></span>
- <span data-ttu-id="54933-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-394">'Identity'</span></span>
- <span data-ttu-id="54933-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-396">'Razor'</span></span>
- <span data-ttu-id="54933-397">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-397">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-398">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-399">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-399">'Blazor'</span></span>
- <span data-ttu-id="54933-400">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-400">'Identity'</span></span>
- <span data-ttu-id="54933-401">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-401">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-402">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-402">'Razor'</span></span>
- <span data-ttu-id="54933-403">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-403">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-404">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-405">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-405">'Blazor'</span></span>
- <span data-ttu-id="54933-406">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-406">'Identity'</span></span>
- <span data-ttu-id="54933-407">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-407">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-408">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-408">'Razor'</span></span>
- <span data-ttu-id="54933-409">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-409">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-410">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-411">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-411">'Blazor'</span></span>
- <span data-ttu-id="54933-412">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-412">'Identity'</span></span>
- <span data-ttu-id="54933-413">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-413">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-414">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-414">'Razor'</span></span>
- <span data-ttu-id="54933-415">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-415">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-416">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-417">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-417">'Blazor'</span></span>
- <span data-ttu-id="54933-418">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-418">'Identity'</span></span>
- <span data-ttu-id="54933-419">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-419">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-420">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-420">'Razor'</span></span>
- <span data-ttu-id="54933-421">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-421">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-422">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-423">'Blazor'</span></span>
- <span data-ttu-id="54933-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-424">'Identity'</span></span>
- <span data-ttu-id="54933-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-426">'Razor'</span></span>
- <span data-ttu-id="54933-427">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-428">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-429">'Blazor'</span></span>
- <span data-ttu-id="54933-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-430">'Identity'</span></span>
- <span data-ttu-id="54933-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-432">'Razor'</span></span>
- <span data-ttu-id="54933-433">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-434">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-435">'Blazor'</span></span>
- <span data-ttu-id="54933-436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-436">'Identity'</span></span>
- <span data-ttu-id="54933-437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-437">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-438">'Razor'</span></span>
- <span data-ttu-id="54933-439">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-439">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-440">------------ | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-441">'Blazor'</span></span>
- <span data-ttu-id="54933-442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-442">'Identity'</span></span>
- <span data-ttu-id="54933-443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-443">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-444">'Razor'</span></span>
- <span data-ttu-id="54933-445">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-446">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-447">'Blazor'</span></span>
- <span data-ttu-id="54933-448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-448">'Identity'</span></span>
- <span data-ttu-id="54933-449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-449">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-450">'Razor'</span></span>
- <span data-ttu-id="54933-451">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-451">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-452">---- | | `CUSTOMCONNSTR_` | Provider personalizzato | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
 MySQL | `SQLAZURECONNSTR_` | SQL Server del [database SQL di Azure](https://azure.microsoft.com/services/sql-database/) |
 | `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/)|</span><span class="sxs-lookup"><span data-stu-id="54933-452">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="54933-453">Quando una variabile di ambiente viene individuata e caricata nella configurazione con uno qualsiasi dei quattro prefissi indicati nella tabella:</span><span class="sxs-lookup"><span data-stu-id="54933-453">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="54933-454">La chiave di configurazione viene creata rimuovendo il prefisso della variabile di ambiente e aggiungendo una sezione per la chiave di configurazione (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="54933-454">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="54933-455">Viene creata una nuova coppia chiave-valore della configurazione che rappresenta il provider di connessione al database (ad eccezione di `CUSTOMCONNSTR_`, che non ha un provider dichiarato).</span><span class="sxs-lookup"><span data-stu-id="54933-455">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="54933-456">Chiave della variabile di ambiente</span><span class="sxs-lookup"><span data-stu-id="54933-456">Environment variable key</span></span> | <span data-ttu-id="54933-457">Chiave di configurazione convertita</span><span class="sxs-lookup"><span data-stu-id="54933-457">Converted configuration key</span></span> | <span data-ttu-id="54933-458">Voce di configurazione del provider</span><span class="sxs-lookup"><span data-stu-id="54933-458">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="54933-459">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-460">'Blazor'</span></span>
- <span data-ttu-id="54933-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-461">'Identity'</span></span>
- <span data-ttu-id="54933-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-463">'Razor'</span></span>
- <span data-ttu-id="54933-464">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-465">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-466">'Blazor'</span></span>
- <span data-ttu-id="54933-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-467">'Identity'</span></span>
- <span data-ttu-id="54933-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-469">'Razor'</span></span>
- <span data-ttu-id="54933-470">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-471">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-472">'Blazor'</span></span>
- <span data-ttu-id="54933-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-473">'Identity'</span></span>
- <span data-ttu-id="54933-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-475">'Razor'</span></span>
- <span data-ttu-id="54933-476">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-477">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-478">'Blazor'</span></span>
- <span data-ttu-id="54933-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-479">'Identity'</span></span>
- <span data-ttu-id="54933-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-481">'Razor'</span></span>
- <span data-ttu-id="54933-482">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-483">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-484">'Blazor'</span></span>
- <span data-ttu-id="54933-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-485">'Identity'</span></span>
- <span data-ttu-id="54933-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-487">'Razor'</span></span>
- <span data-ttu-id="54933-488">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-489">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-490">'Blazor'</span></span>
- <span data-ttu-id="54933-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-491">'Identity'</span></span>
- <span data-ttu-id="54933-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-493">'Razor'</span></span>
- <span data-ttu-id="54933-494">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-495">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-496">'Blazor'</span></span>
- <span data-ttu-id="54933-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-497">'Identity'</span></span>
- <span data-ttu-id="54933-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-499">'Razor'</span></span>
- <span data-ttu-id="54933-500">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-501">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-502">'Blazor'</span></span>
- <span data-ttu-id="54933-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-503">'Identity'</span></span>
- <span data-ttu-id="54933-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-505">'Razor'</span></span>
- <span data-ttu-id="54933-506">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-507">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-508">'Blazor'</span></span>
- <span data-ttu-id="54933-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-509">'Identity'</span></span>
- <span data-ttu-id="54933-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-511">'Razor'</span></span>
- <span data-ttu-id="54933-512">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-513">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-514">'Blazor'</span></span>
- <span data-ttu-id="54933-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-515">'Identity'</span></span>
- <span data-ttu-id="54933-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-517">'Razor'</span></span>
- <span data-ttu-id="54933-518">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-518">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-519">------------ | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-520">'Blazor'</span></span>
- <span data-ttu-id="54933-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-521">'Identity'</span></span>
- <span data-ttu-id="54933-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-523">'Razor'</span></span>
- <span data-ttu-id="54933-524">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-525">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-526">'Blazor'</span></span>
- <span data-ttu-id="54933-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-527">'Identity'</span></span>
- <span data-ttu-id="54933-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-529">'Razor'</span></span>
- <span data-ttu-id="54933-530">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-531">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-532">'Blazor'</span></span>
- <span data-ttu-id="54933-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-533">'Identity'</span></span>
- <span data-ttu-id="54933-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-535">'Razor'</span></span>
- <span data-ttu-id="54933-536">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-537">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-538">'Blazor'</span></span>
- <span data-ttu-id="54933-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-539">'Identity'</span></span>
- <span data-ttu-id="54933-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-541">'Razor'</span></span>
- <span data-ttu-id="54933-542">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-543">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-544">'Blazor'</span></span>
- <span data-ttu-id="54933-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-545">'Identity'</span></span>
- <span data-ttu-id="54933-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-547">'Razor'</span></span>
- <span data-ttu-id="54933-548">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-549">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-550">'Blazor'</span></span>
- <span data-ttu-id="54933-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-551">'Identity'</span></span>
- <span data-ttu-id="54933-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-553">'Razor'</span></span>
- <span data-ttu-id="54933-554">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-555">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-556">'Blazor'</span></span>
- <span data-ttu-id="54933-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-557">'Identity'</span></span>
- <span data-ttu-id="54933-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-559">'Razor'</span></span>
- <span data-ttu-id="54933-560">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-561">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-562">'Blazor'</span></span>
- <span data-ttu-id="54933-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-563">'Identity'</span></span>
- <span data-ttu-id="54933-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-565">'Razor'</span></span>
- <span data-ttu-id="54933-566">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-567">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-568">'Blazor'</span></span>
- <span data-ttu-id="54933-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-569">'Identity'</span></span>
- <span data-ttu-id="54933-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-571">'Razor'</span></span>
- <span data-ttu-id="54933-572">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-573">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-574">'Blazor'</span></span>
- <span data-ttu-id="54933-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-575">'Identity'</span></span>
- <span data-ttu-id="54933-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-577">'Razor'</span></span>
- <span data-ttu-id="54933-578">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-579">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-580">'Blazor'</span></span>
- <span data-ttu-id="54933-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-581">'Identity'</span></span>
- <span data-ttu-id="54933-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-583">'Razor'</span></span>
- <span data-ttu-id="54933-584">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-584">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-585">-------------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-586">'Blazor'</span></span>
- <span data-ttu-id="54933-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-587">'Identity'</span></span>
- <span data-ttu-id="54933-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-589">'Razor'</span></span>
- <span data-ttu-id="54933-590">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-591">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-592">'Blazor'</span></span>
- <span data-ttu-id="54933-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-593">'Identity'</span></span>
- <span data-ttu-id="54933-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-595">'Razor'</span></span>
- <span data-ttu-id="54933-596">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-597">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-598">'Blazor'</span></span>
- <span data-ttu-id="54933-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-599">'Identity'</span></span>
- <span data-ttu-id="54933-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-601">'Razor'</span></span>
- <span data-ttu-id="54933-602">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-603">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-604">'Blazor'</span></span>
- <span data-ttu-id="54933-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-605">'Identity'</span></span>
- <span data-ttu-id="54933-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-607">'Razor'</span></span>
- <span data-ttu-id="54933-608">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-609">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-610">'Blazor'</span></span>
- <span data-ttu-id="54933-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-611">'Identity'</span></span>
- <span data-ttu-id="54933-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-613">'Razor'</span></span>
- <span data-ttu-id="54933-614">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-615">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-616">'Blazor'</span></span>
- <span data-ttu-id="54933-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-617">'Identity'</span></span>
- <span data-ttu-id="54933-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-619">'Razor'</span></span>
- <span data-ttu-id="54933-620">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-621">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-622">'Blazor'</span></span>
- <span data-ttu-id="54933-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-623">'Identity'</span></span>
- <span data-ttu-id="54933-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-625">'Razor'</span></span>
- <span data-ttu-id="54933-626">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-627">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-628">'Blazor'</span></span>
- <span data-ttu-id="54933-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-629">'Identity'</span></span>
- <span data-ttu-id="54933-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-631">'Razor'</span></span>
- <span data-ttu-id="54933-632">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-633">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-634">'Blazor'</span></span>
- <span data-ttu-id="54933-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-635">'Identity'</span></span>
- <span data-ttu-id="54933-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-637">'Razor'</span></span>
- <span data-ttu-id="54933-638">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-639">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-640">'Blazor'</span></span>
- <span data-ttu-id="54933-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-641">'Identity'</span></span>
- <span data-ttu-id="54933-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-643">'Razor'</span></span>
- <span data-ttu-id="54933-644">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-645">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-646">'Blazor'</span></span>
- <span data-ttu-id="54933-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-647">'Identity'</span></span>
- <span data-ttu-id="54933-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-649">'Razor'</span></span>
- <span data-ttu-id="54933-650">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-651">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-652">'Blazor'</span></span>
- <span data-ttu-id="54933-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-653">'Identity'</span></span>
- <span data-ttu-id="54933-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-655">'Razor'</span></span>
- <span data-ttu-id="54933-656">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-657">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-658">'Blazor'</span></span>
- <span data-ttu-id="54933-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-659">'Identity'</span></span>
- <span data-ttu-id="54933-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-661">'Razor'</span></span>
- <span data-ttu-id="54933-662">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-663">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-664">'Blazor'</span></span>
- <span data-ttu-id="54933-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-665">'Identity'</span></span>
- <span data-ttu-id="54933-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-667">'Razor'</span></span>
- <span data-ttu-id="54933-668">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-669">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-670">'Blazor'</span></span>
- <span data-ttu-id="54933-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-671">'Identity'</span></span>
- <span data-ttu-id="54933-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-673">'Razor'</span></span>
- <span data-ttu-id="54933-674">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-675">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-676">'Blazor'</span></span>
- <span data-ttu-id="54933-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-677">'Identity'</span></span>
- <span data-ttu-id="54933-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-679">'Razor'</span></span>
- <span data-ttu-id="54933-680">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-681">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-682">'Blazor'</span></span>
- <span data-ttu-id="54933-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-683">'Identity'</span></span>
- <span data-ttu-id="54933-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-685">'Razor'</span></span>
- <span data-ttu-id="54933-686">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-687">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-688">'Blazor'</span></span>
- <span data-ttu-id="54933-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-689">'Identity'</span></span>
- <span data-ttu-id="54933-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-691">'Razor'</span></span>
- <span data-ttu-id="54933-692">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-693">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-694">'Blazor'</span></span>
- <span data-ttu-id="54933-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-695">'Identity'</span></span>
- <span data-ttu-id="54933-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-697">'Razor'</span></span>
- <span data-ttu-id="54933-698">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-699">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-700">'Blazor'</span></span>
- <span data-ttu-id="54933-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-701">'Identity'</span></span>
- <span data-ttu-id="54933-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-703">'Razor'</span></span>
- <span data-ttu-id="54933-704">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-705">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-706">'Blazor'</span></span>
- <span data-ttu-id="54933-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-707">'Identity'</span></span>
- <span data-ttu-id="54933-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-709">'Razor'</span></span>
- <span data-ttu-id="54933-710">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-711">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-712">'Blazor'</span></span>
- <span data-ttu-id="54933-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-713">'Identity'</span></span>
- <span data-ttu-id="54933-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-715">'Razor'</span></span>
- <span data-ttu-id="54933-716">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-717">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-718">'Blazor'</span></span>
- <span data-ttu-id="54933-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-719">'Identity'</span></span>
- <span data-ttu-id="54933-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-721">'Razor'</span></span>
- <span data-ttu-id="54933-722">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-723">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-724">'Blazor'</span></span>
- <span data-ttu-id="54933-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-725">'Identity'</span></span>
- <span data-ttu-id="54933-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-727">'Razor'</span></span>
- <span data-ttu-id="54933-728">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-729">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-730">'Blazor'</span></span>
- <span data-ttu-id="54933-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-731">'Identity'</span></span>
- <span data-ttu-id="54933-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-733">'Razor'</span></span>
- <span data-ttu-id="54933-734">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-735">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-736">'Blazor'</span></span>
- <span data-ttu-id="54933-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-737">'Identity'</span></span>
- <span data-ttu-id="54933-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-739">'Razor'</span></span>
- <span data-ttu-id="54933-740">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-741">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-742">'Blazor'</span></span>
- <span data-ttu-id="54933-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-743">'Identity'</span></span>
- <span data-ttu-id="54933-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-745">'Razor'</span></span>
- <span data-ttu-id="54933-746">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-747">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-748">'Blazor'</span></span>
- <span data-ttu-id="54933-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-749">'Identity'</span></span>
- <span data-ttu-id="54933-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-751">'Razor'</span></span>
- <span data-ttu-id="54933-752">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-753">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-754">'Blazor'</span></span>
- <span data-ttu-id="54933-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-755">'Identity'</span></span>
- <span data-ttu-id="54933-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-757">'Razor'</span></span>
- <span data-ttu-id="54933-758">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-759">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-760">'Blazor'</span></span>
- <span data-ttu-id="54933-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-761">'Identity'</span></span>
- <span data-ttu-id="54933-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-763">'Razor'</span></span>
- <span data-ttu-id="54933-764">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-765">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-766">'Blazor'</span></span>
- <span data-ttu-id="54933-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-767">'Identity'</span></span>
- <span data-ttu-id="54933-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-769">'Razor'</span></span>
- <span data-ttu-id="54933-770">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-771">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-772">'Blazor'</span></span>
- <span data-ttu-id="54933-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-773">'Identity'</span></span>
- <span data-ttu-id="54933-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-775">'Razor'</span></span>
- <span data-ttu-id="54933-776">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-777">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-778">'Blazor'</span></span>
- <span data-ttu-id="54933-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-779">'Identity'</span></span>
- <span data-ttu-id="54933-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-781">'Razor'</span></span>
- <span data-ttu-id="54933-782">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-783">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-784">'Blazor'</span></span>
- <span data-ttu-id="54933-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-785">'Identity'</span></span>
- <span data-ttu-id="54933-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-787">'Razor'</span></span>
- <span data-ttu-id="54933-788">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-789">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-790">'Blazor'</span></span>
- <span data-ttu-id="54933-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-791">'Identity'</span></span>
- <span data-ttu-id="54933-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-793">'Razor'</span></span>
- <span data-ttu-id="54933-794">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-795">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-796">'Blazor'</span></span>
- <span data-ttu-id="54933-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-797">'Identity'</span></span>
- <span data-ttu-id="54933-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-799">'Razor'</span></span>
- <span data-ttu-id="54933-800">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-801">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-802">'Blazor'</span></span>
- <span data-ttu-id="54933-803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-803">'Identity'</span></span>
- <span data-ttu-id="54933-804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-804">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-805">'Razor'</span></span>
- <span data-ttu-id="54933-806">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-806">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Voce di configurazione non creata.</span><span class="sxs-lookup"><span data-stu-id="54933-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="54933-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Chiave: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="54933-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="54933-809">Valore: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`   |  `ConnectionStrings:{KEY}`   | Chiave: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="54933-809">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="54933-810">Valore: `System.Data.SqlClient` | | `SQLCONNSTR_{KEY}`        |  `ConnectionStrings:{KEY}`   | Chiave: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="54933-810">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="54933-811">Valore`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="54933-811">Value: `System.Data.SqlClient`  |</span></span>

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="54933-812">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="54933-812">JSON configuration provider</span></span>

<span data-ttu-id="54933-813"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Carica la configurazione dalle coppie chiave-valore del file JSON.</span><span class="sxs-lookup"><span data-stu-id="54933-813">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="54933-814">Gli overload possono specificare:</span><span class="sxs-lookup"><span data-stu-id="54933-814">Overloads can specify:</span></span>

* <span data-ttu-id="54933-815">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="54933-815">Whether the file is optional.</span></span>
* <span data-ttu-id="54933-816">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="54933-816">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="54933-817">Esaminare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="54933-817">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="54933-818">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="54933-818">The preceding code:</span></span>

* <span data-ttu-id="54933-819">Configura il provider di configurazione JSON per caricare il file *config. JSON* con le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="54933-819">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="54933-820">`optional: true`: Il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="54933-820">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="54933-821">`reloadOnChange: true`: Il file viene ricaricato quando vengono salvate le modifiche.</span><span class="sxs-lookup"><span data-stu-id="54933-821">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="54933-822">Legge i [provider di configurazione predefiniti](#default) prima del file *config. JSON* .</span><span class="sxs-lookup"><span data-stu-id="54933-822">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="54933-823">Impostazioni nell'impostazione di sostituzione del file *config. JSON* nei provider di configurazione predefiniti, tra cui il [provider di configurazione delle variabili di ambiente](#evcp) e il provider di configurazione della riga di [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="54933-823">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="54933-824">In genere ***non*** si vuole che un file JSON personalizzato esegua l'override dei valori impostati nel [provider di configurazione delle variabili di ambiente](#evcp) e nel provider di configurazione della riga di [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="54933-824">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="54933-825">Il codice seguente cancella tutti i provider di configurazione e aggiunge diversi provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-825">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="54933-826">Nel codice precedente, Settings in *config. JSON* e *config*. `Environment` . file *JSON* :</span><span class="sxs-lookup"><span data-stu-id="54933-826">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="54933-827">Eseguire l'override delle impostazioni nel file *appSettings. JSON* e *appSettings*. `Environment` . file *JSON* .</span><span class="sxs-lookup"><span data-stu-id="54933-827">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="54933-828">Viene sottoposto a override dalle impostazioni del [provider di configurazione delle variabili di ambiente](#evcp) e del provider di configurazione della riga di [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="54933-828">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="54933-829">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il file *config. JSON* seguente:</span><span class="sxs-lookup"><span data-stu-id="54933-829">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="54933-830">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="54933-830">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="54933-831">Provider di configurazione file</span><span class="sxs-lookup"><span data-stu-id="54933-831">File configuration provider</span></span>

<span data-ttu-id="54933-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> è la classe base per il caricamento della configurazione dal file system.</span><span class="sxs-lookup"><span data-stu-id="54933-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="54933-833">I provider di configurazione seguenti derivano da `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="54933-833">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="54933-834">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="54933-834">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="54933-835">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="54933-835">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="54933-836">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="54933-836">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="54933-837">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="54933-837">INI configuration provider</span></span>

<span data-ttu-id="54933-838"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carica la configurazione da coppie chiave-valore di file INI in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="54933-838">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="54933-839">Il codice seguente cancella tutti i provider di configurazione e aggiunge diversi provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-839">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="54933-840">Nel codice precedente, le impostazioni in *MyIniConfig. ini* e *MyIniConfig*. `Environment` . i file *ini* vengono sottoposti a override dalle impostazioni nel:</span><span class="sxs-lookup"><span data-stu-id="54933-840">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="54933-841">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="54933-841">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="54933-842">[Provider di configurazione della riga di comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="54933-842">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="54933-843">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il seguente file *MyIniConfig. ini* :</span><span class="sxs-lookup"><span data-stu-id="54933-843">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="54933-844">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="54933-844">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="54933-845">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="54933-845">XML configuration provider</span></span>

<span data-ttu-id="54933-846">Il <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carica la configurazione da coppie chiave-valore di file XML in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="54933-846">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="54933-847">Il codice seguente cancella tutti i provider di configurazione e aggiunge diversi provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-847">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="54933-848">Nel codice precedente, le impostazioni in *MyXMLFile. XML* e *MyXMLFile*. `Environment` . i file *XML* vengono sottoposti a override dalle impostazioni in:</span><span class="sxs-lookup"><span data-stu-id="54933-848">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="54933-849">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="54933-849">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="54933-850">[Provider di configurazione della riga di comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="54933-850">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="54933-851">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene il seguente file *MyXMLFile. XML* :</span><span class="sxs-lookup"><span data-stu-id="54933-851">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="54933-852">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza diverse impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="54933-852">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="54933-853">La ripetizione di elementi che usano lo stesso nome di elemento funziona se si usa l'attributo `name` per distinguere gli elementi:</span><span class="sxs-lookup"><span data-stu-id="54933-853">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="54933-854">Il codice seguente legge il file di configurazione precedente e visualizza le chiavi e i valori:</span><span class="sxs-lookup"><span data-stu-id="54933-854">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="54933-855">È possibile usare attributi per fornire valori:</span><span class="sxs-lookup"><span data-stu-id="54933-855">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="54933-856">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="54933-856">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="54933-857">key:attribute</span><span class="sxs-lookup"><span data-stu-id="54933-857">key:attribute</span></span>
* <span data-ttu-id="54933-858">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="54933-858">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="54933-859">Provider di configurazione chiave per file</span><span class="sxs-lookup"><span data-stu-id="54933-859">Key-per-file configuration provider</span></span>

<span data-ttu-id="54933-860">Il <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa i file di una directory come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-860">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="54933-861">La chiave è il nome del file.</span><span class="sxs-lookup"><span data-stu-id="54933-861">The key is the file name.</span></span> <span data-ttu-id="54933-862">Il valore contiene il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="54933-862">The value contains the file's contents.</span></span> <span data-ttu-id="54933-863">Il provider di configurazione chiave per file viene usato negli scenari di hosting di Docker.</span><span class="sxs-lookup"><span data-stu-id="54933-863">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="54933-864">Per attivare la configurazione chiave-per-file, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="54933-864">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="54933-865">Il `directoryPath` per i file deve essere un percorso assoluto.</span><span class="sxs-lookup"><span data-stu-id="54933-865">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="54933-866">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="54933-866">Overloads permit specifying:</span></span>

* <span data-ttu-id="54933-867">Un delegato `Action<KeyPerFileConfigurationSource>` che configura l'origine.</span><span class="sxs-lookup"><span data-stu-id="54933-867">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="54933-868">Se la directory è facoltativa e il percorso della directory.</span><span class="sxs-lookup"><span data-stu-id="54933-868">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="54933-869">Il doppio carattere di sottolineatura (`__`) viene usato come delimitatore per le chiavi di configurazione nei nomi dei file.</span><span class="sxs-lookup"><span data-stu-id="54933-869">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="54933-870">Ad esempio, il nome di file `Logging__LogLevel__System` produce la chiave di configurazione `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="54933-870">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="54933-871">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="54933-871">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="54933-872">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="54933-872">Memory configuration provider</span></span>

<span data-ttu-id="54933-873">Il <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una raccolta in memoria come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-873">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="54933-874">Il codice seguente aggiunge una raccolta di memoria al sistema di configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-874">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="54933-875">Il codice seguente del [download dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) Visualizza le impostazioni di configurazione precedenti:</span><span class="sxs-lookup"><span data-stu-id="54933-875">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="54933-876">Nel codice precedente, `config.AddInMemoryCollection(Dict)` viene aggiunto dopo i [provider di configurazione predefiniti](#default).</span><span class="sxs-lookup"><span data-stu-id="54933-876">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="54933-877">Per un esempio di ordinamento dei provider di configurazione, vedere [provider di configurazione JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="54933-877">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="54933-878">Vedere [associare una matrice](#boa) per un altro esempio di utilizzo di `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="54933-878">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="54933-879">GetValue</span><span class="sxs-lookup"><span data-stu-id="54933-879">GetValue</span></span>

<span data-ttu-id="54933-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)estrae un singolo valore dalla configurazione con una chiave specificata e lo converte nel tipo specificato:</span><span class="sxs-lookup"><span data-stu-id="54933-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="54933-881">Nel codice precedente, se `NumberKey` non è stato trovato nella configurazione, viene usato il valore predefinito di `99` .</span><span class="sxs-lookup"><span data-stu-id="54933-881">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="54933-882">GetSection, GetChildren ed Exists</span><span class="sxs-lookup"><span data-stu-id="54933-882">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="54933-883">Per gli esempi che seguono, prendere in considerazione il seguente file *MySubsection. JSON* :</span><span class="sxs-lookup"><span data-stu-id="54933-883">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="54933-884">Il codice seguente aggiunge *MySubsection. JSON* ai provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-884">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="54933-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="54933-885">GetSection</span></span>

<span data-ttu-id="54933-886">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) restituisce una sottosezione di configurazione con la chiave della sottosezione specificata.</span><span class="sxs-lookup"><span data-stu-id="54933-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="54933-887">Il codice seguente restituisce i valori per `section1` :</span><span class="sxs-lookup"><span data-stu-id="54933-887">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="54933-888">Il codice seguente restituisce i valori per `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="54933-888">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="54933-889">`GetSection` non restituisce mai `null`.</span><span class="sxs-lookup"><span data-stu-id="54933-889">`GetSection` never returns `null`.</span></span> <span data-ttu-id="54933-890">Se non viene trovata una sezione corrispondente, viene restituita una `IConfigurationSection` vuota.</span><span class="sxs-lookup"><span data-stu-id="54933-890">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="54933-891">Quando `GetSection` restituisce una sezione corrispondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> non viene compilata.</span><span class="sxs-lookup"><span data-stu-id="54933-891">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="54933-892">Quando la sezione esiste, vengono restituiti <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.</span><span class="sxs-lookup"><span data-stu-id="54933-892">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="54933-893">GetChildren ed EXISTS</span><span class="sxs-lookup"><span data-stu-id="54933-893">GetChildren and Exists</span></span>

<span data-ttu-id="54933-894">Il codice seguente chiama [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) e restituisce i valori per `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="54933-894">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="54933-895">Il codice precedente chiama [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) per verificare che la sezione esista:</span><span class="sxs-lookup"><span data-stu-id="54933-895">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="54933-896">Associare una matrice</span><span class="sxs-lookup"><span data-stu-id="54933-896">Bind an array</span></span>

<span data-ttu-id="54933-897">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supporta l'associazione di matrici a oggetti usando gli indici di matrice nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-897">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="54933-898">Qualsiasi formato di matrice che espone un segmento di chiave numerica è in grado di associare array a una matrice di classi [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="54933-898">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="54933-899">Si consideri il file con *estensione JSON* dal [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="54933-899">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="54933-900">Il codice seguente aggiunge il file *Array. JSON* ai provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-900">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="54933-901">Il codice seguente legge la configurazione e Visualizza i valori:</span><span class="sxs-lookup"><span data-stu-id="54933-901">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="54933-902">Il codice precedente restituisce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="54933-902">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="54933-903">Nell'output precedente, index 3 ha un valore `value40` , corrispondente a `"4": "value40",` in *ArrayList. JSON*.</span><span class="sxs-lookup"><span data-stu-id="54933-903">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="54933-904">Gli indici di matrice associati sono continui e non sono associati all'indice della chiave di configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-904">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="54933-905">Il binder di configurazione non è in grado di associare valori null o di creare voci null negli oggetti associati</span><span class="sxs-lookup"><span data-stu-id="54933-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="54933-906">Il codice seguente carica la `array:entries` configurazione con il <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="54933-906">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="54933-907">Il codice seguente legge la configurazione in `arrayDict` `Dictionary` e Visualizza i valori:</span><span class="sxs-lookup"><span data-stu-id="54933-907">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="54933-908">Il codice precedente restituisce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="54933-908">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="54933-909">L'indice &num;3 nell'oggetto associato contiene i dati di configurazione per la chiave di configurazione `array:4` e il relativo valore `value4`.</span><span class="sxs-lookup"><span data-stu-id="54933-909">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="54933-910">Quando i dati di configurazione contenenti una matrice sono associati, gli indici di matrice nelle chiavi di configurazione vengono usati per scorrere i dati di configurazione durante la creazione dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="54933-910">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="54933-911">Un valore null non può essere mantenuto nei dati di configurazione e una voce con valore null non viene creata in un oggetto associato quando una matrice nelle chiavi di configurazione ignora uno o più indici.</span><span class="sxs-lookup"><span data-stu-id="54933-911">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="54933-912">È possibile specificare l'elemento di configurazione mancante per &num; l'indice 3 prima di eseguire il binding all' `ArrayExample` istanza da qualsiasi provider di configurazione che legga la &num; coppia chiave/valore dell'indice 3.</span><span class="sxs-lookup"><span data-stu-id="54933-912">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="54933-913">Si consideri il seguente file *valore3. JSON* dal Download di esempio:</span><span class="sxs-lookup"><span data-stu-id="54933-913">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="54933-914">Il codice seguente include la configurazione per *valore3. JSON* e `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="54933-914">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="54933-915">Il codice seguente legge la configurazione precedente e Visualizza i valori:</span><span class="sxs-lookup"><span data-stu-id="54933-915">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="54933-916">Il codice precedente restituisce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="54933-916">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="54933-917">I provider di configurazione personalizzati non devono implementare l'associazione di matrici.</span><span class="sxs-lookup"><span data-stu-id="54933-917">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="54933-918">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="54933-918">Custom configuration provider</span></span>

<span data-ttu-id="54933-919">L'app di esempio dimostra come creare un provider di configurazione di base che legge le coppie chiave-valore di configurazione da un database usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="54933-919">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="54933-920">Il provider ha le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="54933-920">The provider has the following characteristics:</span></span>

* <span data-ttu-id="54933-921">Il database in memoria di Entity Framework viene usato a scopo dimostrativo.</span><span class="sxs-lookup"><span data-stu-id="54933-921">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="54933-922">Per usare un database che richiede una stringa di connessione, implementare un `ConfigurationBuilder` secondario per fornire la stringa di connessione da un altro provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-922">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="54933-923">Il provider legge una tabella di database in una configurazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="54933-923">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="54933-924">Il provider non esegue una query sul database per ogni chiave.</span><span class="sxs-lookup"><span data-stu-id="54933-924">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="54933-925">Il ricaricamento in caso di modifica non è implementato, quindi l'aggiornamento del database dopo l'avvio dell'app non ha alcun effetto sulla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="54933-925">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="54933-926">Definire un'entità `EFConfigurationValue` per l'archiviazione dei valori di configurazione nel database.</span><span class="sxs-lookup"><span data-stu-id="54933-926">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="54933-927">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="54933-927">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="54933-928">Aggiungere `EFConfigurationContext` per archiviare i valori configurati e accedervi.</span><span class="sxs-lookup"><span data-stu-id="54933-928">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="54933-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="54933-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="54933-930">Creare una classe che implementi <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="54933-930">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="54933-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="54933-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="54933-932">Creare il provider di configurazione personalizzato ereditando da <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="54933-932">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="54933-933">Il provider di configurazione inizializza il database quando è vuoto.</span><span class="sxs-lookup"><span data-stu-id="54933-933">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="54933-934">Poiché le [chiavi di configurazione non fanno distinzione tra maiuscole](#keys)e minuscole, il dizionario utilizzato per inizializzare il database viene creato con l'operatore di confronto senza distinzione tra maiuscole e minuscole ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="54933-934">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="54933-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="54933-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="54933-936">Un metodo di estensione `AddEFConfiguration` consente di aggiungere l'origine di configurazione a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="54933-936">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="54933-937">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="54933-937">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="54933-938">L'esempio di codice seguente mostra come usare il `EFConfigurationProvider` personalizzato in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="54933-938">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="54933-939">Accedi alla configurazione all'avvio</span><span class="sxs-lookup"><span data-stu-id="54933-939">Access configuration in Startup</span></span>

<span data-ttu-id="54933-940">Il codice seguente consente di visualizzare i dati di configurazione nei `Startup` metodi:</span><span class="sxs-lookup"><span data-stu-id="54933-940">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="54933-941">Per un esempio di accesso alla configurazione usando metodi di servizio di avvio, vedere [Avvio dell'applicazione: Metodi pratici](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="54933-941">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="54933-942">Accedi alla configurazione nelle Razor pagine</span><span class="sxs-lookup"><span data-stu-id="54933-942">Access configuration in Razor Pages</span></span>

<span data-ttu-id="54933-943">Il codice seguente consente di visualizzare i dati di configurazione in una Razor pagina:</span><span class="sxs-lookup"><span data-stu-id="54933-943">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="54933-944">Nel codice seguente, `MyOptions` viene aggiunto al contenitore del servizio con <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associato alla configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-944">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="54933-945">Il markup seguente utilizza la [`@inject`](xref:mvc/views/razor#inject) Razor direttiva per risolvere e visualizzare i valori delle opzioni:</span><span class="sxs-lookup"><span data-stu-id="54933-945">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="54933-946">Accedere alla configurazione in un file di visualizzazione MVC</span><span class="sxs-lookup"><span data-stu-id="54933-946">Access configuration in a MVC view file</span></span>

<span data-ttu-id="54933-947">Il codice seguente consente di visualizzare i dati di configurazione in una visualizzazione MVC:</span><span class="sxs-lookup"><span data-stu-id="54933-947">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="54933-948">Configurare le opzioni con un delegato</span><span class="sxs-lookup"><span data-stu-id="54933-948">Configure options with a delegate</span></span>

<span data-ttu-id="54933-949">Le opzioni configurate in un delegato eseguono l'override dei valori impostati nei provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-949">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="54933-950">La configurazione delle opzioni con un delegato è illustrata nell'esempio 2 nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="54933-950">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="54933-951">Nel codice seguente <xref:Microsoft.Extensions.Options.IConfigureOptions%601> viene aggiunto un servizio al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="54933-951">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="54933-952">Usa un delegato per configurare i valori per `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="54933-952">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="54933-953">Il codice seguente consente di visualizzare i valori delle opzioni:</span><span class="sxs-lookup"><span data-stu-id="54933-953">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="54933-954">Nell'esempio precedente, i valori di `Option1` e `Option2` vengono specificati in *appSettings. JSON* e quindi sottoposti a override dal delegato configurato.</span><span class="sxs-lookup"><span data-stu-id="54933-954">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="54933-955">Host e configurazione delle app</span><span class="sxs-lookup"><span data-stu-id="54933-955">Host versus app configuration</span></span>

<span data-ttu-id="54933-956">Prima che l'app venga configurata e avviata, viene configurato e avviato un *host*.</span><span class="sxs-lookup"><span data-stu-id="54933-956">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="54933-957">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="54933-957">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="54933-958">Sia l'app che l'host vengono configurati tramite i provider di configurazione descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="54933-958">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="54933-959">Anche le coppie chiave-valore di configurazione dell'host sono incluse nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="54933-959">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="54933-960">Per altre informazioni su come vengono usati i provider di configurazione quando viene creato l'host e sugli effetti delle origini di configurazione sull'host di configurazione, vedere <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="54933-960">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="54933-961">Configurazione host predefinita</span><span class="sxs-lookup"><span data-stu-id="54933-961">Default host configuration</span></span>

<span data-ttu-id="54933-962">Per informazioni dettagliate sulla configurazione predefinita quando viene usato l'[host Web](xref:fundamentals/host/web-host), vedere la [versione di questo argomento per ASP.NET Core 2.2](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="54933-962">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="54933-963">La configurazione dell'host viene specificata da:</span><span class="sxs-lookup"><span data-stu-id="54933-963">Host configuration is provided from:</span></span>
  * <span data-ttu-id="54933-964">Variabili di ambiente con prefisso `DOTNET_` (ad esempio, `DOTNET_ENVIRONMENT` ) utilizzando il [provider di configurazione delle variabili di ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="54933-964">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="54933-965">Il prefisso (`DOTNET_`) viene rimosso al caricamento delle coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-965">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="54933-966">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="54933-966">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="54933-967">La configurazione predefinita dell'host Web viene stabilita (`ConfigureWebHostDefaults`) nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="54933-967">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="54933-968">Kestrel viene usato come server Web e configurato con i provider di configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="54933-968">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="54933-969">Aggiungere il middleware di filtro host.</span><span class="sxs-lookup"><span data-stu-id="54933-969">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="54933-970">Aggiungere il middleware delle intestazioni inoltrate se la variabile di ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è impostata su `true`.</span><span class="sxs-lookup"><span data-stu-id="54933-970">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="54933-971">Abilitare l'integrazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="54933-971">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="54933-972">Altra configurazione</span><span class="sxs-lookup"><span data-stu-id="54933-972">Other configuration</span></span>

<span data-ttu-id="54933-973">Questo argomento riguarda solo la *configurazione dell'app*.</span><span class="sxs-lookup"><span data-stu-id="54933-973">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="54933-974">Altri aspetti dell'esecuzione e dell'hosting di app ASP.NET Core sono configurati usando i file di configurazione non trattati in questo argomento:</span><span class="sxs-lookup"><span data-stu-id="54933-974">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="54933-975">*Launch. JSON* / *launchSettings. JSON* sono i file di configurazione degli strumenti per l'ambiente di sviluppo, descritti:</span><span class="sxs-lookup"><span data-stu-id="54933-975">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="54933-976">In <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="54933-976">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="54933-977">Nella documentazione in cui vengono usati i file per configurare ASP.NET Core app per scenari di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="54933-977">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="54933-978">*Web. config* è un file di configurazione del server, descritto negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="54933-978">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="54933-979">Per ulteriori informazioni sulla migrazione della configurazione dell'app da versioni precedenti di ASP.NET, vedere <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="54933-979">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="54933-980">Aggiungere la configurazione da un assembly esterno</span><span class="sxs-lookup"><span data-stu-id="54933-980">Add configuration from an external assembly</span></span>

<span data-ttu-id="54933-981">Un'implementazione <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> consente l'aggiunta di miglioramenti a un'app all'avvio, da un assembly esterno alla classe `Startup` dell'app.</span><span class="sxs-lookup"><span data-stu-id="54933-981">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="54933-982">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="54933-982">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54933-983">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="54933-983">Additional resources</span></span>

* [<span data-ttu-id="54933-984">Codice sorgente configurazione</span><span class="sxs-lookup"><span data-stu-id="54933-984">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="54933-985">La configurazione delle app in ASP.NET Core si basa su coppie chiave-valore stabilite dai *provider di configurazione*.</span><span class="sxs-lookup"><span data-stu-id="54933-985">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="54933-986">I provider di configurazione leggono i dati di configurazione in coppie chiave-valore da un'ampia gamma di origini di configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-986">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="54933-987">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="54933-987">Azure Key Vault</span></span>
* <span data-ttu-id="54933-988">Configurazione app di Azure</span><span class="sxs-lookup"><span data-stu-id="54933-988">Azure App Configuration</span></span>
* <span data-ttu-id="54933-989">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="54933-989">Command-line arguments</span></span>
* <span data-ttu-id="54933-990">Provider personalizzati (installati o creati)</span><span class="sxs-lookup"><span data-stu-id="54933-990">Custom providers (installed or created)</span></span>
* <span data-ttu-id="54933-991">File della directory</span><span class="sxs-lookup"><span data-stu-id="54933-991">Directory files</span></span>
* <span data-ttu-id="54933-992">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="54933-992">Environment variables</span></span>
* <span data-ttu-id="54933-993">Oggetti .NET in memoria</span><span class="sxs-lookup"><span data-stu-id="54933-993">In-memory .NET objects</span></span>
* <span data-ttu-id="54933-994">File di impostazioni</span><span class="sxs-lookup"><span data-stu-id="54933-994">Settings files</span></span>

<span data-ttu-id="54933-995">I pacchetti di configurazione per gli scenari di provider di configurazione comuni ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sono inclusi nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="54933-995">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="54933-996">Gli esempi di codice che seguono e quelli inclusi nell'app di esempio usano lo spazio dei nomi <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="54933-996">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="54933-997">Il *modello di opzioni* è un'estensione dei concetti di configurazione descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="54933-997">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="54933-998">Le opzioni usano le classi per rappresentare i gruppi di impostazioni correlate.</span><span class="sxs-lookup"><span data-stu-id="54933-998">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="54933-999">Per altre informazioni, vedere <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="54933-999">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="54933-1000">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54933-1000">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="54933-1001">Host e configurazione delle app</span><span class="sxs-lookup"><span data-stu-id="54933-1001">Host versus app configuration</span></span>

<span data-ttu-id="54933-1002">Prima che l'app venga configurata e avviata, viene configurato e avviato un *host*.</span><span class="sxs-lookup"><span data-stu-id="54933-1002">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="54933-1003">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="54933-1003">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="54933-1004">Sia l'app che l'host vengono configurati tramite i provider di configurazione descritti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="54933-1004">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="54933-1005">Anche le coppie chiave-valore di configurazione dell'host sono incluse nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="54933-1005">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="54933-1006">Per altre informazioni su come vengono usati i provider di configurazione quando viene creato l'host e sugli effetti delle origini di configurazione sull'host di configurazione, vedere <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="54933-1006">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="54933-1007">Altra configurazione</span><span class="sxs-lookup"><span data-stu-id="54933-1007">Other configuration</span></span>

<span data-ttu-id="54933-1008">Questo argomento riguarda solo la *configurazione dell'app*.</span><span class="sxs-lookup"><span data-stu-id="54933-1008">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="54933-1009">Altri aspetti dell'esecuzione e dell'hosting di app ASP.NET Core sono configurati usando i file di configurazione non trattati in questo argomento:</span><span class="sxs-lookup"><span data-stu-id="54933-1009">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="54933-1010">*Launch. JSON* / *launchSettings. JSON* sono i file di configurazione degli strumenti per l'ambiente di sviluppo, descritti:</span><span class="sxs-lookup"><span data-stu-id="54933-1010">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="54933-1011">In <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="54933-1011">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="54933-1012">Nella documentazione in cui vengono usati i file per configurare ASP.NET Core app per scenari di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="54933-1012">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="54933-1013">*Web. config* è un file di configurazione del server, descritto negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="54933-1013">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="54933-1014">Per ulteriori informazioni sulla migrazione della configurazione dell'app da versioni precedenti di ASP.NET, vedere <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="54933-1014">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="54933-1015">Configurazione predefinita</span><span class="sxs-lookup"><span data-stu-id="54933-1015">Default configuration</span></span>

<span data-ttu-id="54933-1016">Le app basate sui modelli [dotnet new](/dotnet/core/tools/dotnet-new) di ASP.NET Core chiamano <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> durante la compilazione di un host.</span><span class="sxs-lookup"><span data-stu-id="54933-1016">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="54933-1017">`CreateDefaultBuilder` fornisce la configurazione predefinita per l'app nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="54933-1017">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="54933-1018">La configurazione seguente si applica alle app che usano l'[host Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="54933-1018">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="54933-1019">Per informazioni dettagliate sulla configurazione predefinita quando viene usato l'[host generico](xref:fundamentals/host/generic-host), vedere la [versione più recente di questo argomento](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="54933-1019">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="54933-1020">La configurazione dell'host viene specificata da:</span><span class="sxs-lookup"><span data-stu-id="54933-1020">Host configuration is provided from:</span></span>
  * <span data-ttu-id="54933-1021">Variabili di ambiente con prefisso `ASPNETCORE_` (ad esempio `ASPNETCORE_ENVIRONMENT`) che usano il [provider di configurazione delle variabili di ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="54933-1021">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="54933-1022">Il prefisso (`ASPNETCORE_`) viene rimosso al caricamento delle coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-1022">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="54933-1023">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="54933-1023">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="54933-1024">La configurazione dell'app viene fornita da:</span><span class="sxs-lookup"><span data-stu-id="54933-1024">App configuration is provided from:</span></span>
  * <span data-ttu-id="54933-1025">*appsettings.json* mediante il [provider di configurazione dei file](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="54933-1025">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="54933-1026">*appsettings.{Ambiente}.json* mediante il [provider di configurazione dei file](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="54933-1026">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="54933-1027">[Strumento di gestione dei segreti](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development` usando l'assembly di ingresso.</span><span class="sxs-lookup"><span data-stu-id="54933-1027">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="54933-1028">Variabili di ambiente che usano il [provider di configurazione delle variabili di ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="54933-1028">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="54933-1029">Argomenti della riga di comando che usano il [provider di configurazione della riga di comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="54933-1029">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="54933-1030">Sicurezza</span><span class="sxs-lookup"><span data-stu-id="54933-1030">Security</span></span>

<span data-ttu-id="54933-1031">Per proteggere i dati di configurazione sensibili, adottare le pratiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="54933-1031">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="54933-1032">Non archiviare mai la password o altri dati sensibili nel codice del provider di configurazione o in file di configurazione di testo normale.</span><span class="sxs-lookup"><span data-stu-id="54933-1032">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="54933-1033">Non usare i segreti di produzione in ambienti di sviluppo o di test.</span><span class="sxs-lookup"><span data-stu-id="54933-1033">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="54933-1034">Specificare i segreti all'esterno del progetto in modo che non possano essere inavvertitamente inviati a un repository del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="54933-1034">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="54933-1035">Per altre informazioni, vedere gli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="54933-1035">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="54933-1036"><xref:security/app-secrets>&ndash;Include consigli sull'uso delle variabili di ambiente per archiviare dati riservati.</span><span class="sxs-lookup"><span data-stu-id="54933-1036"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="54933-1037">Secret Manager usa il provider di configurazione dei file per archiviare i segreti utente in un file JSON nel sistema locale.</span><span class="sxs-lookup"><span data-stu-id="54933-1037">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="54933-1038">Il provider di configurazione dei file è descritto più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="54933-1038">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="54933-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) archivia in modo sicuro i segreti delle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="54933-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="54933-1040">Per altre informazioni, vedere <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="54933-1040">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="54933-1041">Dati di configurazione gerarchici</span><span class="sxs-lookup"><span data-stu-id="54933-1041">Hierarchical configuration data</span></span>

<span data-ttu-id="54933-1042">L'API di configurazione è in grado di mantenere i dati di configurazione gerarchici rendendoli flat grazie all'uso di un delimitatore nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-1042">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="54933-1043">Nel file JSON seguente, esistono quattro chiavi in una gerarchia strutturata di due sezioni:</span><span class="sxs-lookup"><span data-stu-id="54933-1043">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="54933-1044">Quando il file viene letto nella configurazione, vengono create chiavi univoche per mantenere la struttura di dati gerarchici originale dell'origine di configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-1044">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="54933-1045">Le sezioni e le chiavi vengono rese flat usando due punti (`:`) per mantenere la struttura originale:</span><span class="sxs-lookup"><span data-stu-id="54933-1045">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="54933-1046">section0:key0</span><span class="sxs-lookup"><span data-stu-id="54933-1046">section0:key0</span></span>
* <span data-ttu-id="54933-1047">section0:key1</span><span class="sxs-lookup"><span data-stu-id="54933-1047">section0:key1</span></span>
* <span data-ttu-id="54933-1048">section1:key0</span><span class="sxs-lookup"><span data-stu-id="54933-1048">section1:key0</span></span>
* <span data-ttu-id="54933-1049">section1:key1</span><span class="sxs-lookup"><span data-stu-id="54933-1049">section1:key1</span></span>

<span data-ttu-id="54933-1050">I metodi <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sono disponibili per isolare le sezioni e gli elementi figlio di una sezione nei dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="54933-1051">Questi metodi sono descritti più avanti in [GetSection, GetChildren ed Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="54933-1051">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="54933-1052">Convenzioni</span><span class="sxs-lookup"><span data-stu-id="54933-1052">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="54933-1053">Origini e provider</span><span class="sxs-lookup"><span data-stu-id="54933-1053">Sources and providers</span></span>

<span data-ttu-id="54933-1054">All'avvio dell'app, le origini di configurazione vengono lette nell'ordine con cui sono specificati i rispettivi provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-1054">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="54933-1055">I provider di configurazione che implementano il rilevamento delle modifiche sono in grado di ricaricare la configurazione quando viene modificata un'impostazione sottostante.</span><span class="sxs-lookup"><span data-stu-id="54933-1055">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="54933-1056">Ad esempio, il provider di configurazione dei file (descritto più avanti in questo argomento) e il [provider di configurazione di Azure Key Vault](xref:security/key-vault-configuration) implementano il rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="54933-1056">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="54933-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> è disponibile nel contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) dell'app.</span><span class="sxs-lookup"><span data-stu-id="54933-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="54933-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration>può essere inserito in una Razor pagina <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> o MVC <xref:Microsoft.AspNetCore.Mvc.Controller> per ottenere la configurazione per la classe.</span><span class="sxs-lookup"><span data-stu-id="54933-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="54933-1059">Negli esempi seguenti il `_config` campo viene usato per accedere ai valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-1059">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="54933-1060">I provider di configurazione non possono usare l'inserimento delle dipendenze, perché non è disponibile quando vengono configurati dall'host.</span><span class="sxs-lookup"><span data-stu-id="54933-1060">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="54933-1061">Chiavi</span><span class="sxs-lookup"><span data-stu-id="54933-1061">Keys</span></span>

<span data-ttu-id="54933-1062">Le chiavi di configurazione adottano le convenzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="54933-1062">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="54933-1063">Per le chiavi non viene fatta distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="54933-1063">Keys are case-insensitive.</span></span> <span data-ttu-id="54933-1064">Ad esempio, `ConnectionString` e `connectionstring` vengono considerate chiavi equivalenti.</span><span class="sxs-lookup"><span data-stu-id="54933-1064">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="54933-1065">Se un valore per la stessa chiave viene impostato dallo stesso provider di configurazione o da provider diversi, viene usato l'ultimo valore impostato per la chiave.</span><span class="sxs-lookup"><span data-stu-id="54933-1065">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="54933-1066">Chiavi gerarchiche</span><span class="sxs-lookup"><span data-stu-id="54933-1066">Hierarchical keys</span></span>
  * <span data-ttu-id="54933-1067">Nell'ambito dell'API di configurazione, il separatore due punti (`:`) funziona in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="54933-1067">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="54933-1068">Nelle variabili di ambiente, un separatore due punti potrebbe non funzionare in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="54933-1068">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="54933-1069">Il doppio carattere di sottolineatura (`__`) è supportato da tutte le piattaforme e viene convertito automaticamente nei due punti.</span><span class="sxs-lookup"><span data-stu-id="54933-1069">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="54933-1070">In Azure Key Vault, le chiavi gerarchiche usano `--` (due trattini) come separatore.</span><span class="sxs-lookup"><span data-stu-id="54933-1070">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="54933-1071">Scrivere il codice per sostituire i trattini con i due punti quando i segreti vengono caricati nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="54933-1071">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="54933-1072">Il <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supporta l'associazione di matrici agli oggetti usando gli indici delle matrici nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-1072">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="54933-1073">L'associazione di matrici è descritta nella sezione [Associare una matrice a una classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="54933-1073">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="54933-1074">Valori</span><span class="sxs-lookup"><span data-stu-id="54933-1074">Values</span></span>

<span data-ttu-id="54933-1075">I valori di configurazione adottano le convenzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="54933-1075">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="54933-1076">I valori sono stringhe.</span><span class="sxs-lookup"><span data-stu-id="54933-1076">Values are strings.</span></span>
* <span data-ttu-id="54933-1077">I valori null non possono essere archiviati nella configurazione o associati a oggetti.</span><span class="sxs-lookup"><span data-stu-id="54933-1077">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="54933-1078">Providers</span><span class="sxs-lookup"><span data-stu-id="54933-1078">Providers</span></span>

<span data-ttu-id="54933-1079">La tabella seguente mostra i provider di configurazione disponibili per le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="54933-1079">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="54933-1080">Provider</span><span class="sxs-lookup"><span data-stu-id="54933-1080">Provider</span></span> | <span data-ttu-id="54933-1081">Fornisce la configurazione da&hellip;</span><span class="sxs-lookup"><span data-stu-id="54933-1081">Provides configuration from&hellip;</span></span> |
| ---
<span data-ttu-id="54933-1082">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1083">'Blazor'</span></span>
- <span data-ttu-id="54933-1084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1084">'Identity'</span></span>
- <span data-ttu-id="54933-1085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1085">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1086">'Razor'</span></span>
- <span data-ttu-id="54933-1087">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1088">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1089">'Blazor'</span></span>
- <span data-ttu-id="54933-1090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1090">'Identity'</span></span>
- <span data-ttu-id="54933-1091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1091">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1092">'Razor'</span></span>
- <span data-ttu-id="54933-1093">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1093">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-1094">---- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1095">'Blazor'</span></span>
- <span data-ttu-id="54933-1096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1096">'Identity'</span></span>
- <span data-ttu-id="54933-1097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1097">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1098">'Razor'</span></span>
- <span data-ttu-id="54933-1099">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1099">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1100">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1101">'Blazor'</span></span>
- <span data-ttu-id="54933-1102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1102">'Identity'</span></span>
- <span data-ttu-id="54933-1103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1103">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1104">'Razor'</span></span>
- <span data-ttu-id="54933-1105">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1106">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1107">'Blazor'</span></span>
- <span data-ttu-id="54933-1108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1108">'Identity'</span></span>
- <span data-ttu-id="54933-1109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1109">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1110">'Razor'</span></span>
- <span data-ttu-id="54933-1111">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1112">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1113">'Blazor'</span></span>
- <span data-ttu-id="54933-1114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1114">'Identity'</span></span>
- <span data-ttu-id="54933-1115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1115">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1116">'Razor'</span></span>
- <span data-ttu-id="54933-1117">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1118">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1119">'Blazor'</span></span>
- <span data-ttu-id="54933-1120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1120">'Identity'</span></span>
- <span data-ttu-id="54933-1121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1121">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1122">'Razor'</span></span>
- <span data-ttu-id="54933-1123">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1124">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1125">'Blazor'</span></span>
- <span data-ttu-id="54933-1126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1126">'Identity'</span></span>
- <span data-ttu-id="54933-1127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1127">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1128">'Razor'</span></span>
- <span data-ttu-id="54933-1129">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1130">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1131">'Blazor'</span></span>
- <span data-ttu-id="54933-1132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1132">'Identity'</span></span>
- <span data-ttu-id="54933-1133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1133">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1134">'Razor'</span></span>
- <span data-ttu-id="54933-1135">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1136">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1137">'Blazor'</span></span>
- <span data-ttu-id="54933-1138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1138">'Identity'</span></span>
- <span data-ttu-id="54933-1139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1139">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1140">'Razor'</span></span>
- <span data-ttu-id="54933-1141">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1141">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1142">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1143">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1143">'Blazor'</span></span>
- <span data-ttu-id="54933-1144">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1144">'Identity'</span></span>
- <span data-ttu-id="54933-1145">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1145">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1146">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1146">'Razor'</span></span>
- <span data-ttu-id="54933-1147">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1147">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1148">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1149">'Blazor'</span></span>
- <span data-ttu-id="54933-1150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1150">'Identity'</span></span>
- <span data-ttu-id="54933-1151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1151">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1152">'Razor'</span></span>
- <span data-ttu-id="54933-1153">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1154">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1155">'Blazor'</span></span>
- <span data-ttu-id="54933-1156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1156">'Identity'</span></span>
- <span data-ttu-id="54933-1157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1157">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1158">'Razor'</span></span>
- <span data-ttu-id="54933-1159">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1160">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1161">'Blazor'</span></span>
- <span data-ttu-id="54933-1162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1162">'Identity'</span></span>
- <span data-ttu-id="54933-1163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1163">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1164">'Razor'</span></span>
- <span data-ttu-id="54933-1165">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1166">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1167">'Blazor'</span></span>
- <span data-ttu-id="54933-1168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1168">'Identity'</span></span>
- <span data-ttu-id="54933-1169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1169">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1170">'Razor'</span></span>
- <span data-ttu-id="54933-1171">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1172">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1173">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1173">'Blazor'</span></span>
- <span data-ttu-id="54933-1174">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1174">'Identity'</span></span>
- <span data-ttu-id="54933-1175">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1175">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1176">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1176">'Razor'</span></span>
- <span data-ttu-id="54933-1177">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1177">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1178">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1179">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1179">'Blazor'</span></span>
- <span data-ttu-id="54933-1180">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1180">'Identity'</span></span>
- <span data-ttu-id="54933-1181">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1181">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1182">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1182">'Razor'</span></span>
- <span data-ttu-id="54933-1183">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1183">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-1184">------------------ | | [Provider di configurazione Azure Key Vault](xref:security/key-vault-configuration) (argomenti*sulla sicurezza* ) | Azure Key Vault | | [Provider di configurazione app Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentazione di Azure) | Configurazione app Azure | | [Provider di configurazione della riga di comando](#command-line-configuration-provider) | Parametri della riga di comando | | [Provider di configurazione personalizzato](#custom-configuration-provider) | Origine personalizzata | | [Provider di configurazione delle variabili di ambiente](#environment-variables-configuration-provider) | Variabili di ambiente | | [Provider di configurazione file](#file-configuration-provider) | File (INI, JSON, XML) | | [Provider di configurazione chiave per file](#key-per-file-configuration-provider) | File di directory | | [Provider di configurazione della memoria](#memory-configuration-provider) | Raccolte in memoria | | [Segreti utente (gestione Secret)](xref:security/app-secrets) (argomenti*sulla sicurezza* ) | File nella directory del profilo utente |</span><span class="sxs-lookup"><span data-stu-id="54933-1184">------------------ | | [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics) | Azure Key Vault | | [Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation) | Azure App Configuration | | [Command-line Configuration Provider](#command-line-configuration-provider) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables Configuration Provider](#environment-variables-configuration-provider) | Environment variables | | [File Configuration Provider](#file-configuration-provider) | Files (INI, JSON, XML) | | [Key-per-file Configuration Provider](#key-per-file-configuration-provider) | Directory files | | [Memory Configuration Provider](#memory-configuration-provider) | In-memory collections | | [User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics) | File in the user profile directory |</span></span>

<span data-ttu-id="54933-1185">Le origini di configurazione vengono lette nell'ordine in cui vengono specificati i rispetti provider di configurazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="54933-1185">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="54933-1186">I provider di configurazione descritti in questo argomento sono descritti in ordine alfabetico, non nell'ordine in cui il codice li dispone.</span><span class="sxs-lookup"><span data-stu-id="54933-1186">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="54933-1187">Ordinare i provider di configurazione nel codice in base alle priorità per le origini di configurazione sottostanti richieste dall'app.</span><span class="sxs-lookup"><span data-stu-id="54933-1187">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="54933-1188">Una sequenza tipica di provider di configurazione è:</span><span class="sxs-lookup"><span data-stu-id="54933-1188">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="54933-1189">Files (*appSettings. JSON*, *appSettings. { Environment}. JSON*, dove `{Environment}` è l'ambiente host corrente dell'app)</span><span class="sxs-lookup"><span data-stu-id="54933-1189">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="54933-1190">Insieme di credenziali chiave Azure</span><span class="sxs-lookup"><span data-stu-id="54933-1190">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="54933-1191">[Segreti utente (Secret Manager)](xref:security/app-secrets) (solo nell'ambiente di sviluppo)</span><span class="sxs-lookup"><span data-stu-id="54933-1191">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="54933-1192">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="54933-1192">Environment variables</span></span>
1. <span data-ttu-id="54933-1193">Argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="54933-1193">Command-line arguments</span></span>

<span data-ttu-id="54933-1194">È pratica comune posizionare il provider di configurazione della riga di comando per ultimo in una serie di provider per consentire agli argomenti della riga di comando di sostituire la configurazione impostata da altri provider.</span><span class="sxs-lookup"><span data-stu-id="54933-1194">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="54933-1195">La sequenza di provider precedente viene utilizzata quando viene inizializzato un nuovo generatore host con `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="54933-1195">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="54933-1196">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="54933-1196">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="54933-1197">Configurare il generatore di host con UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="54933-1197">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="54933-1198">Per configurare il generatore di host, chiamare <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> sul generatore di host con la configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-1198">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="54933-1199">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="54933-1199">ConfigureAppConfiguration</span></span>

<span data-ttu-id="54933-1200">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare i provider di configurazione dell'app, oltre a quelli aggiunti automaticamente da `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="54933-1200">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="54933-1201">Sostituire la configurazione precedente con argomenti della riga di comando</span><span class="sxs-lookup"><span data-stu-id="54933-1201">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="54933-1202">Per fornire la configurazione dell'app che può essere sostituita con argomenti della riga di comando, chiamare `AddCommandLine` per ultimo:</span><span class="sxs-lookup"><span data-stu-id="54933-1202">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="54933-1203">Rimuovere i provider aggiunti da CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="54933-1203">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="54933-1204">Per rimuovere i provider aggiunti da `CreateDefaultBuilder` , chiamare prima [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) in [IConfigurationBuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="54933-1204">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="54933-1205">Utilizzare la configurazione durante l'avvio dell'app</span><span class="sxs-lookup"><span data-stu-id="54933-1205">Consume configuration during app startup</span></span>

<span data-ttu-id="54933-1206">La configurazione fornita all'app in `ConfigureAppConfiguration` è disponibile durante l'avvio dell'app, incluso `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="54933-1206">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="54933-1207">Per altre informazioni, vedere la sezione [Accedere alla configurazione durante l'avvio](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="54933-1207">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="54933-1208">Provider di configurazione della riga di comando</span><span class="sxs-lookup"><span data-stu-id="54933-1208">Command-line Configuration Provider</span></span>

<span data-ttu-id="54933-1209"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carica la configurazione da coppie chiave-valore di argomenti della riga di comando in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="54933-1209">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="54933-1210">Per attivare la configurazione della riga di comando, metodo di estensione <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> viene chiamato su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="54933-1210">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="54933-1211">`AddCommandLine` viene chiamato automaticamente quando viene chiamato il metodo `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="54933-1211">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="54933-1212">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="54933-1212">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="54933-1213">`CreateDefaultBuilder` carica anche:</span><span class="sxs-lookup"><span data-stu-id="54933-1213">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="54933-1214">Una configurazione facoltativa dai file *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="54933-1214">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="54933-1215">[Segreti utente (Secret Manager)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="54933-1215">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="54933-1216">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="54933-1216">Environment variables.</span></span>

<span data-ttu-id="54933-1217">`CreateDefaultBuilder` aggiunge il provider di configurazione della riga di comando per ultimo.</span><span class="sxs-lookup"><span data-stu-id="54933-1217">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="54933-1218">Gli argomenti della riga di comando passati in fase di esecuzione sostituiscono la configurazione impostata dagli altri provider.</span><span class="sxs-lookup"><span data-stu-id="54933-1218">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="54933-1219">`CreateDefaultBuilder` opera durante la creazione dell'host.</span><span class="sxs-lookup"><span data-stu-id="54933-1219">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="54933-1220">Pertanto, la configurazione della riga di comando attivata da `CreateDefaultBuilder` può influire sul modo in cui l'host viene configurato.</span><span class="sxs-lookup"><span data-stu-id="54933-1220">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="54933-1221">Per le app basate sui modelli di ASP.NET Core, `AddCommandLine` è già stato chiamato da `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="54933-1221">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="54933-1222">Per aggiungere altri provider di configurazione e mantenere la possibilità di sostituire la configurazione di tali provider con argomenti della riga di comando, chiamare i provider aggiuntivi dell'app in `ConfigureAppConfiguration` e quindi chiamare `AddCommandLine` per ultimo.</span><span class="sxs-lookup"><span data-stu-id="54933-1222">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="54933-1223">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="54933-1223">**Example**</span></span>

<span data-ttu-id="54933-1224">L'app di esempio consente di sfruttare il metodo di servizio statico `CreateDefaultBuilder` per creare l'host, che include una chiamata a <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="54933-1224">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="54933-1225">Aprire un prompt dei comandi nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="54933-1225">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="54933-1226">Fornire un argomento della riga di comando per il comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="54933-1226">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="54933-1227">Dopo che l'app è in esecuzione, aprire un browser per l'app all'indirizzo `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="54933-1227">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="54933-1228">Notare che l'output contiene la coppia chiave-valore per l'argomento della riga di comando di configurazione fornito per `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="54933-1228">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="54933-1229">Argomenti</span><span class="sxs-lookup"><span data-stu-id="54933-1229">Arguments</span></span>

<span data-ttu-id="54933-1230">Il valore deve seguire un segno di uguale (`=`) o la chiave deve avere un prefisso (`--` o `/`) quando il valore segue uno spazio.</span><span class="sxs-lookup"><span data-stu-id="54933-1230">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="54933-1231">Il valore non è necessario se viene usato un segno di uguale, ad esempio `CommandLineKey=`.</span><span class="sxs-lookup"><span data-stu-id="54933-1231">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="54933-1232">Prefisso chiave</span><span class="sxs-lookup"><span data-stu-id="54933-1232">Key prefix</span></span>               | <span data-ttu-id="54933-1233">Esempio</span><span class="sxs-lookup"><span data-stu-id="54933-1233">Example</span></span>                                                |
| ---
<span data-ttu-id="54933-1234">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1235">'Blazor'</span></span>
- <span data-ttu-id="54933-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1236">'Identity'</span></span>
- <span data-ttu-id="54933-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1238">'Razor'</span></span>
- <span data-ttu-id="54933-1239">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1240">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1241">'Blazor'</span></span>
- <span data-ttu-id="54933-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1242">'Identity'</span></span>
- <span data-ttu-id="54933-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1244">'Razor'</span></span>
- <span data-ttu-id="54933-1245">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1246">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1247">'Blazor'</span></span>
- <span data-ttu-id="54933-1248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1248">'Identity'</span></span>
- <span data-ttu-id="54933-1249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1249">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1250">'Razor'</span></span>
- <span data-ttu-id="54933-1251">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1252">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1253">'Blazor'</span></span>
- <span data-ttu-id="54933-1254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1254">'Identity'</span></span>
- <span data-ttu-id="54933-1255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1255">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1256">'Razor'</span></span>
- <span data-ttu-id="54933-1257">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1258">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1259">'Blazor'</span></span>
- <span data-ttu-id="54933-1260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1260">'Identity'</span></span>
- <span data-ttu-id="54933-1261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1261">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1262">'Razor'</span></span>
- <span data-ttu-id="54933-1263">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1264">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1265">'Blazor'</span></span>
- <span data-ttu-id="54933-1266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1266">'Identity'</span></span>
- <span data-ttu-id="54933-1267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1267">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1268">'Razor'</span></span>
- <span data-ttu-id="54933-1269">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1270">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1271">'Blazor'</span></span>
- <span data-ttu-id="54933-1272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1272">'Identity'</span></span>
- <span data-ttu-id="54933-1273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1273">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1274">'Razor'</span></span>
- <span data-ttu-id="54933-1275">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1276">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1277">'Blazor'</span></span>
- <span data-ttu-id="54933-1278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1278">'Identity'</span></span>
- <span data-ttu-id="54933-1279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1279">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1280">'Razor'</span></span>
- <span data-ttu-id="54933-1281">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1282">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1283">'Blazor'</span></span>
- <span data-ttu-id="54933-1284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1284">'Identity'</span></span>
- <span data-ttu-id="54933-1285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1285">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1286">'Razor'</span></span>
- <span data-ttu-id="54933-1287">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1288">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1289">'Blazor'</span></span>
- <span data-ttu-id="54933-1290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1290">'Identity'</span></span>
- <span data-ttu-id="54933-1291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1291">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1292">'Razor'</span></span>
- <span data-ttu-id="54933-1293">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1293">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-1294">------------ | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1295">'Blazor'</span></span>
- <span data-ttu-id="54933-1296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1296">'Identity'</span></span>
- <span data-ttu-id="54933-1297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1297">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1298">'Razor'</span></span>
- <span data-ttu-id="54933-1299">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1300">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1301">'Blazor'</span></span>
- <span data-ttu-id="54933-1302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1302">'Identity'</span></span>
- <span data-ttu-id="54933-1303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1303">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1304">'Razor'</span></span>
- <span data-ttu-id="54933-1305">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1306">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1307">'Blazor'</span></span>
- <span data-ttu-id="54933-1308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1308">'Identity'</span></span>
- <span data-ttu-id="54933-1309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1309">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1310">'Razor'</span></span>
- <span data-ttu-id="54933-1311">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1312">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1313">'Blazor'</span></span>
- <span data-ttu-id="54933-1314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1314">'Identity'</span></span>
- <span data-ttu-id="54933-1315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1315">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1316">'Razor'</span></span>
- <span data-ttu-id="54933-1317">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1318">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1319">'Blazor'</span></span>
- <span data-ttu-id="54933-1320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1320">'Identity'</span></span>
- <span data-ttu-id="54933-1321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1321">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1322">'Razor'</span></span>
- <span data-ttu-id="54933-1323">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1324">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1325">'Blazor'</span></span>
- <span data-ttu-id="54933-1326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1326">'Identity'</span></span>
- <span data-ttu-id="54933-1327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1327">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1328">'Razor'</span></span>
- <span data-ttu-id="54933-1329">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1330">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1331">'Blazor'</span></span>
- <span data-ttu-id="54933-1332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1332">'Identity'</span></span>
- <span data-ttu-id="54933-1333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1333">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1334">'Razor'</span></span>
- <span data-ttu-id="54933-1335">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1336">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1337">'Blazor'</span></span>
- <span data-ttu-id="54933-1338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1338">'Identity'</span></span>
- <span data-ttu-id="54933-1339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1339">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1340">'Razor'</span></span>
- <span data-ttu-id="54933-1341">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1342">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1343">'Blazor'</span></span>
- <span data-ttu-id="54933-1344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1344">'Identity'</span></span>
- <span data-ttu-id="54933-1345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1345">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1346">'Razor'</span></span>
- <span data-ttu-id="54933-1347">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1348">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1349">'Blazor'</span></span>
- <span data-ttu-id="54933-1350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1350">'Identity'</span></span>
- <span data-ttu-id="54933-1351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1351">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1352">'Razor'</span></span>
- <span data-ttu-id="54933-1353">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1354">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1355">'Blazor'</span></span>
- <span data-ttu-id="54933-1356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1356">'Identity'</span></span>
- <span data-ttu-id="54933-1357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1357">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1358">'Razor'</span></span>
- <span data-ttu-id="54933-1359">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1360">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1361">'Blazor'</span></span>
- <span data-ttu-id="54933-1362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1362">'Identity'</span></span>
- <span data-ttu-id="54933-1363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1363">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1364">'Razor'</span></span>
- <span data-ttu-id="54933-1365">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1366">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1367">'Blazor'</span></span>
- <span data-ttu-id="54933-1368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1368">'Identity'</span></span>
- <span data-ttu-id="54933-1369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1369">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1370">'Razor'</span></span>
- <span data-ttu-id="54933-1371">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1372">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1373">'Blazor'</span></span>
- <span data-ttu-id="54933-1374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1374">'Identity'</span></span>
- <span data-ttu-id="54933-1375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1375">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1376">'Razor'</span></span>
- <span data-ttu-id="54933-1377">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1378">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1379">'Blazor'</span></span>
- <span data-ttu-id="54933-1380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1380">'Identity'</span></span>
- <span data-ttu-id="54933-1381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1381">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1382">'Razor'</span></span>
- <span data-ttu-id="54933-1383">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1384">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1385">'Blazor'</span></span>
- <span data-ttu-id="54933-1386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1386">'Identity'</span></span>
- <span data-ttu-id="54933-1387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1387">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1388">'Razor'</span></span>
- <span data-ttu-id="54933-1389">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1390">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1391">'Blazor'</span></span>
- <span data-ttu-id="54933-1392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1392">'Identity'</span></span>
- <span data-ttu-id="54933-1393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1393">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1394">'Razor'</span></span>
- <span data-ttu-id="54933-1395">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1396">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1397">'Blazor'</span></span>
- <span data-ttu-id="54933-1398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1398">'Identity'</span></span>
- <span data-ttu-id="54933-1399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1399">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1400">'Razor'</span></span>
- <span data-ttu-id="54933-1401">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1402">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1403">'Blazor'</span></span>
- <span data-ttu-id="54933-1404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1404">'Identity'</span></span>
- <span data-ttu-id="54933-1405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1405">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1406">'Razor'</span></span>
- <span data-ttu-id="54933-1407">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1408">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1409">'Blazor'</span></span>
- <span data-ttu-id="54933-1410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1410">'Identity'</span></span>
- <span data-ttu-id="54933-1411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1411">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1412">'Razor'</span></span>
- <span data-ttu-id="54933-1413">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1414">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1415">'Blazor'</span></span>
- <span data-ttu-id="54933-1416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1416">'Identity'</span></span>
- <span data-ttu-id="54933-1417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1417">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1418">'Razor'</span></span>
- <span data-ttu-id="54933-1419">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1420">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1421">'Blazor'</span></span>
- <span data-ttu-id="54933-1422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1422">'Identity'</span></span>
- <span data-ttu-id="54933-1423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1423">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1424">'Razor'</span></span>
- <span data-ttu-id="54933-1425">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1426">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1427">'Blazor'</span></span>
- <span data-ttu-id="54933-1428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1428">'Identity'</span></span>
- <span data-ttu-id="54933-1429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1429">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1430">'Razor'</span></span>
- <span data-ttu-id="54933-1431">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1432">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1433">'Blazor'</span></span>
- <span data-ttu-id="54933-1434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1434">'Identity'</span></span>
- <span data-ttu-id="54933-1435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1435">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1436">'Razor'</span></span>
- <span data-ttu-id="54933-1437">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1438">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1439">'Blazor'</span></span>
- <span data-ttu-id="54933-1440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1440">'Identity'</span></span>
- <span data-ttu-id="54933-1441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1441">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1442">'Razor'</span></span>
- <span data-ttu-id="54933-1443">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1443">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-1444">--------------------------- | | Nessun prefisso | `CommandLineKey1=value1`                               |
| Due trattini ( `--` ) | `--CommandLineKey2=value2` , `--CommandLineKey2 value2` |
 | Barra ( `/` ) | `/CommandLineKey3=value3` ,`/CommandLineKey3 value3`   |</span><span class="sxs-lookup"><span data-stu-id="54933-1444">--------------------------- | | No prefix                | `CommandLineKey1=value1`                               |
| Two dashes (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Forward slash (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span></span>

<span data-ttu-id="54933-1445">Nello stesso comando, non mischiare coppie chiave-valore di argomenti della riga di comando che usano un segno di uguale con coppie chiave-valore che usano uno spazio.</span><span class="sxs-lookup"><span data-stu-id="54933-1445">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="54933-1446">Comandi di esempio:</span><span class="sxs-lookup"><span data-stu-id="54933-1446">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="54933-1447">Mapping di sostituzione</span><span class="sxs-lookup"><span data-stu-id="54933-1447">Switch mappings</span></span>

<span data-ttu-id="54933-1448">I mapping di sostituzione consentono di usare la logica di sostituzione del nome della chiave.</span><span class="sxs-lookup"><span data-stu-id="54933-1448">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="54933-1449">Quando si compila manualmente la configurazione con un oggetto <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , fornire un dizionario di sostituzioni switch al <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodo.</span><span class="sxs-lookup"><span data-stu-id="54933-1449">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="54933-1450">Quando viene utilizzato il dizionario dei mapping di sostituzione, nel dizionario viene controllata la presenza di una chiave corrispondente alla chiave fornita da un argomento della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="54933-1450">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="54933-1451">Se la chiave della riga di comando viene trovata nel dizionario, il valore del dizionario (la sostituzione della chiave) viene passato nuovamente per impostare la coppia chiave-valore nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="54933-1451">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="54933-1452">Un mapping di sostituzione è necessario per le chiavi della riga di comando con un trattino singolo (`-`) come prefisso.</span><span class="sxs-lookup"><span data-stu-id="54933-1452">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="54933-1453">Regole principali del dizionario dei mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="54933-1453">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="54933-1454">Le sostituzioni devono iniziare con un trattino (`-`) o un trattino doppio (`--`).</span><span class="sxs-lookup"><span data-stu-id="54933-1454">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="54933-1455">Il dizionario dei mapping di sostituzione non deve contenere chiavi duplicate.</span><span class="sxs-lookup"><span data-stu-id="54933-1455">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="54933-1456">Creare un dizionario dei mapping di sostituzione.</span><span class="sxs-lookup"><span data-stu-id="54933-1456">Create a switch mappings dictionary.</span></span> <span data-ttu-id="54933-1457">Nell'esempio seguente vengono creati due mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="54933-1457">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="54933-1458">Quando viene creato l'host, chiamare `AddCommandLine` con il dizionario dei mapping di sostituzione:</span><span class="sxs-lookup"><span data-stu-id="54933-1458">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="54933-1459">Per le app che usano i mapping di sostituzione, la chiamata a `CreateDefaultBuilder` non deve passare argomenti.</span><span class="sxs-lookup"><span data-stu-id="54933-1459">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="54933-1460">La chiamata `AddCommandLine` del metodo `CreateDefaultBuilder` non include sostituzioni mappate e non è possibile passare il dizionario dei mapping di sostituzione a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="54933-1460">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="54933-1461">La soluzione consiste nel non passare gli argomenti a `CreateDefaultBuilder` consentendo invece al metodo `AddCommandLine` del metodo `ConfigurationBuilder` di elaborare entrambi gli argomenti e il dizionario dei mapping di sostituzione.</span><span class="sxs-lookup"><span data-stu-id="54933-1461">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="54933-1462">Il dizionario dei mapping di sostituzione creato contiene i dati visualizzati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="54933-1462">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="54933-1463">Chiave</span><span class="sxs-lookup"><span data-stu-id="54933-1463">Key</span></span>       | <span data-ttu-id="54933-1464">valore</span><span class="sxs-lookup"><span data-stu-id="54933-1464">Value</span></span>             |
| ---
<span data-ttu-id="54933-1465">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1466">'Blazor'</span></span>
- <span data-ttu-id="54933-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1467">'Identity'</span></span>
- <span data-ttu-id="54933-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1469">'Razor'</span></span>
- <span data-ttu-id="54933-1470">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1471">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1472">'Blazor'</span></span>
- <span data-ttu-id="54933-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1473">'Identity'</span></span>
- <span data-ttu-id="54933-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1475">'Razor'</span></span>
- <span data-ttu-id="54933-1476">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1476">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-1477">----- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1478">'Blazor'</span></span>
- <span data-ttu-id="54933-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1479">'Identity'</span></span>
- <span data-ttu-id="54933-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1481">'Razor'</span></span>
- <span data-ttu-id="54933-1482">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1483">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1484">'Blazor'</span></span>
- <span data-ttu-id="54933-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1485">'Identity'</span></span>
- <span data-ttu-id="54933-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1487">'Razor'</span></span>
- <span data-ttu-id="54933-1488">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1489">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1490">'Blazor'</span></span>
- <span data-ttu-id="54933-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1491">'Identity'</span></span>
- <span data-ttu-id="54933-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1493">'Razor'</span></span>
- <span data-ttu-id="54933-1494">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1495">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1496">'Blazor'</span></span>
- <span data-ttu-id="54933-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1497">'Identity'</span></span>
- <span data-ttu-id="54933-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1499">'Razor'</span></span>
- <span data-ttu-id="54933-1500">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1501">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1502">'Blazor'</span></span>
- <span data-ttu-id="54933-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1503">'Identity'</span></span>
- <span data-ttu-id="54933-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1505">'Razor'</span></span>
- <span data-ttu-id="54933-1506">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1507">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1508">'Blazor'</span></span>
- <span data-ttu-id="54933-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1509">'Identity'</span></span>
- <span data-ttu-id="54933-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1511">'Razor'</span></span>
- <span data-ttu-id="54933-1512">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1512">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span><span class="sxs-lookup"><span data-stu-id="54933-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span></span>

<span data-ttu-id="54933-1514">Se le chiavi con mapping di sostituzione vengono usate all'avvio dell'app, la configurazione riceve il valore di configurazione per la chiave fornita dal dizionario:</span><span class="sxs-lookup"><span data-stu-id="54933-1514">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="54933-1515">Dopo aver eseguito il comando precedente, la configurazione contiene i valori mostrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="54933-1515">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="54933-1516">Chiave</span><span class="sxs-lookup"><span data-stu-id="54933-1516">Key</span></span>               | <span data-ttu-id="54933-1517">valore</span><span class="sxs-lookup"><span data-stu-id="54933-1517">Value</span></span>    |
| ---
<span data-ttu-id="54933-1518">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1519">'Blazor'</span></span>
- <span data-ttu-id="54933-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1520">'Identity'</span></span>
- <span data-ttu-id="54933-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1522">'Razor'</span></span>
- <span data-ttu-id="54933-1523">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1523">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1524">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1525">'Blazor'</span></span>
- <span data-ttu-id="54933-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1526">'Identity'</span></span>
- <span data-ttu-id="54933-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1528">'Razor'</span></span>
- <span data-ttu-id="54933-1529">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1530">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1531">'Blazor'</span></span>
- <span data-ttu-id="54933-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1532">'Identity'</span></span>
- <span data-ttu-id="54933-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1534">'Razor'</span></span>
- <span data-ttu-id="54933-1535">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1536">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1537">'Blazor'</span></span>
- <span data-ttu-id="54933-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1538">'Identity'</span></span>
- <span data-ttu-id="54933-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1540">'Razor'</span></span>
- <span data-ttu-id="54933-1541">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1542">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1543">'Blazor'</span></span>
- <span data-ttu-id="54933-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1544">'Identity'</span></span>
- <span data-ttu-id="54933-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1546">'Razor'</span></span>
- <span data-ttu-id="54933-1547">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1548">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1549">'Blazor'</span></span>
- <span data-ttu-id="54933-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1550">'Identity'</span></span>
- <span data-ttu-id="54933-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1552">'Razor'</span></span>
- <span data-ttu-id="54933-1553">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1553">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-1554">--------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1555">'Blazor'</span></span>
- <span data-ttu-id="54933-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1556">'Identity'</span></span>
- <span data-ttu-id="54933-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1558">'Razor'</span></span>
- <span data-ttu-id="54933-1559">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1560">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1561">'Blazor'</span></span>
- <span data-ttu-id="54933-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1562">'Identity'</span></span>
- <span data-ttu-id="54933-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1564">'Razor'</span></span>
- <span data-ttu-id="54933-1565">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1565">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span><span class="sxs-lookup"><span data-stu-id="54933-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span></span>

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="54933-1567">Provider di configurazione delle variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="54933-1567">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="54933-1568"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carica la configurazione da coppie chiave-valore di variabili di ambiente in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="54933-1568">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="54933-1569">Per attivare la configurazione delle variabili di ambiente, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="54933-1569">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="54933-1570">[App Azure servizio](https://azure.microsoft.com/services/app-service/) consente di impostare le variabili di ambiente nel portale di Azure che possono eseguire l'override della configurazione dell'app usando il provider di configurazione delle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="54933-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="54933-1571">Per altre informazioni, vedere [App di Azure: Eseguire l'override della configurazione delle app usando il portale di Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="54933-1571">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="54933-1572">`AddEnvironmentVariables` consente di caricare variabili di ambiente con prefisso `ASPNETCORE_` per la [configurazione host](#host-versus-app-configuration) quando viene inizializzato un nuovo generatore di host con l'[host Web](xref:fundamentals/host/web-host) e viene chiamato `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="54933-1572">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="54933-1573">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="54933-1573">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="54933-1574">`CreateDefaultBuilder` carica anche:</span><span class="sxs-lookup"><span data-stu-id="54933-1574">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="54933-1575">Configurazione delle app dalle variabili di ambiente senza prefisso chiamando `AddEnvironmentVariables` senza prefisso.</span><span class="sxs-lookup"><span data-stu-id="54933-1575">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="54933-1576">Una configurazione facoltativa dai file *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="54933-1576">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="54933-1577">[Segreti utente (Secret Manager)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="54933-1577">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="54933-1578">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="54933-1578">Command-line arguments.</span></span>

<span data-ttu-id="54933-1579">Il provider di configurazione delle variabili di ambiente viene chiamato dopo aver stabilito la configurazione dai segreti utente e dai file *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="54933-1579">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="54933-1580">La chiamata del provider in questa posizione consente alle variabili di ambiente lette in fase di esecuzione di sostituire la configurazione impostata dai segreti utente e dai file *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="54933-1580">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="54933-1581">Per fornire la configurazione dell'app da altre variabili di ambiente, chiamare i provider aggiuntivi dell'app in `ConfigureAppConfiguration` e chiamare `AddEnvironmentVariables` con il prefisso:</span><span class="sxs-lookup"><span data-stu-id="54933-1581">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="54933-1582">Chiamare `AddEnvironmentVariables` Last per consentire alle variabili di ambiente con il prefisso specificato di eseguire l'override dei valori di altri provider.</span><span class="sxs-lookup"><span data-stu-id="54933-1582">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="54933-1583">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="54933-1583">**Example**</span></span>

<span data-ttu-id="54933-1584">L'app di esempio consente di sfruttare il metodo di servizio statico `CreateDefaultBuilder` per creare l'host, che include una chiamata a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="54933-1584">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="54933-1585">Eseguire l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="54933-1585">Run the sample app.</span></span> <span data-ttu-id="54933-1586">Aprire un browser per l'app all'indirizzo `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="54933-1586">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="54933-1587">Notare che l'output contiene la coppia chiave-valore per la variabile di ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="54933-1587">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="54933-1588">Il valore riflette l'ambiente in cui l'app è in esecuzione, in genere `Development` durante l'esecuzione locale.</span><span class="sxs-lookup"><span data-stu-id="54933-1588">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="54933-1589">Per limitare l'elenco delle variabili di ambiente restituito dall'app, l'app filtra le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="54933-1589">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="54933-1590">Vedere il file *Pages/Index.cshtml.cs* dell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="54933-1590">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="54933-1591">Per esporre tutte le variabili di ambiente disponibili per l'app, modificare `FilteredConfiguration` in *pages/index. cshtml. cs* come riportato di seguito:</span><span class="sxs-lookup"><span data-stu-id="54933-1591">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="54933-1592">Prefissi</span><span class="sxs-lookup"><span data-stu-id="54933-1592">Prefixes</span></span>

<span data-ttu-id="54933-1593">Le variabili di ambiente caricate nella configurazione dell'app vengono filtrate quando si specifica un prefisso per il `AddEnvironmentVariables` metodo.</span><span class="sxs-lookup"><span data-stu-id="54933-1593">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="54933-1594">Ad esempio, per filtrare le variabili di ambiente in base al prefisso `CUSTOM_`, fornire il prefisso al provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-1594">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="54933-1595">Il prefisso viene rimosso quando vengono create le coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-1595">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="54933-1596">Quando viene creato il generatore di host, la configurazione dell'host viene fornita dalle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="54933-1596">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="54933-1597">Per altre informazioni sul prefisso usato per queste variabili di ambiente, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="54933-1597">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="54933-1598">**Prefissi della stringa di connessione**</span><span class="sxs-lookup"><span data-stu-id="54933-1598">**Connection string prefixes**</span></span>

<span data-ttu-id="54933-1599">L'API di configurazione prevede regole di elaborazione speciali per quattro variabili di ambiente di stringa di connessione coinvolte nella configurazione di stringhe di connessione di Azure per l'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="54933-1599">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="54933-1600">Le variabili di ambiente con i prefissi indicati nella tabella vengono caricate nell'app se non viene fornito alcun prefisso a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="54933-1600">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="54933-1601">Prefisso della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="54933-1601">Connection string prefix</span></span> | <span data-ttu-id="54933-1602">Provider</span><span class="sxs-lookup"><span data-stu-id="54933-1602">Provider</span></span> |
| ---
<span data-ttu-id="54933-1603">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1604">'Blazor'</span></span>
- <span data-ttu-id="54933-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1605">'Identity'</span></span>
- <span data-ttu-id="54933-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1607">'Razor'</span></span>
- <span data-ttu-id="54933-1608">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1609">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1610">'Blazor'</span></span>
- <span data-ttu-id="54933-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1611">'Identity'</span></span>
- <span data-ttu-id="54933-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1613">'Razor'</span></span>
- <span data-ttu-id="54933-1614">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1615">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1616">'Blazor'</span></span>
- <span data-ttu-id="54933-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1617">'Identity'</span></span>
- <span data-ttu-id="54933-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1619">'Razor'</span></span>
- <span data-ttu-id="54933-1620">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1621">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1622">'Blazor'</span></span>
- <span data-ttu-id="54933-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1623">'Identity'</span></span>
- <span data-ttu-id="54933-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1625">'Razor'</span></span>
- <span data-ttu-id="54933-1626">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1627">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1628">'Blazor'</span></span>
- <span data-ttu-id="54933-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1629">'Identity'</span></span>
- <span data-ttu-id="54933-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1631">'Razor'</span></span>
- <span data-ttu-id="54933-1632">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1633">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1634">'Blazor'</span></span>
- <span data-ttu-id="54933-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1635">'Identity'</span></span>
- <span data-ttu-id="54933-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1637">'Razor'</span></span>
- <span data-ttu-id="54933-1638">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1639">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1640">'Blazor'</span></span>
- <span data-ttu-id="54933-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1641">'Identity'</span></span>
- <span data-ttu-id="54933-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1643">'Razor'</span></span>
- <span data-ttu-id="54933-1644">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1645">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1646">'Blazor'</span></span>
- <span data-ttu-id="54933-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1647">'Identity'</span></span>
- <span data-ttu-id="54933-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1649">'Razor'</span></span>
- <span data-ttu-id="54933-1650">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1651">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1652">'Blazor'</span></span>
- <span data-ttu-id="54933-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1653">'Identity'</span></span>
- <span data-ttu-id="54933-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1655">'Razor'</span></span>
- <span data-ttu-id="54933-1656">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1657">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1658">'Blazor'</span></span>
- <span data-ttu-id="54933-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1659">'Identity'</span></span>
- <span data-ttu-id="54933-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1661">'Razor'</span></span>
- <span data-ttu-id="54933-1662">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-1663">------------ | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1664">'Blazor'</span></span>
- <span data-ttu-id="54933-1665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1665">'Identity'</span></span>
- <span data-ttu-id="54933-1666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1666">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1667">'Razor'</span></span>
- <span data-ttu-id="54933-1668">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1669">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1670">'Blazor'</span></span>
- <span data-ttu-id="54933-1671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1671">'Identity'</span></span>
- <span data-ttu-id="54933-1672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1672">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1673">'Razor'</span></span>
- <span data-ttu-id="54933-1674">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1674">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-1675">---- | | `CUSTOMCONNSTR_` | Provider personalizzato | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
 MySQL | `SQLAZURECONNSTR_` | SQL Server del [database SQL di Azure](https://azure.microsoft.com/services/sql-database/) |
 | `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/)|</span><span class="sxs-lookup"><span data-stu-id="54933-1675">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="54933-1676">Quando una variabile di ambiente viene individuata e caricata nella configurazione con uno qualsiasi dei quattro prefissi indicati nella tabella:</span><span class="sxs-lookup"><span data-stu-id="54933-1676">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="54933-1677">La chiave di configurazione viene creata rimuovendo il prefisso della variabile di ambiente e aggiungendo una sezione per la chiave di configurazione (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="54933-1677">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="54933-1678">Viene creata una nuova coppia chiave-valore della configurazione che rappresenta il provider di connessione al database (ad eccezione di `CUSTOMCONNSTR_`, che non ha un provider dichiarato).</span><span class="sxs-lookup"><span data-stu-id="54933-1678">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="54933-1679">Chiave della variabile di ambiente</span><span class="sxs-lookup"><span data-stu-id="54933-1679">Environment variable key</span></span> | <span data-ttu-id="54933-1680">Chiave di configurazione convertita</span><span class="sxs-lookup"><span data-stu-id="54933-1680">Converted configuration key</span></span> | <span data-ttu-id="54933-1681">Voce di configurazione del provider</span><span class="sxs-lookup"><span data-stu-id="54933-1681">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="54933-1682">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1683">'Blazor'</span></span>
- <span data-ttu-id="54933-1684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1684">'Identity'</span></span>
- <span data-ttu-id="54933-1685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1685">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1686">'Razor'</span></span>
- <span data-ttu-id="54933-1687">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1688">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1689">'Blazor'</span></span>
- <span data-ttu-id="54933-1690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1690">'Identity'</span></span>
- <span data-ttu-id="54933-1691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1691">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1692">'Razor'</span></span>
- <span data-ttu-id="54933-1693">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1694">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1695">'Blazor'</span></span>
- <span data-ttu-id="54933-1696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1696">'Identity'</span></span>
- <span data-ttu-id="54933-1697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1697">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1698">'Razor'</span></span>
- <span data-ttu-id="54933-1699">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1700">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1701">'Blazor'</span></span>
- <span data-ttu-id="54933-1702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1702">'Identity'</span></span>
- <span data-ttu-id="54933-1703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1703">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1704">'Razor'</span></span>
- <span data-ttu-id="54933-1705">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1706">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1707">'Blazor'</span></span>
- <span data-ttu-id="54933-1708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1708">'Identity'</span></span>
- <span data-ttu-id="54933-1709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1709">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1710">'Razor'</span></span>
- <span data-ttu-id="54933-1711">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1712">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1713">'Blazor'</span></span>
- <span data-ttu-id="54933-1714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1714">'Identity'</span></span>
- <span data-ttu-id="54933-1715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1715">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1716">'Razor'</span></span>
- <span data-ttu-id="54933-1717">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1718">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1719">'Blazor'</span></span>
- <span data-ttu-id="54933-1720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1720">'Identity'</span></span>
- <span data-ttu-id="54933-1721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1721">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1722">'Razor'</span></span>
- <span data-ttu-id="54933-1723">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1724">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1725">'Blazor'</span></span>
- <span data-ttu-id="54933-1726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1726">'Identity'</span></span>
- <span data-ttu-id="54933-1727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1727">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1728">'Razor'</span></span>
- <span data-ttu-id="54933-1729">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1730">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1731">'Blazor'</span></span>
- <span data-ttu-id="54933-1732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1732">'Identity'</span></span>
- <span data-ttu-id="54933-1733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1733">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1734">'Razor'</span></span>
- <span data-ttu-id="54933-1735">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1736">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1737">'Blazor'</span></span>
- <span data-ttu-id="54933-1738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1738">'Identity'</span></span>
- <span data-ttu-id="54933-1739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1739">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1740">'Razor'</span></span>
- <span data-ttu-id="54933-1741">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1741">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-1742">------------ | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1743">'Blazor'</span></span>
- <span data-ttu-id="54933-1744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1744">'Identity'</span></span>
- <span data-ttu-id="54933-1745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1745">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1746">'Razor'</span></span>
- <span data-ttu-id="54933-1747">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1748">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1749">'Blazor'</span></span>
- <span data-ttu-id="54933-1750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1750">'Identity'</span></span>
- <span data-ttu-id="54933-1751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1751">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1752">'Razor'</span></span>
- <span data-ttu-id="54933-1753">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1754">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1755">'Blazor'</span></span>
- <span data-ttu-id="54933-1756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1756">'Identity'</span></span>
- <span data-ttu-id="54933-1757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1757">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1758">'Razor'</span></span>
- <span data-ttu-id="54933-1759">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1760">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1761">'Blazor'</span></span>
- <span data-ttu-id="54933-1762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1762">'Identity'</span></span>
- <span data-ttu-id="54933-1763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1763">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1764">'Razor'</span></span>
- <span data-ttu-id="54933-1765">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1766">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1767">'Blazor'</span></span>
- <span data-ttu-id="54933-1768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1768">'Identity'</span></span>
- <span data-ttu-id="54933-1769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1769">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1770">'Razor'</span></span>
- <span data-ttu-id="54933-1771">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1772">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1773">'Blazor'</span></span>
- <span data-ttu-id="54933-1774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1774">'Identity'</span></span>
- <span data-ttu-id="54933-1775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1775">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1776">'Razor'</span></span>
- <span data-ttu-id="54933-1777">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1778">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1779">'Blazor'</span></span>
- <span data-ttu-id="54933-1780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1780">'Identity'</span></span>
- <span data-ttu-id="54933-1781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1781">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1782">'Razor'</span></span>
- <span data-ttu-id="54933-1783">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1784">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1785">'Blazor'</span></span>
- <span data-ttu-id="54933-1786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1786">'Identity'</span></span>
- <span data-ttu-id="54933-1787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1787">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1788">'Razor'</span></span>
- <span data-ttu-id="54933-1789">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1790">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1791">'Blazor'</span></span>
- <span data-ttu-id="54933-1792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1792">'Identity'</span></span>
- <span data-ttu-id="54933-1793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1793">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1794">'Razor'</span></span>
- <span data-ttu-id="54933-1795">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1796">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1797">'Blazor'</span></span>
- <span data-ttu-id="54933-1798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1798">'Identity'</span></span>
- <span data-ttu-id="54933-1799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1799">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1800">'Razor'</span></span>
- <span data-ttu-id="54933-1801">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1802">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1803">'Blazor'</span></span>
- <span data-ttu-id="54933-1804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1804">'Identity'</span></span>
- <span data-ttu-id="54933-1805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1805">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1806">'Razor'</span></span>
- <span data-ttu-id="54933-1807">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1807">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-1808">-------------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1809">'Blazor'</span></span>
- <span data-ttu-id="54933-1810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1810">'Identity'</span></span>
- <span data-ttu-id="54933-1811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1811">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1812">'Razor'</span></span>
- <span data-ttu-id="54933-1813">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1814">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1815">'Blazor'</span></span>
- <span data-ttu-id="54933-1816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1816">'Identity'</span></span>
- <span data-ttu-id="54933-1817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1817">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1818">'Razor'</span></span>
- <span data-ttu-id="54933-1819">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1820">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1821">'Blazor'</span></span>
- <span data-ttu-id="54933-1822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1822">'Identity'</span></span>
- <span data-ttu-id="54933-1823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1823">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1824">'Razor'</span></span>
- <span data-ttu-id="54933-1825">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1826">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1827">'Blazor'</span></span>
- <span data-ttu-id="54933-1828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1828">'Identity'</span></span>
- <span data-ttu-id="54933-1829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1829">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1830">'Razor'</span></span>
- <span data-ttu-id="54933-1831">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1832">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1833">'Blazor'</span></span>
- <span data-ttu-id="54933-1834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1834">'Identity'</span></span>
- <span data-ttu-id="54933-1835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1835">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1836">'Razor'</span></span>
- <span data-ttu-id="54933-1837">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1838">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1839">'Blazor'</span></span>
- <span data-ttu-id="54933-1840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1840">'Identity'</span></span>
- <span data-ttu-id="54933-1841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1841">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1842">'Razor'</span></span>
- <span data-ttu-id="54933-1843">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1844">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1845">'Blazor'</span></span>
- <span data-ttu-id="54933-1846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1846">'Identity'</span></span>
- <span data-ttu-id="54933-1847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1847">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1848">'Razor'</span></span>
- <span data-ttu-id="54933-1849">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1850">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1851">'Blazor'</span></span>
- <span data-ttu-id="54933-1852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1852">'Identity'</span></span>
- <span data-ttu-id="54933-1853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1853">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1854">'Razor'</span></span>
- <span data-ttu-id="54933-1855">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1856">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1857">'Blazor'</span></span>
- <span data-ttu-id="54933-1858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1858">'Identity'</span></span>
- <span data-ttu-id="54933-1859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1859">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1860">'Razor'</span></span>
- <span data-ttu-id="54933-1861">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1862">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1863">'Blazor'</span></span>
- <span data-ttu-id="54933-1864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1864">'Identity'</span></span>
- <span data-ttu-id="54933-1865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1865">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1866">'Razor'</span></span>
- <span data-ttu-id="54933-1867">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1868">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1869">'Blazor'</span></span>
- <span data-ttu-id="54933-1870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1870">'Identity'</span></span>
- <span data-ttu-id="54933-1871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1871">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1872">'Razor'</span></span>
- <span data-ttu-id="54933-1873">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1874">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1875">'Blazor'</span></span>
- <span data-ttu-id="54933-1876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1876">'Identity'</span></span>
- <span data-ttu-id="54933-1877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1877">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1878">'Razor'</span></span>
- <span data-ttu-id="54933-1879">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1879">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1880">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1881">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1881">'Blazor'</span></span>
- <span data-ttu-id="54933-1882">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1882">'Identity'</span></span>
- <span data-ttu-id="54933-1883">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1883">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1884">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1884">'Razor'</span></span>
- <span data-ttu-id="54933-1885">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1885">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1886">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1887">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1887">'Blazor'</span></span>
- <span data-ttu-id="54933-1888">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1888">'Identity'</span></span>
- <span data-ttu-id="54933-1889">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1889">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1890">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1890">'Razor'</span></span>
- <span data-ttu-id="54933-1891">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1891">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1892">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1893">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1893">'Blazor'</span></span>
- <span data-ttu-id="54933-1894">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1894">'Identity'</span></span>
- <span data-ttu-id="54933-1895">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1895">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1896">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1896">'Razor'</span></span>
- <span data-ttu-id="54933-1897">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1897">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1898">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1899">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1899">'Blazor'</span></span>
- <span data-ttu-id="54933-1900">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1900">'Identity'</span></span>
- <span data-ttu-id="54933-1901">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1901">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1902">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1902">'Razor'</span></span>
- <span data-ttu-id="54933-1903">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1903">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1904">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1905">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1905">'Blazor'</span></span>
- <span data-ttu-id="54933-1906">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1906">'Identity'</span></span>
- <span data-ttu-id="54933-1907">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1907">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1908">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1908">'Razor'</span></span>
- <span data-ttu-id="54933-1909">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1909">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1910">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1911">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1911">'Blazor'</span></span>
- <span data-ttu-id="54933-1912">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1912">'Identity'</span></span>
- <span data-ttu-id="54933-1913">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1913">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1914">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1914">'Razor'</span></span>
- <span data-ttu-id="54933-1915">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1915">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1916">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1917">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1917">'Blazor'</span></span>
- <span data-ttu-id="54933-1918">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1918">'Identity'</span></span>
- <span data-ttu-id="54933-1919">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1919">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1920">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1920">'Razor'</span></span>
- <span data-ttu-id="54933-1921">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1921">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1922">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1923">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1923">'Blazor'</span></span>
- <span data-ttu-id="54933-1924">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1924">'Identity'</span></span>
- <span data-ttu-id="54933-1925">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1925">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1926">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1926">'Razor'</span></span>
- <span data-ttu-id="54933-1927">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1927">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1928">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1929">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1929">'Blazor'</span></span>
- <span data-ttu-id="54933-1930">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1930">'Identity'</span></span>
- <span data-ttu-id="54933-1931">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1931">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1932">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1932">'Razor'</span></span>
- <span data-ttu-id="54933-1933">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1933">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1934">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1935">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1935">'Blazor'</span></span>
- <span data-ttu-id="54933-1936">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1936">'Identity'</span></span>
- <span data-ttu-id="54933-1937">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1937">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1938">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1938">'Razor'</span></span>
- <span data-ttu-id="54933-1939">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1939">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1940">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1941">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1941">'Blazor'</span></span>
- <span data-ttu-id="54933-1942">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1942">'Identity'</span></span>
- <span data-ttu-id="54933-1943">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1943">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1944">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1944">'Razor'</span></span>
- <span data-ttu-id="54933-1945">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1945">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1946">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1947">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1947">'Blazor'</span></span>
- <span data-ttu-id="54933-1948">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1948">'Identity'</span></span>
- <span data-ttu-id="54933-1949">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1949">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1950">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1950">'Razor'</span></span>
- <span data-ttu-id="54933-1951">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1951">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1952">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1953">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1953">'Blazor'</span></span>
- <span data-ttu-id="54933-1954">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1954">'Identity'</span></span>
- <span data-ttu-id="54933-1955">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1955">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1956">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1956">'Razor'</span></span>
- <span data-ttu-id="54933-1957">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1957">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1958">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1959">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1959">'Blazor'</span></span>
- <span data-ttu-id="54933-1960">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1960">'Identity'</span></span>
- <span data-ttu-id="54933-1961">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1961">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1962">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1962">'Razor'</span></span>
- <span data-ttu-id="54933-1963">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1963">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1964">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1965">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1965">'Blazor'</span></span>
- <span data-ttu-id="54933-1966">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1966">'Identity'</span></span>
- <span data-ttu-id="54933-1967">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1967">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1968">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1968">'Razor'</span></span>
- <span data-ttu-id="54933-1969">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1969">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1970">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1971">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1971">'Blazor'</span></span>
- <span data-ttu-id="54933-1972">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1972">'Identity'</span></span>
- <span data-ttu-id="54933-1973">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1973">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1974">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1974">'Razor'</span></span>
- <span data-ttu-id="54933-1975">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1975">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1976">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1977">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1977">'Blazor'</span></span>
- <span data-ttu-id="54933-1978">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1978">'Identity'</span></span>
- <span data-ttu-id="54933-1979">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1979">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1980">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1980">'Razor'</span></span>
- <span data-ttu-id="54933-1981">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1981">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1982">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1983">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1983">'Blazor'</span></span>
- <span data-ttu-id="54933-1984">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1984">'Identity'</span></span>
- <span data-ttu-id="54933-1985">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1985">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1986">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1986">'Razor'</span></span>
- <span data-ttu-id="54933-1987">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1987">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1988">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1989">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1989">'Blazor'</span></span>
- <span data-ttu-id="54933-1990">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1990">'Identity'</span></span>
- <span data-ttu-id="54933-1991">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1991">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1992">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1992">'Razor'</span></span>
- <span data-ttu-id="54933-1993">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1993">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-1994">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-1995">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-1995">'Blazor'</span></span>
- <span data-ttu-id="54933-1996">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-1996">'Identity'</span></span>
- <span data-ttu-id="54933-1997">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-1997">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-1998">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-1998">'Razor'</span></span>
- <span data-ttu-id="54933-1999">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-1999">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2000">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2001">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2001">'Blazor'</span></span>
- <span data-ttu-id="54933-2002">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2002">'Identity'</span></span>
- <span data-ttu-id="54933-2003">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2003">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2004">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2004">'Razor'</span></span>
- <span data-ttu-id="54933-2005">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2005">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2006">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2007">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2007">'Blazor'</span></span>
- <span data-ttu-id="54933-2008">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2008">'Identity'</span></span>
- <span data-ttu-id="54933-2009">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2009">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2010">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2010">'Razor'</span></span>
- <span data-ttu-id="54933-2011">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2011">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2012">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2013">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2013">'Blazor'</span></span>
- <span data-ttu-id="54933-2014">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2014">'Identity'</span></span>
- <span data-ttu-id="54933-2015">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2015">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2016">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2016">'Razor'</span></span>
- <span data-ttu-id="54933-2017">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2017">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2018">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2019">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2019">'Blazor'</span></span>
- <span data-ttu-id="54933-2020">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2020">'Identity'</span></span>
- <span data-ttu-id="54933-2021">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2021">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2022">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2022">'Razor'</span></span>
- <span data-ttu-id="54933-2023">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2023">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2024">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2025">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2025">'Blazor'</span></span>
- <span data-ttu-id="54933-2026">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2026">'Identity'</span></span>
- <span data-ttu-id="54933-2027">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2027">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2028">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2028">'Razor'</span></span>
- <span data-ttu-id="54933-2029">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2029">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Voce di configurazione non creata.</span><span class="sxs-lookup"><span data-stu-id="54933-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="54933-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Chiave: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="54933-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="54933-2032">Valore: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`   |  `ConnectionStrings:{KEY}`   | Chiave: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="54933-2032">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="54933-2033">Valore: `System.Data.SqlClient` | | `SQLCONNSTR_{KEY}`        |  `ConnectionStrings:{KEY}`   | Chiave: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="54933-2033">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="54933-2034">Valore`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="54933-2034">Value: `System.Data.SqlClient`  |</span></span>

<span data-ttu-id="54933-2035">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="54933-2035">**Example**</span></span>

<span data-ttu-id="54933-2036">Nel server viene creata una variabile di ambiente della stringa di connessione personalizzata:</span><span class="sxs-lookup"><span data-stu-id="54933-2036">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="54933-2037">Nome &ndash;`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="54933-2037">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="54933-2038">Valore &ndash; di`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="54933-2038">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="54933-2039">Se `IConfiguration` viene inserito e assegnato a un campo denominato `_config` , leggere il valore:</span><span class="sxs-lookup"><span data-stu-id="54933-2039">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="54933-2040">Provider di configurazione dei file</span><span class="sxs-lookup"><span data-stu-id="54933-2040">File Configuration Provider</span></span>

<span data-ttu-id="54933-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> è la classe base per il caricamento della configurazione dal file system.</span><span class="sxs-lookup"><span data-stu-id="54933-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="54933-2042">I provider di configurazione seguenti sono dedicati per specifici tipi di file:</span><span class="sxs-lookup"><span data-stu-id="54933-2042">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="54933-2043">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="54933-2043">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="54933-2044">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="54933-2044">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="54933-2045">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="54933-2045">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="54933-2046">Provider di configurazione INI</span><span class="sxs-lookup"><span data-stu-id="54933-2046">INI Configuration Provider</span></span>

<span data-ttu-id="54933-2047"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carica la configurazione da coppie chiave-valore di file INI in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="54933-2047">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="54933-2048">Per attivare la configurazione di file INI, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="54933-2048">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="54933-2049">I due punti possono essere usati come delimitatore di sezione nella configurazione di file INI.</span><span class="sxs-lookup"><span data-stu-id="54933-2049">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="54933-2050">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="54933-2050">Overloads permit specifying:</span></span>

* <span data-ttu-id="54933-2051">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="54933-2051">Whether the file is optional.</span></span>
* <span data-ttu-id="54933-2052">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="54933-2052">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="54933-2053">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider> usato per accedere al file.</span><span class="sxs-lookup"><span data-stu-id="54933-2053">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="54933-2054">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="54933-2054">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="54933-2055">Un esempio generico di un file di configurazione INI:</span><span class="sxs-lookup"><span data-stu-id="54933-2055">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="54933-2056">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="54933-2056">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="54933-2057">section0:key0</span><span class="sxs-lookup"><span data-stu-id="54933-2057">section0:key0</span></span>
* <span data-ttu-id="54933-2058">section0:key1</span><span class="sxs-lookup"><span data-stu-id="54933-2058">section0:key1</span></span>
* <span data-ttu-id="54933-2059">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="54933-2059">section1:subsection:key</span></span>
* <span data-ttu-id="54933-2060">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="54933-2060">section2:subsection0:key</span></span>
* <span data-ttu-id="54933-2061">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="54933-2061">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="54933-2062">Provider di configurazione JSON</span><span class="sxs-lookup"><span data-stu-id="54933-2062">JSON Configuration Provider</span></span>

<span data-ttu-id="54933-2063">Il <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carica la configurazione da coppie chiave-valore di file JSON in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="54933-2063">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="54933-2064">Per attivare la configurazione di file JSON, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="54933-2064">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="54933-2065">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="54933-2065">Overloads permit specifying:</span></span>

* <span data-ttu-id="54933-2066">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="54933-2066">Whether the file is optional.</span></span>
* <span data-ttu-id="54933-2067">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="54933-2067">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="54933-2068">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider> usato per accedere al file.</span><span class="sxs-lookup"><span data-stu-id="54933-2068">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="54933-2069">`AddJsonFile`viene chiamato automaticamente due volte quando viene inizializzato un nuovo generatore host con `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="54933-2069">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="54933-2070">Il metodo viene chiamato per caricare la configurazione da:</span><span class="sxs-lookup"><span data-stu-id="54933-2070">The method is called to load configuration from:</span></span>

* <span data-ttu-id="54933-2071">*appsettings.json* &ndash; Questo file viene letto per primo.</span><span class="sxs-lookup"><span data-stu-id="54933-2071">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="54933-2072">La versione dell'ambiente del file può sostituire i valori forniti dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="54933-2072">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="54933-2073">*appSettings. {Environment}. JSON* &ndash; la versione dell'ambiente del file viene caricata in base a [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="54933-2073">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="54933-2074">Per altre informazioni, vedere la sezione [Configurazione predefinita](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="54933-2074">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="54933-2075">`CreateDefaultBuilder` carica anche:</span><span class="sxs-lookup"><span data-stu-id="54933-2075">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="54933-2076">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="54933-2076">Environment variables.</span></span>
* <span data-ttu-id="54933-2077">[Segreti utente (Secret Manager)](xref:security/app-secrets) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="54933-2077">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="54933-2078">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="54933-2078">Command-line arguments.</span></span>

<span data-ttu-id="54933-2079">Il provider di configurazione JSON viene stabilito per primo.</span><span class="sxs-lookup"><span data-stu-id="54933-2079">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="54933-2080">I segreti utente, le variabili di ambiente e gli argomenti della riga di comando sostituiscono quindi la configurazione impostata dai file *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="54933-2080">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="54933-2081">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app per file diversi da *appsettings.json* e *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="54933-2081">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="54933-2082">**Esempio**</span><span class="sxs-lookup"><span data-stu-id="54933-2082">**Example**</span></span>

<span data-ttu-id="54933-2083">L'app di esempio sfrutta il metodo di convenienza statica `CreateDefaultBuilder` per compilare l'host, che include due chiamate a `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="54933-2083">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="54933-2084">La prima chiamata a `AddJsonFile` carica la configurazione da *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="54933-2084">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="54933-2085">La seconda chiamata a `AddJsonFile` carica la configurazione da *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="54933-2085">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="54933-2086">Per *appSettings. Development. JSON* nell'app di esempio, viene caricato il file seguente:</span><span class="sxs-lookup"><span data-stu-id="54933-2086">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="54933-2087">Eseguire l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="54933-2087">Run the sample app.</span></span> <span data-ttu-id="54933-2088">Aprire un browser per l'app all'indirizzo `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="54933-2088">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="54933-2089">L'output contiene coppie chiave-valore per la configurazione basata sull'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="54933-2089">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="54933-2090">Il livello di registrazione della chiave `Logging:LogLevel:Default` è `Debug` quando si esegue l'app nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="54933-2090">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="54933-2091">Eseguire di nuovo l'app di esempio nell'ambiente di produzione:</span><span class="sxs-lookup"><span data-stu-id="54933-2091">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="54933-2092">Aprire il file *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="54933-2092">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="54933-2093">Nel `ConfigurationSample` profilo, modificare il valore della variabile di `ASPNETCORE_ENVIRONMENT` ambiente in `Production` .</span><span class="sxs-lookup"><span data-stu-id="54933-2093">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="54933-2094">Salvare il file ed eseguire l'app con `dotnet run` in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="54933-2094">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="54933-2095">Impostazioni in *appSettings. Development. JSON* non sostituisce più le impostazioni in *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="54933-2095">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="54933-2096">Il livello di registrazione per la chiave `Logging:LogLevel:Default` è `Warning` .</span><span class="sxs-lookup"><span data-stu-id="54933-2096">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="54933-2097">Provider di configurazione XML</span><span class="sxs-lookup"><span data-stu-id="54933-2097">XML Configuration Provider</span></span>

<span data-ttu-id="54933-2098">Il <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carica la configurazione da coppie chiave-valore di file XML in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="54933-2098">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="54933-2099">Per attivare la configurazione di file XML, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="54933-2099">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="54933-2100">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="54933-2100">Overloads permit specifying:</span></span>

* <span data-ttu-id="54933-2101">Se il file è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="54933-2101">Whether the file is optional.</span></span>
* <span data-ttu-id="54933-2102">Se la configurazione viene ricaricata se viene modificato il file.</span><span class="sxs-lookup"><span data-stu-id="54933-2102">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="54933-2103">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider> usato per accedere al file.</span><span class="sxs-lookup"><span data-stu-id="54933-2103">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="54933-2104">Il nodo radice del file di configurazione viene ignorato quando vengono create le coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-2104">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="54933-2105">Non specificare una definizione DTD (Document Type Definition) o uno spazio dei nomi nel file.</span><span class="sxs-lookup"><span data-stu-id="54933-2105">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="54933-2106">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="54933-2106">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="54933-2107">I file di configurazione XML possono usare nomi di elementi distinti per le sezioni ripetute:</span><span class="sxs-lookup"><span data-stu-id="54933-2107">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="54933-2108">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="54933-2108">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="54933-2109">section0:key0</span><span class="sxs-lookup"><span data-stu-id="54933-2109">section0:key0</span></span>
* <span data-ttu-id="54933-2110">section0:key1</span><span class="sxs-lookup"><span data-stu-id="54933-2110">section0:key1</span></span>
* <span data-ttu-id="54933-2111">section1:key0</span><span class="sxs-lookup"><span data-stu-id="54933-2111">section1:key0</span></span>
* <span data-ttu-id="54933-2112">section1:key1</span><span class="sxs-lookup"><span data-stu-id="54933-2112">section1:key1</span></span>

<span data-ttu-id="54933-2113">La ripetizione di elementi che usano lo stesso nome di elemento funziona se si usa l'attributo `name` per distinguere gli elementi:</span><span class="sxs-lookup"><span data-stu-id="54933-2113">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="54933-2114">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="54933-2114">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="54933-2115">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="54933-2115">section:section0:key:key0</span></span>
* <span data-ttu-id="54933-2116">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="54933-2116">section:section0:key:key1</span></span>
* <span data-ttu-id="54933-2117">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="54933-2117">section:section1:key:key0</span></span>
* <span data-ttu-id="54933-2118">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="54933-2118">section:section1:key:key1</span></span>

<span data-ttu-id="54933-2119">È possibile usare attributi per fornire valori:</span><span class="sxs-lookup"><span data-stu-id="54933-2119">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="54933-2120">Il file di configurazione precedente carica le chiavi seguenti con `value`:</span><span class="sxs-lookup"><span data-stu-id="54933-2120">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="54933-2121">key:attribute</span><span class="sxs-lookup"><span data-stu-id="54933-2121">key:attribute</span></span>
* <span data-ttu-id="54933-2122">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="54933-2122">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="54933-2123">Provider di configurazione KeyPerFile</span><span class="sxs-lookup"><span data-stu-id="54933-2123">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="54933-2124">Il <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa i file di una directory come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-2124">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="54933-2125">La chiave è il nome del file.</span><span class="sxs-lookup"><span data-stu-id="54933-2125">The key is the file name.</span></span> <span data-ttu-id="54933-2126">Il valore contiene il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="54933-2126">The value contains the file's contents.</span></span> <span data-ttu-id="54933-2127">Il provider di configurazione KeyPerFile viene usato in scenari di hosting Docker.</span><span class="sxs-lookup"><span data-stu-id="54933-2127">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="54933-2128">Per attivare la configurazione chiave-per-file, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="54933-2128">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="54933-2129">Il `directoryPath` per i file deve essere un percorso assoluto.</span><span class="sxs-lookup"><span data-stu-id="54933-2129">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="54933-2130">Gli overload consentono di specificare:</span><span class="sxs-lookup"><span data-stu-id="54933-2130">Overloads permit specifying:</span></span>

* <span data-ttu-id="54933-2131">Un delegato `Action<KeyPerFileConfigurationSource>` che configura l'origine.</span><span class="sxs-lookup"><span data-stu-id="54933-2131">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="54933-2132">Se la directory è facoltativa e il percorso della directory.</span><span class="sxs-lookup"><span data-stu-id="54933-2132">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="54933-2133">Il doppio carattere di sottolineatura (`__`) viene usato come delimitatore per le chiavi di configurazione nei nomi dei file.</span><span class="sxs-lookup"><span data-stu-id="54933-2133">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="54933-2134">Ad esempio, il nome di file `Logging__LogLevel__System` produce la chiave di configurazione `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="54933-2134">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="54933-2135">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="54933-2135">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="54933-2136">Provider di configurazione della memoria</span><span class="sxs-lookup"><span data-stu-id="54933-2136">Memory Configuration Provider</span></span>

<span data-ttu-id="54933-2137">Il <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una raccolta in memoria come coppie chiave-valore della configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-2137">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="54933-2138">Per attivare la configurazione della raccolta in memoria, chiamare il metodo di estensione <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> su un'istanza di <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="54933-2138">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="54933-2139">Il provider di configurazione può essere inizializzato con un `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="54933-2139">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="54933-2140">Chiamare `ConfigureAppConfiguration` quando si crea l'host per specificare la configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="54933-2140">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="54933-2141">Nell'esempio seguente viene creato un dizionario di configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-2141">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="54933-2142">Il dizionario viene usato con una chiamata a `AddInMemoryCollection` per fornire la configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-2142">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="54933-2143">GetValue</span><span class="sxs-lookup"><span data-stu-id="54933-2143">GetValue</span></span>

<span data-ttu-id="54933-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)estrae un singolo valore dalla configurazione con una chiave specificata e lo converte nel tipo non di raccolta specificato.</span><span class="sxs-lookup"><span data-stu-id="54933-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="54933-2145">Un overload accetta un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="54933-2145">An overload accepts a default value.</span></span>

<span data-ttu-id="54933-2146">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="54933-2146">The following example:</span></span>

* <span data-ttu-id="54933-2147">Estrae il valore di stringa dalla configurazione con la chiave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="54933-2147">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="54933-2148">Se `NumberKey` non viene trovato nelle chiavi di configurazione, viene usato il valore predefinito di `99`.</span><span class="sxs-lookup"><span data-stu-id="54933-2148">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="54933-2149">Assegna al valore il tipo `int`.</span><span class="sxs-lookup"><span data-stu-id="54933-2149">Types the value as an `int`.</span></span>
* <span data-ttu-id="54933-2150">Memorizza il valore nella proprietà `NumberConfig` per l'uso da parte della pagina.</span><span class="sxs-lookup"><span data-stu-id="54933-2150">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="54933-2151">GetSection, GetChildren ed Exists</span><span class="sxs-lookup"><span data-stu-id="54933-2151">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="54933-2152">Per gli esempi che seguono, prendere in considerazione il file JSON seguente.</span><span class="sxs-lookup"><span data-stu-id="54933-2152">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="54933-2153">Vengono trovate quattro chiavi in due sezioni, una delle quali include una coppia di sottosezioni:</span><span class="sxs-lookup"><span data-stu-id="54933-2153">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="54933-2154">Quando il file viene letto nella configurazione, vengono create le chiavi gerarchiche univoche seguenti per contenere i valori di configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-2154">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="54933-2155">section0:key0</span><span class="sxs-lookup"><span data-stu-id="54933-2155">section0:key0</span></span>
* <span data-ttu-id="54933-2156">section0:key1</span><span class="sxs-lookup"><span data-stu-id="54933-2156">section0:key1</span></span>
* <span data-ttu-id="54933-2157">section1:key0</span><span class="sxs-lookup"><span data-stu-id="54933-2157">section1:key0</span></span>
* <span data-ttu-id="54933-2158">section1:key1</span><span class="sxs-lookup"><span data-stu-id="54933-2158">section1:key1</span></span>
* <span data-ttu-id="54933-2159">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="54933-2159">section2:subsection0:key0</span></span>
* <span data-ttu-id="54933-2160">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="54933-2160">section2:subsection0:key1</span></span>
* <span data-ttu-id="54933-2161">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="54933-2161">section2:subsection1:key0</span></span>
* <span data-ttu-id="54933-2162">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="54933-2162">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="54933-2163">GetSection</span><span class="sxs-lookup"><span data-stu-id="54933-2163">GetSection</span></span>

<span data-ttu-id="54933-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) estrae una sottosezione della configurazione con la chiave di sottosezione specificata.</span><span class="sxs-lookup"><span data-stu-id="54933-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="54933-2165">Per restituire una <xref:Microsoft.Extensions.Configuration.IConfigurationSection> che contiene solo le coppie chiave-valore in `section1`, chiamare `GetSection` e fornire il nome della sezione:</span><span class="sxs-lookup"><span data-stu-id="54933-2165">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="54933-2166">`configSection` non ha un valore, ma solo una chiave e un percorso.</span><span class="sxs-lookup"><span data-stu-id="54933-2166">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="54933-2167">In modo analogo, per ottenere i valori per le chiavi in `section2:subsection0`, chiamare `GetSection` e fornire il percorso della sezione:</span><span class="sxs-lookup"><span data-stu-id="54933-2167">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="54933-2168">`GetSection` non restituisce mai `null`.</span><span class="sxs-lookup"><span data-stu-id="54933-2168">`GetSection` never returns `null`.</span></span> <span data-ttu-id="54933-2169">Se non viene trovata una sezione corrispondente, viene restituita una `IConfigurationSection` vuota.</span><span class="sxs-lookup"><span data-stu-id="54933-2169">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="54933-2170">Quando `GetSection` restituisce una sezione corrispondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> non viene compilata.</span><span class="sxs-lookup"><span data-stu-id="54933-2170">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="54933-2171">Quando la sezione esiste, vengono restituiti <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.</span><span class="sxs-lookup"><span data-stu-id="54933-2171">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="54933-2172">GetChildren</span><span class="sxs-lookup"><span data-stu-id="54933-2172">GetChildren</span></span>

<span data-ttu-id="54933-2173">Una chiamata a [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) per `section2` ottiene una `IEnumerable<IConfigurationSection>` che include:</span><span class="sxs-lookup"><span data-stu-id="54933-2173">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="54933-2174">Exists</span><span class="sxs-lookup"><span data-stu-id="54933-2174">Exists</span></span>

<span data-ttu-id="54933-2175">Usare [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) per determinare se esiste una sezione di configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="54933-2176">Considerati i dati di esempio, `sectionExists` è `false` perché non esiste una sezione `section2:subsection2` nei dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-2176">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="54933-2177">Associazione a un oggetto grafico</span><span class="sxs-lookup"><span data-stu-id="54933-2177">Bind to an object graph</span></span>

<span data-ttu-id="54933-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> è in grado di associare un intero oggetto grafico POCO.</span><span class="sxs-lookup"><span data-stu-id="54933-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="54933-2179">Come per l'associazione di un oggetto semplice, vengono associate solo le proprietà di lettura/scrittura pubbliche.</span><span class="sxs-lookup"><span data-stu-id="54933-2179">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="54933-2180">L'esempio contiene un modello `TvShow` il cui oggetto grafico include `Metadata` e le classi `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="54933-2180">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="54933-2181">L'app di esempio ha un file *tvshow.xml* che contiene i dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-2181">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="54933-2182">La configurazione viene associata all'intero oggetto grafico `TvShow` con il metodo `Bind`.</span><span class="sxs-lookup"><span data-stu-id="54933-2182">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="54933-2183">L'istanza associata viene assegnata a una proprietà per il rendering:</span><span class="sxs-lookup"><span data-stu-id="54933-2183">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="54933-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)associa e restituisce il tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="54933-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="54933-2185">`Get<T>` può essere più comodo che usare `Bind`.</span><span class="sxs-lookup"><span data-stu-id="54933-2185">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="54933-2186">Il codice seguente illustra come usare `Get<T>` con l'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="54933-2186">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="54933-2187">Associare una matrice a una classe</span><span class="sxs-lookup"><span data-stu-id="54933-2187">Bind an array to a class</span></span>

<span data-ttu-id="54933-2188">*L'app di esempio dimostra i concetti spiegati in questa sezione.*</span><span class="sxs-lookup"><span data-stu-id="54933-2188">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="54933-2189">Il <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supporta l'associazione di matrici agli oggetti usando gli indici delle matrici nelle chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-2189">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="54933-2190">Qualsiasi formato di matrice che espone un segmento di chiave numerico ( `:0:` , `:1:` , &hellip; `:{n}:` ) è in grado di associare array a una matrice di classi poco.</span><span class="sxs-lookup"><span data-stu-id="54933-2190">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="54933-2191">L'associazione viene fornita per convenzione.</span><span class="sxs-lookup"><span data-stu-id="54933-2191">Binding is provided by convention.</span></span> <span data-ttu-id="54933-2192">I provider di configurazione personalizzati non devono implementare l'associazione di matrici.</span><span class="sxs-lookup"><span data-stu-id="54933-2192">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="54933-2193">**Elaborazione di matrici in memoria**</span><span class="sxs-lookup"><span data-stu-id="54933-2193">**In-memory array processing**</span></span>

<span data-ttu-id="54933-2194">Prendere in considerazione le chiavi di configurazione e i valori indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="54933-2194">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="54933-2195">Chiave</span><span class="sxs-lookup"><span data-stu-id="54933-2195">Key</span></span>             | <span data-ttu-id="54933-2196">valore</span><span class="sxs-lookup"><span data-stu-id="54933-2196">Value</span></span>  |
| :---
<span data-ttu-id="54933-2197">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2198">'Blazor'</span></span>
- <span data-ttu-id="54933-2199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2199">'Identity'</span></span>
- <span data-ttu-id="54933-2200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2200">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2201">'Razor'</span></span>
- <span data-ttu-id="54933-2202">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2203">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2204">'Blazor'</span></span>
- <span data-ttu-id="54933-2205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2205">'Identity'</span></span>
- <span data-ttu-id="54933-2206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2206">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2207">'Razor'</span></span>
- <span data-ttu-id="54933-2208">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2209">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2210">'Blazor'</span></span>
- <span data-ttu-id="54933-2211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2211">'Identity'</span></span>
- <span data-ttu-id="54933-2212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2212">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2213">'Razor'</span></span>
- <span data-ttu-id="54933-2214">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2215">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2216">'Blazor'</span></span>
- <span data-ttu-id="54933-2217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2217">'Identity'</span></span>
- <span data-ttu-id="54933-2218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2218">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2219">'Razor'</span></span>
- <span data-ttu-id="54933-2220">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2220">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-2221">-------: | :----: | | matrice: voci: 0 | value0 | | matrice: voci: 1 | value1 | | matrice: voci: 2 | Value2 | | matrice: voci: 4 | Value4 | | matrice: voci: 5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="54933-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span></span>

<span data-ttu-id="54933-2222">Queste chiavi e i valori vengono caricati nell'app di esempio usando il provider di configurazione della memoria:</span><span class="sxs-lookup"><span data-stu-id="54933-2222">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="54933-2223">La matrice ignora un valore per l'indice &num;3.</span><span class="sxs-lookup"><span data-stu-id="54933-2223">The array skips a value for index &num;3.</span></span> <span data-ttu-id="54933-2224">Lo strumento di associazione di configurazione non è in grado di associare valori null o di creare voci null negli oggetti associati, situazione che diventerà chiara tra poco quando viene illustrato il risultato dell'associazione di questa matrice a un oggetto.</span><span class="sxs-lookup"><span data-stu-id="54933-2224">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="54933-2225">Nell'app di esempio, è disponibile una classe POCO per i dati di configurazione associati:</span><span class="sxs-lookup"><span data-stu-id="54933-2225">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="54933-2226">I dati di configurazione sono associati all'oggetto:</span><span class="sxs-lookup"><span data-stu-id="54933-2226">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="54933-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)è anche possibile usare la sintassi, che comporta un codice più compatto:</span><span class="sxs-lookup"><span data-stu-id="54933-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="54933-2228">L'oggetto associato, un'istanza di `ArrayExample`, riceve i dati di matrice dalla configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-2228">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="54933-2229">Indice di `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="54933-2229">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="54933-2230">`ArrayExample.Entries` Valore</span><span class="sxs-lookup"><span data-stu-id="54933-2230">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="54933-2231">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2232">'Blazor'</span></span>
- <span data-ttu-id="54933-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2233">'Identity'</span></span>
- <span data-ttu-id="54933-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2235">'Razor'</span></span>
- <span data-ttu-id="54933-2236">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2237">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2238">'Blazor'</span></span>
- <span data-ttu-id="54933-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2239">'Identity'</span></span>
- <span data-ttu-id="54933-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2241">'Razor'</span></span>
- <span data-ttu-id="54933-2242">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2243">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2244">'Blazor'</span></span>
- <span data-ttu-id="54933-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2245">'Identity'</span></span>
- <span data-ttu-id="54933-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2247">'Razor'</span></span>
- <span data-ttu-id="54933-2248">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2249">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2250">'Blazor'</span></span>
- <span data-ttu-id="54933-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2251">'Identity'</span></span>
- <span data-ttu-id="54933-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2253">'Razor'</span></span>
- <span data-ttu-id="54933-2254">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2255">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2256">'Blazor'</span></span>
- <span data-ttu-id="54933-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2257">'Identity'</span></span>
- <span data-ttu-id="54933-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2259">'Razor'</span></span>
- <span data-ttu-id="54933-2260">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2261">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2262">'Blazor'</span></span>
- <span data-ttu-id="54933-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2263">'Identity'</span></span>
- <span data-ttu-id="54933-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2265">'Razor'</span></span>
- <span data-ttu-id="54933-2266">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2267">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2268">'Blazor'</span></span>
- <span data-ttu-id="54933-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2269">'Identity'</span></span>
- <span data-ttu-id="54933-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2271">'Razor'</span></span>
- <span data-ttu-id="54933-2272">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2273">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2274">'Blazor'</span></span>
- <span data-ttu-id="54933-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2275">'Identity'</span></span>
- <span data-ttu-id="54933-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2277">'Razor'</span></span>
- <span data-ttu-id="54933-2278">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2279">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2280">'Blazor'</span></span>
- <span data-ttu-id="54933-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2281">'Identity'</span></span>
- <span data-ttu-id="54933-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2283">'Razor'</span></span>
- <span data-ttu-id="54933-2284">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2285">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2286">'Blazor'</span></span>
- <span data-ttu-id="54933-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2287">'Identity'</span></span>
- <span data-ttu-id="54933-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2289">'Razor'</span></span>
- <span data-ttu-id="54933-2290">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2291">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2292">'Blazor'</span></span>
- <span data-ttu-id="54933-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2293">'Identity'</span></span>
- <span data-ttu-id="54933-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2295">'Razor'</span></span>
- <span data-ttu-id="54933-2296">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2296">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-2297">-------------: | :---title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2298">'Blazor'</span></span>
- <span data-ttu-id="54933-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2299">'Identity'</span></span>
- <span data-ttu-id="54933-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2301">'Razor'</span></span>
- <span data-ttu-id="54933-2302">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2303">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2304">'Blazor'</span></span>
- <span data-ttu-id="54933-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2305">'Identity'</span></span>
- <span data-ttu-id="54933-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2307">'Razor'</span></span>
- <span data-ttu-id="54933-2308">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2309">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2310">'Blazor'</span></span>
- <span data-ttu-id="54933-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2311">'Identity'</span></span>
- <span data-ttu-id="54933-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2313">'Razor'</span></span>
- <span data-ttu-id="54933-2314">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2315">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2316">'Blazor'</span></span>
- <span data-ttu-id="54933-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2317">'Identity'</span></span>
- <span data-ttu-id="54933-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2319">'Razor'</span></span>
- <span data-ttu-id="54933-2320">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2321">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2322">'Blazor'</span></span>
- <span data-ttu-id="54933-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2323">'Identity'</span></span>
- <span data-ttu-id="54933-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2325">'Razor'</span></span>
- <span data-ttu-id="54933-2326">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2327">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2328">'Blazor'</span></span>
- <span data-ttu-id="54933-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2329">'Identity'</span></span>
- <span data-ttu-id="54933-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2331">'Razor'</span></span>
- <span data-ttu-id="54933-2332">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2333">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2334">'Blazor'</span></span>
- <span data-ttu-id="54933-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2335">'Identity'</span></span>
- <span data-ttu-id="54933-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2337">'Razor'</span></span>
- <span data-ttu-id="54933-2338">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2339">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2340">'Blazor'</span></span>
- <span data-ttu-id="54933-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2341">'Identity'</span></span>
- <span data-ttu-id="54933-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2343">'Razor'</span></span>
- <span data-ttu-id="54933-2344">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2345">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2346">'Blazor'</span></span>
- <span data-ttu-id="54933-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2347">'Identity'</span></span>
- <span data-ttu-id="54933-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2349">'Razor'</span></span>
- <span data-ttu-id="54933-2350">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2351">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2352">'Blazor'</span></span>
- <span data-ttu-id="54933-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2353">'Identity'</span></span>
- <span data-ttu-id="54933-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2355">'Razor'</span></span>
- <span data-ttu-id="54933-2356">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2357">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2358">'Blazor'</span></span>
- <span data-ttu-id="54933-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2359">'Identity'</span></span>
- <span data-ttu-id="54933-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2361">'Razor'</span></span>
- <span data-ttu-id="54933-2362">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2362">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-2363">-------------: | | 0 | value0 | | 1 | value1 | | 2 | Value2 | | 3 | Value4 | | 4 | value5 |</span><span class="sxs-lookup"><span data-stu-id="54933-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span></span>

<span data-ttu-id="54933-2364">L'indice &num;3 nell'oggetto associato contiene i dati di configurazione per la chiave di configurazione `array:4` e il relativo valore `value4`.</span><span class="sxs-lookup"><span data-stu-id="54933-2364">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="54933-2365">Quando i dati di configurazione contenenti una matrice vengono associati, gli indici di matrice nelle chiavi di configurazione vengono usati semplicemente per scorrere i dati di configurazione quando si crea l'oggetto.</span><span class="sxs-lookup"><span data-stu-id="54933-2365">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="54933-2366">Un valore null non può essere mantenuto nei dati di configurazione e una voce con valore null non viene creata in un oggetto associato quando una matrice nelle chiavi di configurazione ignora uno o più indici.</span><span class="sxs-lookup"><span data-stu-id="54933-2366">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="54933-2367">L'elemento di configurazione mancante per l'indice &num;3 può essere fornito prima dell'associazione all'istanza `ArrayExample` da qualsiasi provider di configurazione che produce la coppia chiave-valore corretta nella configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-2367">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="54933-2368">Se il codice di esempio include un provider di configurazione JSON aggiuntivo con la coppia chiave-valore mancante, `ArrayExample.Entries` corrisponde alla matrice di configurazione completa:</span><span class="sxs-lookup"><span data-stu-id="54933-2368">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="54933-2369">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="54933-2369">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="54933-2370">In `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="54933-2370">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="54933-2371">La coppia chiave-valore mostrata nella tabella viene caricata nella configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-2371">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="54933-2372">Chiave</span><span class="sxs-lookup"><span data-stu-id="54933-2372">Key</span></span>             | <span data-ttu-id="54933-2373">valore</span><span class="sxs-lookup"><span data-stu-id="54933-2373">Value</span></span>  |
| :---
<span data-ttu-id="54933-2374">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2375">'Blazor'</span></span>
- <span data-ttu-id="54933-2376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2376">'Identity'</span></span>
- <span data-ttu-id="54933-2377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2377">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2378">'Razor'</span></span>
- <span data-ttu-id="54933-2379">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2380">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2381">'Blazor'</span></span>
- <span data-ttu-id="54933-2382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2382">'Identity'</span></span>
- <span data-ttu-id="54933-2383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2383">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2384">'Razor'</span></span>
- <span data-ttu-id="54933-2385">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2386">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2387">'Blazor'</span></span>
- <span data-ttu-id="54933-2388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2388">'Identity'</span></span>
- <span data-ttu-id="54933-2389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2389">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2390">'Razor'</span></span>
- <span data-ttu-id="54933-2391">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2392">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2393">'Blazor'</span></span>
- <span data-ttu-id="54933-2394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2394">'Identity'</span></span>
- <span data-ttu-id="54933-2395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2395">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2396">'Razor'</span></span>
- <span data-ttu-id="54933-2397">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2397">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-2398">-------: | :----: | | matrice: voci: 3 | valore3 |</span><span class="sxs-lookup"><span data-stu-id="54933-2398">-------: | :----: | | array:entries:3 | value3 |</span></span>

<span data-ttu-id="54933-2399">Se l'istanza della classe `ArrayExample` viene associata dopo che il provider di configurazione JSON include la voce per l'indice &num;3, la matrice `ArrayExample.Entries` include il valore.</span><span class="sxs-lookup"><span data-stu-id="54933-2399">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="54933-2400">Indice di `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="54933-2400">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="54933-2401">`ArrayExample.Entries` Valore</span><span class="sxs-lookup"><span data-stu-id="54933-2401">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="54933-2402">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2403">'Blazor'</span></span>
- <span data-ttu-id="54933-2404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2404">'Identity'</span></span>
- <span data-ttu-id="54933-2405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2405">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2406">'Razor'</span></span>
- <span data-ttu-id="54933-2407">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2408">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2409">'Blazor'</span></span>
- <span data-ttu-id="54933-2410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2410">'Identity'</span></span>
- <span data-ttu-id="54933-2411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2411">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2412">'Razor'</span></span>
- <span data-ttu-id="54933-2413">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2414">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2415">'Blazor'</span></span>
- <span data-ttu-id="54933-2416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2416">'Identity'</span></span>
- <span data-ttu-id="54933-2417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2417">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2418">'Razor'</span></span>
- <span data-ttu-id="54933-2419">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2420">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2421">'Blazor'</span></span>
- <span data-ttu-id="54933-2422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2422">'Identity'</span></span>
- <span data-ttu-id="54933-2423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2423">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2424">'Razor'</span></span>
- <span data-ttu-id="54933-2425">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2426">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2427">'Blazor'</span></span>
- <span data-ttu-id="54933-2428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2428">'Identity'</span></span>
- <span data-ttu-id="54933-2429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2429">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2430">'Razor'</span></span>
- <span data-ttu-id="54933-2431">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2432">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2433">'Blazor'</span></span>
- <span data-ttu-id="54933-2434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2434">'Identity'</span></span>
- <span data-ttu-id="54933-2435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2435">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2436">'Razor'</span></span>
- <span data-ttu-id="54933-2437">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2438">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2439">'Blazor'</span></span>
- <span data-ttu-id="54933-2440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2440">'Identity'</span></span>
- <span data-ttu-id="54933-2441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2441">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2442">'Razor'</span></span>
- <span data-ttu-id="54933-2443">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2443">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2444">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2445">'Blazor'</span></span>
- <span data-ttu-id="54933-2446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2446">'Identity'</span></span>
- <span data-ttu-id="54933-2447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2447">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2448">'Razor'</span></span>
- <span data-ttu-id="54933-2449">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2450">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2451">'Blazor'</span></span>
- <span data-ttu-id="54933-2452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2452">'Identity'</span></span>
- <span data-ttu-id="54933-2453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2453">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2454">'Razor'</span></span>
- <span data-ttu-id="54933-2455">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2456">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2457">'Blazor'</span></span>
- <span data-ttu-id="54933-2458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2458">'Identity'</span></span>
- <span data-ttu-id="54933-2459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2459">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2460">'Razor'</span></span>
- <span data-ttu-id="54933-2461">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2462">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2463">'Blazor'</span></span>
- <span data-ttu-id="54933-2464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2464">'Identity'</span></span>
- <span data-ttu-id="54933-2465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2465">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2466">'Razor'</span></span>
- <span data-ttu-id="54933-2467">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2467">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-2468">-------------: | :---title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2469">'Blazor'</span></span>
- <span data-ttu-id="54933-2470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2470">'Identity'</span></span>
- <span data-ttu-id="54933-2471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2471">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2472">'Razor'</span></span>
- <span data-ttu-id="54933-2473">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2474">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2475">'Blazor'</span></span>
- <span data-ttu-id="54933-2476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2476">'Identity'</span></span>
- <span data-ttu-id="54933-2477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2477">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2478">'Razor'</span></span>
- <span data-ttu-id="54933-2479">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2480">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2481">'Blazor'</span></span>
- <span data-ttu-id="54933-2482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2482">'Identity'</span></span>
- <span data-ttu-id="54933-2483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2483">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2484">'Razor'</span></span>
- <span data-ttu-id="54933-2485">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2486">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2487">'Blazor'</span></span>
- <span data-ttu-id="54933-2488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2488">'Identity'</span></span>
- <span data-ttu-id="54933-2489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2489">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2490">'Razor'</span></span>
- <span data-ttu-id="54933-2491">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2492">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2493">'Blazor'</span></span>
- <span data-ttu-id="54933-2494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2494">'Identity'</span></span>
- <span data-ttu-id="54933-2495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2495">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2496">'Razor'</span></span>
- <span data-ttu-id="54933-2497">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2498">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2499">'Blazor'</span></span>
- <span data-ttu-id="54933-2500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2500">'Identity'</span></span>
- <span data-ttu-id="54933-2501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2501">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2502">'Razor'</span></span>
- <span data-ttu-id="54933-2503">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2504">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2505">'Blazor'</span></span>
- <span data-ttu-id="54933-2506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2506">'Identity'</span></span>
- <span data-ttu-id="54933-2507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2507">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2508">'Razor'</span></span>
- <span data-ttu-id="54933-2509">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2510">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2511">'Blazor'</span></span>
- <span data-ttu-id="54933-2512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2512">'Identity'</span></span>
- <span data-ttu-id="54933-2513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2513">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2514">'Razor'</span></span>
- <span data-ttu-id="54933-2515">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2516">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2517">'Blazor'</span></span>
- <span data-ttu-id="54933-2518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2518">'Identity'</span></span>
- <span data-ttu-id="54933-2519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2519">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2520">'Razor'</span></span>
- <span data-ttu-id="54933-2521">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2522">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2523">'Blazor'</span></span>
- <span data-ttu-id="54933-2524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2524">'Identity'</span></span>
- <span data-ttu-id="54933-2525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2525">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2526">'Razor'</span></span>
- <span data-ttu-id="54933-2527">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2527">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2528">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2529">'Blazor'</span></span>
- <span data-ttu-id="54933-2530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2530">'Identity'</span></span>
- <span data-ttu-id="54933-2531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2531">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2532">'Razor'</span></span>
- <span data-ttu-id="54933-2533">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2533">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-2534">-------------: | | 0 | value0 | | 1 | value1 | | 2 | Value2 | | 3 | valore3 | | 4 | Value4 | | 5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="54933-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span></span>

<span data-ttu-id="54933-2535">**Elaborazione di matrice JSON**</span><span class="sxs-lookup"><span data-stu-id="54933-2535">**JSON array processing**</span></span>

<span data-ttu-id="54933-2536">Se un file JSON contiene una matrice, vengono create chiavi di configurazione per gli elementi della matrice con un indice di sezione in base zero.</span><span class="sxs-lookup"><span data-stu-id="54933-2536">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="54933-2537">Nel file di configurazione seguente, `subsection` è una matrice:</span><span class="sxs-lookup"><span data-stu-id="54933-2537">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="54933-2538">Il provider di configurazione JSON legge i dati di configurazione nelle coppie chiave-valore seguenti:</span><span class="sxs-lookup"><span data-stu-id="54933-2538">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="54933-2539">Chiave</span><span class="sxs-lookup"><span data-stu-id="54933-2539">Key</span></span>                     | <span data-ttu-id="54933-2540">valore</span><span class="sxs-lookup"><span data-stu-id="54933-2540">Value</span></span>  |
| ---
<span data-ttu-id="54933-2541">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2542">'Blazor'</span></span>
- <span data-ttu-id="54933-2543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2543">'Identity'</span></span>
- <span data-ttu-id="54933-2544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2544">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2545">'Razor'</span></span>
- <span data-ttu-id="54933-2546">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2547">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2548">'Blazor'</span></span>
- <span data-ttu-id="54933-2549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2549">'Identity'</span></span>
- <span data-ttu-id="54933-2550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2550">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2551">'Razor'</span></span>
- <span data-ttu-id="54933-2552">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2553">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2554">'Blazor'</span></span>
- <span data-ttu-id="54933-2555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2555">'Identity'</span></span>
- <span data-ttu-id="54933-2556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2556">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2557">'Razor'</span></span>
- <span data-ttu-id="54933-2558">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2559">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2560">'Blazor'</span></span>
- <span data-ttu-id="54933-2561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2561">'Identity'</span></span>
- <span data-ttu-id="54933-2562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2562">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2563">'Razor'</span></span>
- <span data-ttu-id="54933-2564">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2565">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2566">'Blazor'</span></span>
- <span data-ttu-id="54933-2567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2567">'Identity'</span></span>
- <span data-ttu-id="54933-2568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2568">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2569">'Razor'</span></span>
- <span data-ttu-id="54933-2570">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2571">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2572">'Blazor'</span></span>
- <span data-ttu-id="54933-2573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2573">'Identity'</span></span>
- <span data-ttu-id="54933-2574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2574">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2575">'Razor'</span></span>
- <span data-ttu-id="54933-2576">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2577">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2578">'Blazor'</span></span>
- <span data-ttu-id="54933-2579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2579">'Identity'</span></span>
- <span data-ttu-id="54933-2580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2580">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2581">'Razor'</span></span>
- <span data-ttu-id="54933-2582">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2583">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2584">'Blazor'</span></span>
- <span data-ttu-id="54933-2585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2585">'Identity'</span></span>
- <span data-ttu-id="54933-2586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2586">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2587">'Razor'</span></span>
- <span data-ttu-id="54933-2588">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2589">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2590">'Blazor'</span></span>
- <span data-ttu-id="54933-2591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2591">'Identity'</span></span>
- <span data-ttu-id="54933-2592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2592">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2593">'Razor'</span></span>
- <span data-ttu-id="54933-2594">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2594">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-2595">------------ | :----: | | json_array: chiave | valore a | | json_array: sottosezione: 0 | valoreB | | json_array: sottosezione: 1 | Valorec | | json_array: sottosezione: 2 | con valori |</span><span class="sxs-lookup"><span data-stu-id="54933-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span></span>

<span data-ttu-id="54933-2596">Nell'app di esempio, la classe POCO seguente è disponibile per associare le coppie chiave-valore della configurazione:</span><span class="sxs-lookup"><span data-stu-id="54933-2596">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="54933-2597">Dopo l'associazione, `JsonArrayExample.Key` contiene il valore `valueA`.</span><span class="sxs-lookup"><span data-stu-id="54933-2597">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="54933-2598">I valori di sottosezione vengono archiviati nella proprietà matrice POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="54933-2598">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="54933-2599">Indice di `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="54933-2599">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="54933-2600">`JsonArrayExample.Subsection` Valore</span><span class="sxs-lookup"><span data-stu-id="54933-2600">`JsonArrayExample.Subsection` Value</span></span> |
| :---
<span data-ttu-id="54933-2601">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2602">'Blazor'</span></span>
- <span data-ttu-id="54933-2603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2603">'Identity'</span></span>
- <span data-ttu-id="54933-2604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2604">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2605">'Razor'</span></span>
- <span data-ttu-id="54933-2606">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2607">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2608">'Blazor'</span></span>
- <span data-ttu-id="54933-2609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2609">'Identity'</span></span>
- <span data-ttu-id="54933-2610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2610">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2611">'Razor'</span></span>
- <span data-ttu-id="54933-2612">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2613">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2614">'Blazor'</span></span>
- <span data-ttu-id="54933-2615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2615">'Identity'</span></span>
- <span data-ttu-id="54933-2616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2616">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2617">'Razor'</span></span>
- <span data-ttu-id="54933-2618">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2618">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2619">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2620">'Blazor'</span></span>
- <span data-ttu-id="54933-2621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2621">'Identity'</span></span>
- <span data-ttu-id="54933-2622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2622">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2623">'Razor'</span></span>
- <span data-ttu-id="54933-2624">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2625">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2626">'Blazor'</span></span>
- <span data-ttu-id="54933-2627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2627">'Identity'</span></span>
- <span data-ttu-id="54933-2628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2628">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2629">'Razor'</span></span>
- <span data-ttu-id="54933-2630">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2631">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2632">'Blazor'</span></span>
- <span data-ttu-id="54933-2633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2633">'Identity'</span></span>
- <span data-ttu-id="54933-2634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2634">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2635">'Razor'</span></span>
- <span data-ttu-id="54933-2636">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2637">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2638">'Blazor'</span></span>
- <span data-ttu-id="54933-2639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2639">'Identity'</span></span>
- <span data-ttu-id="54933-2640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2640">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2641">'Razor'</span></span>
- <span data-ttu-id="54933-2642">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2643">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2644">'Blazor'</span></span>
- <span data-ttu-id="54933-2645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2645">'Identity'</span></span>
- <span data-ttu-id="54933-2646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2646">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2647">'Razor'</span></span>
- <span data-ttu-id="54933-2648">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2649">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2650">'Blazor'</span></span>
- <span data-ttu-id="54933-2651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2651">'Identity'</span></span>
- <span data-ttu-id="54933-2652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2652">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2653">'Razor'</span></span>
- <span data-ttu-id="54933-2654">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2655">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2656">'Blazor'</span></span>
- <span data-ttu-id="54933-2657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2657">'Identity'</span></span>
- <span data-ttu-id="54933-2658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2658">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2659">'Razor'</span></span>
- <span data-ttu-id="54933-2660">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2661">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2662">'Blazor'</span></span>
- <span data-ttu-id="54933-2663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2663">'Identity'</span></span>
- <span data-ttu-id="54933-2664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2664">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2665">'Razor'</span></span>
- <span data-ttu-id="54933-2666">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2667">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2668">'Blazor'</span></span>
- <span data-ttu-id="54933-2669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2669">'Identity'</span></span>
- <span data-ttu-id="54933-2670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2670">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2671">'Razor'</span></span>
- <span data-ttu-id="54933-2672">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2672">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2673">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2674">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2674">'Blazor'</span></span>
- <span data-ttu-id="54933-2675">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2675">'Identity'</span></span>
- <span data-ttu-id="54933-2676">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2676">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2677">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2677">'Razor'</span></span>
- <span data-ttu-id="54933-2678">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2678">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2679">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2680">'Blazor'</span></span>
- <span data-ttu-id="54933-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2681">'Identity'</span></span>
- <span data-ttu-id="54933-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2683">'Razor'</span></span>
- <span data-ttu-id="54933-2684">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2684">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-2685">-----------------: | :---title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2686">'Blazor'</span></span>
- <span data-ttu-id="54933-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2687">'Identity'</span></span>
- <span data-ttu-id="54933-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2689">'Razor'</span></span>
- <span data-ttu-id="54933-2690">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2691">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2692">'Blazor'</span></span>
- <span data-ttu-id="54933-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2693">'Identity'</span></span>
- <span data-ttu-id="54933-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2695">'Razor'</span></span>
- <span data-ttu-id="54933-2696">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2697">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2698">'Blazor'</span></span>
- <span data-ttu-id="54933-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2699">'Identity'</span></span>
- <span data-ttu-id="54933-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2701">'Razor'</span></span>
- <span data-ttu-id="54933-2702">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2703">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2704">'Blazor'</span></span>
- <span data-ttu-id="54933-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2705">'Identity'</span></span>
- <span data-ttu-id="54933-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2707">'Razor'</span></span>
- <span data-ttu-id="54933-2708">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2709">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2710">'Blazor'</span></span>
- <span data-ttu-id="54933-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2711">'Identity'</span></span>
- <span data-ttu-id="54933-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2713">'Razor'</span></span>
- <span data-ttu-id="54933-2714">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2715">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2716">'Blazor'</span></span>
- <span data-ttu-id="54933-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2717">'Identity'</span></span>
- <span data-ttu-id="54933-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2719">'Razor'</span></span>
- <span data-ttu-id="54933-2720">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2721">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2722">'Blazor'</span></span>
- <span data-ttu-id="54933-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2723">'Identity'</span></span>
- <span data-ttu-id="54933-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2725">'Razor'</span></span>
- <span data-ttu-id="54933-2726">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2727">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2728">'Blazor'</span></span>
- <span data-ttu-id="54933-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2729">'Identity'</span></span>
- <span data-ttu-id="54933-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2731">'Razor'</span></span>
- <span data-ttu-id="54933-2732">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2733">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2734">'Blazor'</span></span>
- <span data-ttu-id="54933-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2735">'Identity'</span></span>
- <span data-ttu-id="54933-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2737">'Razor'</span></span>
- <span data-ttu-id="54933-2738">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2739">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2740">'Blazor'</span></span>
- <span data-ttu-id="54933-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2741">'Identity'</span></span>
- <span data-ttu-id="54933-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2743">'Razor'</span></span>
- <span data-ttu-id="54933-2744">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2745">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2746">'Blazor'</span></span>
- <span data-ttu-id="54933-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2747">'Identity'</span></span>
- <span data-ttu-id="54933-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2749">'Razor'</span></span>
- <span data-ttu-id="54933-2750">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2751">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2752">'Blazor'</span></span>
- <span data-ttu-id="54933-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2753">'Identity'</span></span>
- <span data-ttu-id="54933-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2755">'Razor'</span></span>
- <span data-ttu-id="54933-2756">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2757">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2758">'Blazor'</span></span>
- <span data-ttu-id="54933-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2759">'Identity'</span></span>
- <span data-ttu-id="54933-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2761">'Razor'</span></span>
- <span data-ttu-id="54933-2762">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54933-2763">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="54933-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54933-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54933-2764">'Blazor'</span></span>
- <span data-ttu-id="54933-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54933-2765">'Identity'</span></span>
- <span data-ttu-id="54933-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54933-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="54933-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54933-2767">'Razor'</span></span>
- <span data-ttu-id="54933-2768">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="54933-2768">'SignalR' uid:</span></span> 

<span data-ttu-id="54933-2769">-----------------: | | 0 | valoreB | | 1 | Valorec | | 2 | con valori |</span><span class="sxs-lookup"><span data-stu-id="54933-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="54933-2770">Provider di configurazione personalizzato</span><span class="sxs-lookup"><span data-stu-id="54933-2770">Custom configuration provider</span></span>

<span data-ttu-id="54933-2771">L'app di esempio dimostra come creare un provider di configurazione di base che legge le coppie chiave-valore di configurazione da un database usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="54933-2771">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="54933-2772">Il provider ha le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="54933-2772">The provider has the following characteristics:</span></span>

* <span data-ttu-id="54933-2773">Il database in memoria di Entity Framework viene usato a scopo dimostrativo.</span><span class="sxs-lookup"><span data-stu-id="54933-2773">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="54933-2774">Per usare un database che richiede una stringa di connessione, implementare un `ConfigurationBuilder` secondario per fornire la stringa di connessione da un altro provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="54933-2774">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="54933-2775">Il provider legge una tabella di database in una configurazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="54933-2775">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="54933-2776">Il provider non esegue una query sul database per ogni chiave.</span><span class="sxs-lookup"><span data-stu-id="54933-2776">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="54933-2777">Il ricaricamento in caso di modifica non è implementato, quindi l'aggiornamento del database dopo l'avvio dell'app non ha alcun effetto sulla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="54933-2777">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="54933-2778">Definire un'entità `EFConfigurationValue` per l'archiviazione dei valori di configurazione nel database.</span><span class="sxs-lookup"><span data-stu-id="54933-2778">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="54933-2779">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="54933-2779">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="54933-2780">Aggiungere `EFConfigurationContext` per archiviare i valori configurati e accedervi.</span><span class="sxs-lookup"><span data-stu-id="54933-2780">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="54933-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="54933-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="54933-2782">Creare una classe che implementi <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="54933-2782">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="54933-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="54933-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="54933-2784">Creare il provider di configurazione personalizzato ereditando da <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="54933-2784">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="54933-2785">Il provider di configurazione inizializza il database quando è vuoto.</span><span class="sxs-lookup"><span data-stu-id="54933-2785">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="54933-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="54933-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="54933-2787">Un metodo di estensione `AddEFConfiguration` consente di aggiungere l'origine di configurazione a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="54933-2787">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="54933-2788">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="54933-2788">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="54933-2789">L'esempio di codice seguente mostra come usare il `EFConfigurationProvider` personalizzato in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="54933-2789">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="54933-2790">Accedere alla configurazione durante l'avvio</span><span class="sxs-lookup"><span data-stu-id="54933-2790">Access configuration during startup</span></span>

<span data-ttu-id="54933-2791">Inserire `IConfiguration` nel costruttore `Startup` per accedere ai valori di configurazione in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="54933-2791">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="54933-2792">Per accedere alla configurazione in `Startup.Configure`, inserire `IConfiguration` direttamente nel metodo o usare l'istanza dal costruttore:</span><span class="sxs-lookup"><span data-stu-id="54933-2792">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="54933-2793">Per un esempio di accesso alla configurazione usando metodi di servizio di avvio, vedere [Avvio dell'applicazione: Metodi pratici](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="54933-2793">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="54933-2794">Accedere alla configurazione in una Razor pagina di pagine o in una visualizzazione MVC</span><span class="sxs-lookup"><span data-stu-id="54933-2794">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="54933-2795">Per accedere alle impostazioni di configurazione in una Razor pagina di pagine o in una visualizzazione MVC, aggiungere una [direttiva using](xref:mvc/views/razor#using) ([riferimenti per C#: direttiva using](/dotnet/csharp/language-reference/keywords/using-directive)) per lo [spazio dei nomi Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) e inserire <xref:Microsoft.Extensions.Configuration.IConfiguration> nella pagina o nella vista.</span><span class="sxs-lookup"><span data-stu-id="54933-2795">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="54933-2796">In una Razor pagina di pagine:</span><span class="sxs-lookup"><span data-stu-id="54933-2796">In a Razor Pages page:</span></span>

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

<span data-ttu-id="54933-2797">In una visualizzazione MVC:</span><span class="sxs-lookup"><span data-stu-id="54933-2797">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="54933-2798">Aggiungere la configurazione da un assembly esterno</span><span class="sxs-lookup"><span data-stu-id="54933-2798">Add configuration from an external assembly</span></span>

<span data-ttu-id="54933-2799">Un'implementazione <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> consente l'aggiunta di miglioramenti a un'app all'avvio, da un assembly esterno alla classe `Startup` dell'app.</span><span class="sxs-lookup"><span data-stu-id="54933-2799">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="54933-2800">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="54933-2800">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54933-2801">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="54933-2801">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
