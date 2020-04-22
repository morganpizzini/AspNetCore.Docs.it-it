---
title: Archiviazione sicura dei segreti delle app in fase di sviluppo in ASP.NET Core
author: rick-anderson
description: Scopri come archiviare e recuperare informazioni riservate come segreti dell'app durante lo sviluppo di un'app ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: c62c5e59ad0a72506fb72bda82aa821a4f1719c8
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791606"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="272bf-103">Archiviazione sicura dei segreti delle app in fase di sviluppo in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="272bf-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="272bf-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT), Kirk [Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), e [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="272bf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="272bf-105">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="272bf-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="272bf-106">Questo documento illustra le tecniche per l'archiviazione e il recupero di dati sensibili durante lo sviluppo di un'app ASP.NET Core in un computer di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="272bf-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="272bf-107">Non archiviare mai password o altri dati sensibili nel codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="272bf-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="272bf-108">I segreti di produzione non devono essere utilizzati per lo sviluppo o il test.</span><span class="sxs-lookup"><span data-stu-id="272bf-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="272bf-109">I segreti non devono essere distribuiti con l'app.</span><span class="sxs-lookup"><span data-stu-id="272bf-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="272bf-110">Al contrario, i segreti devono essere resi disponibili nell'ambiente di produzione tramite un mezzo controllato come le variabili di ambiente, l'insieme di credenziali delle chiavi di Azure e così via. È possibile archiviare e proteggere i segreti di test e produzione di Azure con il provider di [configurazione dell'insieme](xref:security/key-vault-configuration)di credenziali delle chiavi di Azure.You can store and protect Azure test and production secrets with the Azure Key Vault configuration provider .</span><span class="sxs-lookup"><span data-stu-id="272bf-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="272bf-111">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="272bf-111">Environment variables</span></span>

<span data-ttu-id="272bf-112">Le variabili di ambiente vengono usate per evitare l'archiviazione di segreti dell'app nel codice o nei file di configurazione locali.</span><span class="sxs-lookup"><span data-stu-id="272bf-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="272bf-113">Le variabili di ambiente eseguono l'override dei valori di configurazione per tutte le origini di configurazione specificate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="272bf-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="272bf-114">Si consideri un'app Web ASP.NET Core in cui è abilitata la sicurezza **degli account utente singoli.**</span><span class="sxs-lookup"><span data-stu-id="272bf-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="272bf-115">Una stringa di connessione al database predefinita è inclusa nel `DefaultConnection`file *appsettings.json* del progetto con la chiave .</span><span class="sxs-lookup"><span data-stu-id="272bf-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="272bf-116">La stringa di connessione predefinita è per LocalDB, che viene eseguito in modalità utente e non richiede una password.</span><span class="sxs-lookup"><span data-stu-id="272bf-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="272bf-117">Durante la distribuzione `DefaultConnection` dell'app, il valore della chiave può essere sostituito con il valore di una variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="272bf-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="272bf-118">La variabile di ambiente può archiviare la stringa di connessione completa con credenziali riservate.</span><span class="sxs-lookup"><span data-stu-id="272bf-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="272bf-119">Le variabili di ambiente vengono in genere archiviate in testo normale e non crittografato.</span><span class="sxs-lookup"><span data-stu-id="272bf-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="272bf-120">Se il computer o il processo è compromesso, le variabili di ambiente sono accessibili da parti non attendibili.</span><span class="sxs-lookup"><span data-stu-id="272bf-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="272bf-121">Potrebbero essere necessarie misure aggiuntive per impedire la divulgazione dei segreti utente.</span><span class="sxs-lookup"><span data-stu-id="272bf-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="272bf-122">Responsabile Segreto</span><span class="sxs-lookup"><span data-stu-id="272bf-122">Secret Manager</span></span>

<span data-ttu-id="272bf-123">Lo strumento Secret Manager memorizza i dati sensibili durante lo sviluppo di un progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="272bf-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="272bf-124">In questo contesto, una parte di dati sensibili è un segreto dell'app.</span><span class="sxs-lookup"><span data-stu-id="272bf-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="272bf-125">I segreti dell'app vengono archiviati in un percorso separato dall'albero del progetto.</span><span class="sxs-lookup"><span data-stu-id="272bf-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="272bf-126">I segreti dell'app sono associati a un progetto specifico o condivisi tra più progetti.</span><span class="sxs-lookup"><span data-stu-id="272bf-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="272bf-127">I segreti dell'app non vengono archiviati nel controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="272bf-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="272bf-128">Lo strumento Secret Manager non crittografa i segreti archiviati e non deve essere considerato come un archivio attendibile.</span><span class="sxs-lookup"><span data-stu-id="272bf-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="272bf-129">E 'solo per scopi di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="272bf-129">It's for development purposes only.</span></span> <span data-ttu-id="272bf-130">Le chiavi e i valori vengono archiviati in un file di configurazione JSON nella directory del profilo utente.</span><span class="sxs-lookup"><span data-stu-id="272bf-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="272bf-131">Funzionamento dello strumento Secret Manager</span><span class="sxs-lookup"><span data-stu-id="272bf-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="272bf-132">Lo strumento Secret Manager astrae i dettagli di implementazione, ad esempio dove e come vengono archiviati i valori.</span><span class="sxs-lookup"><span data-stu-id="272bf-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="272bf-133">È possibile utilizzare lo strumento senza conoscere questi dettagli di implementazione.</span><span class="sxs-lookup"><span data-stu-id="272bf-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="272bf-134">I valori vengono archiviati in un file di configurazione JSON in una cartella del profilo utente protetta dal sistema nel computer locale:The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span><span class="sxs-lookup"><span data-stu-id="272bf-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="272bf-135">Windows</span><span class="sxs-lookup"><span data-stu-id="272bf-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="272bf-136">Percorso del file system:</span><span class="sxs-lookup"><span data-stu-id="272bf-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="272bf-137">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="272bf-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="272bf-138">Percorso del file system:</span><span class="sxs-lookup"><span data-stu-id="272bf-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="272bf-139">Nei percorsi dei file `<user_secrets_id>` precedenti `UserSecretsId` sostituire con il valore specificato nel file *con estensione csproj.*</span><span class="sxs-lookup"><span data-stu-id="272bf-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="272bf-140">Non scrivere codice che dipende dalla posizione o dal formato dei dati salvati con lo strumento Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="272bf-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="272bf-141">Questi dettagli di implementazione possono cambiare.</span><span class="sxs-lookup"><span data-stu-id="272bf-141">These implementation details may change.</span></span> <span data-ttu-id="272bf-142">Ad esempio, i valori segreti non sono crittografati, ma potrebbero essere in futuro.</span><span class="sxs-lookup"><span data-stu-id="272bf-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="272bf-143">Abilitare l'archiviazione segretaEnable secret storage</span><span class="sxs-lookup"><span data-stu-id="272bf-143">Enable secret storage</span></span>

<span data-ttu-id="272bf-144">Lo strumento Secret Manager si baserà sulle impostazioni di configurazione specifiche del progetto archiviate nel profilo utente.</span><span class="sxs-lookup"><span data-stu-id="272bf-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="272bf-145">Lo strumento Secret `init` Manager include un comando in .NET Core SDK 3.0.100 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="272bf-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="272bf-146">Per utilizzare i segreti utente, eseguire il comando seguente nella directory del progetto:</span><span class="sxs-lookup"><span data-stu-id="272bf-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="272bf-147">Il comando precedente `UserSecretsId` aggiunge un `PropertyGroup` elemento all'interno di un file *con estensione csproj.*</span><span class="sxs-lookup"><span data-stu-id="272bf-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="272bf-148">Per impostazione predefinita, `UserSecretsId` il testo interno di è un GUID.</span><span class="sxs-lookup"><span data-stu-id="272bf-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="272bf-149">Il testo interno è arbitrario, ma è univoco per il progetto.</span><span class="sxs-lookup"><span data-stu-id="272bf-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="272bf-150">In Visual Studio fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci segreti utente** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="272bf-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="272bf-151">Questo gesto `UserSecretsId` aggiunge un elemento, popolato con un GUID, al file *con estensione csproj.*</span><span class="sxs-lookup"><span data-stu-id="272bf-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="272bf-152">Impostare un segreto</span><span class="sxs-lookup"><span data-stu-id="272bf-152">Set a secret</span></span>

<span data-ttu-id="272bf-153">Definire un segreto dell'app costituito da una chiave e dal relativo valore.</span><span class="sxs-lookup"><span data-stu-id="272bf-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="272bf-154">Il segreto è associato `UserSecretsId` al valore del progetto.</span><span class="sxs-lookup"><span data-stu-id="272bf-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="272bf-155">Ad esempio, eseguire il comando seguente dalla directory in cui è presente il file *con estensione csproj:*</span><span class="sxs-lookup"><span data-stu-id="272bf-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="272bf-156">Nell'esempio precedente, i due `Movies` punti indicano che `ServiceApiKey` è un valore letterale oggetto con una proprietà.</span><span class="sxs-lookup"><span data-stu-id="272bf-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="272bf-157">Lo strumento Secret Manager può essere utilizzato anche da altre directory.</span><span class="sxs-lookup"><span data-stu-id="272bf-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="272bf-158">Utilizzare `--project` l'opzione per fornire il percorso del file system in cui è presente il file *con estensione csproj.*</span><span class="sxs-lookup"><span data-stu-id="272bf-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="272bf-159">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="272bf-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="272bf-160">Appiattimento della struttura JSON in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="272bf-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="272bf-161">Il movimento **Gestisci segreti utente** di Visual Studio apre un file *secrets.json* nell'editor di testo.</span><span class="sxs-lookup"><span data-stu-id="272bf-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="272bf-162">Sostituire il contenuto di *secrets.json* con le coppie chiave-valore da archiviare.</span><span class="sxs-lookup"><span data-stu-id="272bf-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="272bf-163">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="272bf-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="272bf-164">La struttura JSON viene appiattita dopo le modifiche tramite `dotnet user-secrets remove` o `dotnet user-secrets set`.</span><span class="sxs-lookup"><span data-stu-id="272bf-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="272bf-165">Ad esempio, `dotnet user-secrets remove "Movies:ConnectionString"` l'esecuzione comprime il valore letterale dell'oggetto. `Movies`</span><span class="sxs-lookup"><span data-stu-id="272bf-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="272bf-166">Il file modificato è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="272bf-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="272bf-167">Impostare più segreti</span><span class="sxs-lookup"><span data-stu-id="272bf-167">Set multiple secrets</span></span>

<span data-ttu-id="272bf-168">È possibile impostare un batch di `set` segreti eseguendo il piping JSON al comando.</span><span class="sxs-lookup"><span data-stu-id="272bf-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="272bf-169">Nell'esempio seguente, il contenuto del file *input.json* viene reindirizzato al `set` comando.</span><span class="sxs-lookup"><span data-stu-id="272bf-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="272bf-170">Windows</span><span class="sxs-lookup"><span data-stu-id="272bf-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="272bf-171">Aprire una shell dei comandi ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="272bf-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="272bf-172">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="272bf-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="272bf-173">Aprire una shell dei comandi ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="272bf-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="272bf-174">Accedi a un segreto</span><span class="sxs-lookup"><span data-stu-id="272bf-174">Access a secret</span></span>

<span data-ttu-id="272bf-175">[L'API di configurazione di ASP.NET core](xref:fundamentals/configuration/index) fornisce l'accesso ai segreti di Secret Manager.The You You Code Base Configuration API provides access to Secret Manager secrets.</span><span class="sxs-lookup"><span data-stu-id="272bf-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="272bf-176">L'origine di configurazione dei segreti utente viene <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> aggiunta automaticamente in modalità di sviluppo quando il progetto chiama per inizializzare una nuova istanza dell'host con i valori predefiniti preconfigurati.</span><span class="sxs-lookup"><span data-stu-id="272bf-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="272bf-177">`CreateDefaultBuilder`chiamate <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>il è :</span><span class="sxs-lookup"><span data-stu-id="272bf-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="272bf-178">Quando `CreateDefaultBuilder` non viene chiamato, aggiungere l'origine <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>di configurazione dei segreti utente in modo esplicito chiamando .</span><span class="sxs-lookup"><span data-stu-id="272bf-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="272bf-179">Chiama `AddUserSecrets` solo quando l'app viene eseguita nell'ambiente di sviluppo, come illustrato nell'esempio seguente:Call only when the app runs in the Development environment, as shown in the following example:</span><span class="sxs-lookup"><span data-stu-id="272bf-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="272bf-180">I segreti utente possono `Configuration` essere recuperati tramite l'API:User secrets can be retrieved via the API:</span><span class="sxs-lookup"><span data-stu-id="272bf-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="272bf-181">Mappare i segreti a un POCO</span><span class="sxs-lookup"><span data-stu-id="272bf-181">Map secrets to a POCO</span></span>

<span data-ttu-id="272bf-182">Il mapping di un intero valore letterale oggetto a un POCO (una semplice classe .NET con proprietà) è utile per aggregare le proprietà correlate.</span><span class="sxs-lookup"><span data-stu-id="272bf-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="272bf-183">Per eseguire il mapping dei segreti `Configuration` precedenti a un POCO, usare la funzionalità di [associazione dell'oggetto grafico](xref:fundamentals/configuration/index#bind-to-an-object-graph) dell'API.</span><span class="sxs-lookup"><span data-stu-id="272bf-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="272bf-184">Il codice seguente esegue `MovieSettings` l'associazione a `ServiceApiKey` un POCO personalizzato e accede al valore della proprietà:</span><span class="sxs-lookup"><span data-stu-id="272bf-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="272bf-185">I `Movies:ConnectionString` `Movies:ServiceApiKey` segreti e sono mappati `MovieSettings`alle rispettive proprietà in :</span><span class="sxs-lookup"><span data-stu-id="272bf-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="272bf-186">Sostituzione di stringhe con segreti</span><span class="sxs-lookup"><span data-stu-id="272bf-186">String replacement with secrets</span></span>

<span data-ttu-id="272bf-187">L'archiviazione delle password in testo normale non è sicura.</span><span class="sxs-lookup"><span data-stu-id="272bf-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="272bf-188">Ad esempio, una stringa di connessione al database archiviata in appsettings.json può includere una password per l'utente specificato:For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span><span class="sxs-lookup"><span data-stu-id="272bf-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="272bf-189">Un approccio più sicuro consiste nell'archiviare la password come segreto.</span><span class="sxs-lookup"><span data-stu-id="272bf-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="272bf-190">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="272bf-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="272bf-191">Rimuovere `Password` la coppia chiave-valore dalla stringa di connessione in *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="272bf-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="272bf-192">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="272bf-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="272bf-193">Il valore del segreto può <xref:System.Data.SqlClient.SqlConnectionStringBuilder> essere <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> impostato sulla proprietà di un oggetto per completare la stringa di connessione:The secret's value can be set on a object's property to complete the connection string:</span><span class="sxs-lookup"><span data-stu-id="272bf-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="272bf-194">Elencare i segreti</span><span class="sxs-lookup"><span data-stu-id="272bf-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="272bf-195">Eseguire il comando seguente dalla directory in cui è presente il file *con estensione csproj:*</span><span class="sxs-lookup"><span data-stu-id="272bf-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="272bf-196">Viene visualizzato l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="272bf-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="272bf-197">Nell'esempio precedente, i due punti nei nomi delle chiavi indicano la gerarchia di oggetti all'interno di *secrets.json*.</span><span class="sxs-lookup"><span data-stu-id="272bf-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="272bf-198">Rimuovere un singolo segreto</span><span class="sxs-lookup"><span data-stu-id="272bf-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="272bf-199">Eseguire il comando seguente dalla directory in cui è presente il file *con estensione csproj:*</span><span class="sxs-lookup"><span data-stu-id="272bf-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="272bf-200">Il file *secrets.json* dell'app è stato modificato per `MoviesConnectionString` rimuovere la coppia chiave-valore associata alla chiave:</span><span class="sxs-lookup"><span data-stu-id="272bf-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="272bf-201">`dotnet user-secrets list`viene visualizzato il seguente messaggio:</span><span class="sxs-lookup"><span data-stu-id="272bf-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="272bf-202">Rimuovere tutti i segreti</span><span class="sxs-lookup"><span data-stu-id="272bf-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="272bf-203">Eseguire il comando seguente dalla directory in cui è presente il file *con estensione csproj:*</span><span class="sxs-lookup"><span data-stu-id="272bf-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="272bf-204">Tutti i segreti utente per l'app sono stati eliminati dal file *secrets.json:*</span><span class="sxs-lookup"><span data-stu-id="272bf-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="272bf-205">In `dotnet user-secrets list` esecuzione viene visualizzato il seguente messaggio:</span><span class="sxs-lookup"><span data-stu-id="272bf-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="272bf-206">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="272bf-206">Additional resources</span></span>

* <span data-ttu-id="272bf-207">Vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) per informazioni sull'accesso a Secret Manager da IIS.</span><span class="sxs-lookup"><span data-stu-id="272bf-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="272bf-208">Di [Rick Anderson](https://twitter.com/RickAndMSFT), Daniel [Roth](https://github.com/danroth27), e [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="272bf-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="272bf-209">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="272bf-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="272bf-210">Questo documento illustra le tecniche per l'archiviazione e il recupero di dati sensibili durante lo sviluppo di un'app ASP.NET Core in un computer di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="272bf-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="272bf-211">Non archiviare mai password o altri dati sensibili nel codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="272bf-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="272bf-212">I segreti di produzione non devono essere utilizzati per lo sviluppo o il test.</span><span class="sxs-lookup"><span data-stu-id="272bf-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="272bf-213">I segreti non devono essere distribuiti con l'app.</span><span class="sxs-lookup"><span data-stu-id="272bf-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="272bf-214">Al contrario, i segreti devono essere resi disponibili nell'ambiente di produzione tramite un mezzo controllato come le variabili di ambiente, l'insieme di credenziali delle chiavi di Azure e così via. È possibile archiviare e proteggere i segreti di test e produzione di Azure con il provider di [configurazione dell'insieme](xref:security/key-vault-configuration)di credenziali delle chiavi di Azure.You can store and protect Azure test and production secrets with the Azure Key Vault configuration provider .</span><span class="sxs-lookup"><span data-stu-id="272bf-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="272bf-215">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="272bf-215">Environment variables</span></span>

<span data-ttu-id="272bf-216">Le variabili di ambiente vengono usate per evitare l'archiviazione di segreti dell'app nel codice o nei file di configurazione locali.</span><span class="sxs-lookup"><span data-stu-id="272bf-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="272bf-217">Le variabili di ambiente eseguono l'override dei valori di configurazione per tutte le origini di configurazione specificate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="272bf-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="272bf-218">Si consideri un'app Web ASP.NET Core in cui è abilitata la sicurezza **degli account utente singoli.**</span><span class="sxs-lookup"><span data-stu-id="272bf-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="272bf-219">Una stringa di connessione al database predefinita è inclusa nel `DefaultConnection`file *appsettings.json* del progetto con la chiave .</span><span class="sxs-lookup"><span data-stu-id="272bf-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="272bf-220">La stringa di connessione predefinita è per LocalDB, che viene eseguito in modalità utente e non richiede una password.</span><span class="sxs-lookup"><span data-stu-id="272bf-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="272bf-221">Durante la distribuzione `DefaultConnection` dell'app, il valore della chiave può essere sostituito con il valore di una variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="272bf-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="272bf-222">La variabile di ambiente può archiviare la stringa di connessione completa con credenziali riservate.</span><span class="sxs-lookup"><span data-stu-id="272bf-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="272bf-223">Le variabili di ambiente vengono in genere archiviate in testo normale e non crittografato.</span><span class="sxs-lookup"><span data-stu-id="272bf-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="272bf-224">Se il computer o il processo è compromesso, le variabili di ambiente sono accessibili da parti non attendibili.</span><span class="sxs-lookup"><span data-stu-id="272bf-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="272bf-225">Potrebbero essere necessarie misure aggiuntive per impedire la divulgazione dei segreti utente.</span><span class="sxs-lookup"><span data-stu-id="272bf-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="272bf-226">Responsabile Segreto</span><span class="sxs-lookup"><span data-stu-id="272bf-226">Secret Manager</span></span>

<span data-ttu-id="272bf-227">Lo strumento Secret Manager memorizza i dati sensibili durante lo sviluppo di un progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="272bf-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="272bf-228">In questo contesto, una parte di dati sensibili è un segreto dell'app.</span><span class="sxs-lookup"><span data-stu-id="272bf-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="272bf-229">I segreti dell'app vengono archiviati in un percorso separato dall'albero del progetto.</span><span class="sxs-lookup"><span data-stu-id="272bf-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="272bf-230">I segreti dell'app sono associati a un progetto specifico o condivisi tra più progetti.</span><span class="sxs-lookup"><span data-stu-id="272bf-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="272bf-231">I segreti dell'app non vengono archiviati nel controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="272bf-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="272bf-232">Lo strumento Secret Manager non crittografa i segreti archiviati e non deve essere considerato come un archivio attendibile.</span><span class="sxs-lookup"><span data-stu-id="272bf-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="272bf-233">E 'solo per scopi di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="272bf-233">It's for development purposes only.</span></span> <span data-ttu-id="272bf-234">Le chiavi e i valori vengono archiviati in un file di configurazione JSON nella directory del profilo utente.</span><span class="sxs-lookup"><span data-stu-id="272bf-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="272bf-235">Funzionamento dello strumento Secret Manager</span><span class="sxs-lookup"><span data-stu-id="272bf-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="272bf-236">Lo strumento Secret Manager astrae i dettagli di implementazione, ad esempio dove e come vengono archiviati i valori.</span><span class="sxs-lookup"><span data-stu-id="272bf-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="272bf-237">È possibile utilizzare lo strumento senza conoscere questi dettagli di implementazione.</span><span class="sxs-lookup"><span data-stu-id="272bf-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="272bf-238">I valori vengono archiviati in un file di configurazione JSON in una cartella del profilo utente protetta dal sistema nel computer locale:The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span><span class="sxs-lookup"><span data-stu-id="272bf-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="272bf-239">Windows</span><span class="sxs-lookup"><span data-stu-id="272bf-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="272bf-240">Percorso del file system:</span><span class="sxs-lookup"><span data-stu-id="272bf-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="272bf-241">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="272bf-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="272bf-242">Percorso del file system:</span><span class="sxs-lookup"><span data-stu-id="272bf-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="272bf-243">Nei percorsi dei file `<user_secrets_id>` precedenti `UserSecretsId` sostituire con il valore specificato nel file *con estensione csproj.*</span><span class="sxs-lookup"><span data-stu-id="272bf-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="272bf-244">Non scrivere codice che dipende dalla posizione o dal formato dei dati salvati con lo strumento Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="272bf-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="272bf-245">Questi dettagli di implementazione possono cambiare.</span><span class="sxs-lookup"><span data-stu-id="272bf-245">These implementation details may change.</span></span> <span data-ttu-id="272bf-246">Ad esempio, i valori segreti non sono crittografati, ma potrebbero essere in futuro.</span><span class="sxs-lookup"><span data-stu-id="272bf-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="272bf-247">Abilitare l'archiviazione segretaEnable secret storage</span><span class="sxs-lookup"><span data-stu-id="272bf-247">Enable secret storage</span></span>

<span data-ttu-id="272bf-248">Lo strumento Secret Manager si baserà sulle impostazioni di configurazione specifiche del progetto archiviate nel profilo utente.</span><span class="sxs-lookup"><span data-stu-id="272bf-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="272bf-249">Per utilizzare i segreti `UserSecretsId` utente, `PropertyGroup` definire un elemento all'interno di un file *con estensione csproj.*</span><span class="sxs-lookup"><span data-stu-id="272bf-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="272bf-250">Il testo `UserSecretsId` interno di è arbitrario, ma è univoco per il progetto.</span><span class="sxs-lookup"><span data-stu-id="272bf-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="272bf-251">Gli sviluppatori generano `UserSecretsId`in genere un GUID per il file .</span><span class="sxs-lookup"><span data-stu-id="272bf-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="272bf-252">In Visual Studio fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci segreti utente** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="272bf-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="272bf-253">Questo gesto `UserSecretsId` aggiunge un elemento, popolato con un GUID, al file *con estensione csproj.*</span><span class="sxs-lookup"><span data-stu-id="272bf-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="272bf-254">Impostare un segreto</span><span class="sxs-lookup"><span data-stu-id="272bf-254">Set a secret</span></span>

<span data-ttu-id="272bf-255">Definire un segreto dell'app costituito da una chiave e dal relativo valore.</span><span class="sxs-lookup"><span data-stu-id="272bf-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="272bf-256">Il segreto è associato `UserSecretsId` al valore del progetto.</span><span class="sxs-lookup"><span data-stu-id="272bf-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="272bf-257">Ad esempio, eseguire il comando seguente dalla directory in cui è presente il file *con estensione csproj:*</span><span class="sxs-lookup"><span data-stu-id="272bf-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="272bf-258">Nell'esempio precedente, i due `Movies` punti indicano che `ServiceApiKey` è un valore letterale oggetto con una proprietà.</span><span class="sxs-lookup"><span data-stu-id="272bf-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="272bf-259">Lo strumento Secret Manager può essere utilizzato anche da altre directory.</span><span class="sxs-lookup"><span data-stu-id="272bf-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="272bf-260">Utilizzare `--project` l'opzione per fornire il percorso del file system in cui è presente il file *con estensione csproj.*</span><span class="sxs-lookup"><span data-stu-id="272bf-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="272bf-261">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="272bf-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="272bf-262">Appiattimento della struttura JSON in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="272bf-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="272bf-263">Il movimento **Gestisci segreti utente** di Visual Studio apre un file *secrets.json* nell'editor di testo.</span><span class="sxs-lookup"><span data-stu-id="272bf-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="272bf-264">Sostituire il contenuto di *secrets.json* con le coppie chiave-valore da archiviare.</span><span class="sxs-lookup"><span data-stu-id="272bf-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="272bf-265">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="272bf-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="272bf-266">La struttura JSON viene appiattita dopo le modifiche tramite `dotnet user-secrets remove` o `dotnet user-secrets set`.</span><span class="sxs-lookup"><span data-stu-id="272bf-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="272bf-267">Ad esempio, `dotnet user-secrets remove "Movies:ConnectionString"` l'esecuzione comprime il valore letterale dell'oggetto. `Movies`</span><span class="sxs-lookup"><span data-stu-id="272bf-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="272bf-268">Il file modificato è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="272bf-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="272bf-269">Impostare più segreti</span><span class="sxs-lookup"><span data-stu-id="272bf-269">Set multiple secrets</span></span>

<span data-ttu-id="272bf-270">È possibile impostare un batch di `set` segreti eseguendo il piping JSON al comando.</span><span class="sxs-lookup"><span data-stu-id="272bf-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="272bf-271">Nell'esempio seguente, il contenuto del file *input.json* viene reindirizzato al `set` comando.</span><span class="sxs-lookup"><span data-stu-id="272bf-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="272bf-272">Windows</span><span class="sxs-lookup"><span data-stu-id="272bf-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="272bf-273">Aprire una shell dei comandi ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="272bf-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="272bf-274">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="272bf-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="272bf-275">Aprire una shell dei comandi ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="272bf-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="272bf-276">Accedi a un segreto</span><span class="sxs-lookup"><span data-stu-id="272bf-276">Access a secret</span></span>

<span data-ttu-id="272bf-277">[L'API di configurazione di ASP.NET core](xref:fundamentals/configuration/index) fornisce l'accesso ai segreti di Secret Manager.The You You Code Base Configuration API provides access to Secret Manager secrets.</span><span class="sxs-lookup"><span data-stu-id="272bf-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="272bf-278">Se il progetto è destinato a .NET Framework, installare il pacchetto [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet.</span><span class="sxs-lookup"><span data-stu-id="272bf-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="272bf-279">In ASP.NET Core 2.0 o versioni successive, l'origine di configurazione <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> dei segreti utente viene aggiunta automaticamente in modalità di sviluppo quando il progetto chiama per inizializzare una nuova istanza dell'host con i valori predefiniti preconfigurati.</span><span class="sxs-lookup"><span data-stu-id="272bf-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="272bf-280">`CreateDefaultBuilder`chiamate <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>il è :</span><span class="sxs-lookup"><span data-stu-id="272bf-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="272bf-281">Quando `CreateDefaultBuilder` non viene chiamato, aggiungere l'origine <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> di `Startup` configurazione dei segreti utente in modo esplicito chiamando il costruttore.</span><span class="sxs-lookup"><span data-stu-id="272bf-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="272bf-282">Chiama `AddUserSecrets` solo quando l'app viene eseguita nell'ambiente di sviluppo, come illustrato nell'esempio seguente:Call only when the app runs in the Development environment, as shown in the following example:</span><span class="sxs-lookup"><span data-stu-id="272bf-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="272bf-283">I segreti utente possono `Configuration` essere recuperati tramite l'API:User secrets can be retrieved via the API:</span><span class="sxs-lookup"><span data-stu-id="272bf-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="272bf-284">Mappare i segreti a un POCO</span><span class="sxs-lookup"><span data-stu-id="272bf-284">Map secrets to a POCO</span></span>

<span data-ttu-id="272bf-285">Il mapping di un intero valore letterale oggetto a un POCO (una semplice classe .NET con proprietà) è utile per aggregare le proprietà correlate.</span><span class="sxs-lookup"><span data-stu-id="272bf-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="272bf-286">Per eseguire il mapping dei segreti `Configuration` precedenti a un POCO, usare la funzionalità di [associazione dell'oggetto grafico](xref:fundamentals/configuration/index#bind-to-an-object-graph) dell'API.</span><span class="sxs-lookup"><span data-stu-id="272bf-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="272bf-287">Il codice seguente esegue `MovieSettings` l'associazione a `ServiceApiKey` un POCO personalizzato e accede al valore della proprietà:</span><span class="sxs-lookup"><span data-stu-id="272bf-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="272bf-288">I `Movies:ConnectionString` `Movies:ServiceApiKey` segreti e sono mappati `MovieSettings`alle rispettive proprietà in :</span><span class="sxs-lookup"><span data-stu-id="272bf-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="272bf-289">Sostituzione di stringhe con segreti</span><span class="sxs-lookup"><span data-stu-id="272bf-289">String replacement with secrets</span></span>

<span data-ttu-id="272bf-290">L'archiviazione delle password in testo normale non è sicura.</span><span class="sxs-lookup"><span data-stu-id="272bf-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="272bf-291">Ad esempio, una stringa di connessione al database archiviata in appsettings.json può includere una password per l'utente specificato:For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span><span class="sxs-lookup"><span data-stu-id="272bf-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="272bf-292">Un approccio più sicuro consiste nell'archiviare la password come segreto.</span><span class="sxs-lookup"><span data-stu-id="272bf-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="272bf-293">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="272bf-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="272bf-294">Rimuovere `Password` la coppia chiave-valore dalla stringa di connessione in *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="272bf-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="272bf-295">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="272bf-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="272bf-296">Il valore del segreto può <xref:System.Data.SqlClient.SqlConnectionStringBuilder> essere <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> impostato sulla proprietà di un oggetto per completare la stringa di connessione:The secret's value can be set on a object's property to complete the connection string:</span><span class="sxs-lookup"><span data-stu-id="272bf-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="272bf-297">Elencare i segreti</span><span class="sxs-lookup"><span data-stu-id="272bf-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="272bf-298">Eseguire il comando seguente dalla directory in cui è presente il file *con estensione csproj:*</span><span class="sxs-lookup"><span data-stu-id="272bf-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="272bf-299">Viene visualizzato l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="272bf-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="272bf-300">Nell'esempio precedente, i due punti nei nomi delle chiavi indicano la gerarchia di oggetti all'interno di *secrets.json*.</span><span class="sxs-lookup"><span data-stu-id="272bf-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="272bf-301">Rimuovere un singolo segreto</span><span class="sxs-lookup"><span data-stu-id="272bf-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="272bf-302">Eseguire il comando seguente dalla directory in cui è presente il file *con estensione csproj:*</span><span class="sxs-lookup"><span data-stu-id="272bf-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="272bf-303">Il file *secrets.json* dell'app è stato modificato per `MoviesConnectionString` rimuovere la coppia chiave-valore associata alla chiave:</span><span class="sxs-lookup"><span data-stu-id="272bf-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="272bf-304">In `dotnet user-secrets list` esecuzione viene visualizzato il seguente messaggio:</span><span class="sxs-lookup"><span data-stu-id="272bf-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="272bf-305">Rimuovere tutti i segreti</span><span class="sxs-lookup"><span data-stu-id="272bf-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="272bf-306">Eseguire il comando seguente dalla directory in cui è presente il file *con estensione csproj:*</span><span class="sxs-lookup"><span data-stu-id="272bf-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="272bf-307">Tutti i segreti utente per l'app sono stati eliminati dal file *secrets.json:*</span><span class="sxs-lookup"><span data-stu-id="272bf-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="272bf-308">In `dotnet user-secrets list` esecuzione viene visualizzato il seguente messaggio:</span><span class="sxs-lookup"><span data-stu-id="272bf-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="272bf-309">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="272bf-309">Additional resources</span></span>

* <span data-ttu-id="272bf-310">Vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) per informazioni sull'accesso a Secret Manager da IIS.</span><span class="sxs-lookup"><span data-stu-id="272bf-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end