---
title: Archiviazione sicura dei segreti delle app in fase di sviluppo in ASP.NET Core
author: rick-anderson
description: Informazioni su come archiviare e recuperare informazioni riservate come segreti dell'app durante lo sviluppo di un'app ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/app-secrets
ms.openlocfilehash: 7508aebcda4e14812140f13ece635428908a4abb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776682"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="8b40b-103">Archiviazione sicura dei segreti delle app in fase di sviluppo in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b40b-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8b40b-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)e [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="8b40b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="8b40b-105">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8b40b-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8b40b-106">Questo documento illustra le tecniche per l'archiviazione e il recupero di dati sensibili durante lo sviluppo di un'app ASP.NET Core in un computer di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="8b40b-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="8b40b-107">Non archiviare mai le password o altri dati sensibili nel codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="8b40b-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="8b40b-108">I segreti di produzione non devono essere usati per lo sviluppo o il test.</span><span class="sxs-lookup"><span data-stu-id="8b40b-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="8b40b-109">I segreti non devono essere distribuiti con l'app.</span><span class="sxs-lookup"><span data-stu-id="8b40b-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="8b40b-110">Al contrario, i segreti devono essere resi disponibili nell'ambiente di produzione tramite un mezzo controllato, come le variabili di ambiente, Azure Key Vault e così via. È possibile archiviare e proteggere i segreti di test e di produzione di Azure con il [provider di configurazione Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="8b40b-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="8b40b-111">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="8b40b-111">Environment variables</span></span>

<span data-ttu-id="8b40b-112">Le variabili di ambiente vengono usate per evitare l'archiviazione di segreti dell'app nel codice o nei file di configurazione locali.</span><span class="sxs-lookup"><span data-stu-id="8b40b-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="8b40b-113">Le variabili di ambiente sostituiscono i valori di configurazione per tutte le origini di configurazione precedentemente specificate.</span><span class="sxs-lookup"><span data-stu-id="8b40b-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="8b40b-114">Si consideri un'app Web ASP.NET Core in cui è abilitata la sicurezza dei **singoli account utente** .</span><span class="sxs-lookup"><span data-stu-id="8b40b-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="8b40b-115">Una stringa di connessione al database predefinita è inclusa nel file *appSettings. JSON* del progetto con la `DefaultConnection`chiave.</span><span class="sxs-lookup"><span data-stu-id="8b40b-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="8b40b-116">La stringa di connessione predefinita è per il database locale, che viene eseguito in modalità utente e non richiede una password.</span><span class="sxs-lookup"><span data-stu-id="8b40b-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="8b40b-117">Durante la distribuzione dell'app `DefaultConnection` , il valore della chiave può essere sostituito con il valore di una variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="8b40b-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="8b40b-118">La variabile di ambiente può archiviare la stringa di connessione completa con credenziali riservate.</span><span class="sxs-lookup"><span data-stu-id="8b40b-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="8b40b-119">Le variabili di ambiente vengono in genere archiviate in testo normale e non crittografato.</span><span class="sxs-lookup"><span data-stu-id="8b40b-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="8b40b-120">Se il computer o il processo è compromesso, le variabili di ambiente possono essere accessibili da entità non attendibili.</span><span class="sxs-lookup"><span data-stu-id="8b40b-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="8b40b-121">Potrebbero essere necessarie misure aggiuntive per impedire la divulgazione di segreti utente.</span><span class="sxs-lookup"><span data-stu-id="8b40b-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="8b40b-122">Gestione segreta</span><span class="sxs-lookup"><span data-stu-id="8b40b-122">Secret Manager</span></span>

<span data-ttu-id="8b40b-123">Lo strumento Gestione Secret archivia i dati sensibili durante lo sviluppo di un progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8b40b-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="8b40b-124">In questo contesto, una parte di dati sensibili è un segreto dell'app.</span><span class="sxs-lookup"><span data-stu-id="8b40b-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="8b40b-125">I segreti dell'app vengono archiviati in un percorso separato dall'albero del progetto.</span><span class="sxs-lookup"><span data-stu-id="8b40b-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="8b40b-126">I segreti dell'app sono associati a un progetto specifico o condivisi tra più progetti.</span><span class="sxs-lookup"><span data-stu-id="8b40b-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="8b40b-127">I segreti dell'app non vengono archiviati nel controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="8b40b-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="8b40b-128">Lo strumento Secret Manager non crittografa i segreti archiviati e non deve essere considerato come un archivio attendibile.</span><span class="sxs-lookup"><span data-stu-id="8b40b-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="8b40b-129">È solo a scopo di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="8b40b-129">It's for development purposes only.</span></span> <span data-ttu-id="8b40b-130">Le chiavi e i valori vengono archiviati in un file di configurazione JSON nella directory del profilo utente.</span><span class="sxs-lookup"><span data-stu-id="8b40b-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="8b40b-131">Funzionamento dello strumento di gestione dei segreti</span><span class="sxs-lookup"><span data-stu-id="8b40b-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="8b40b-132">Lo strumento di gestione dei segreti estrae i dettagli di implementazione, ad esempio dove e come vengono archiviati i valori.</span><span class="sxs-lookup"><span data-stu-id="8b40b-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="8b40b-133">È possibile utilizzare lo strumento senza conoscere i dettagli di implementazione.</span><span class="sxs-lookup"><span data-stu-id="8b40b-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="8b40b-134">I valori vengono archiviati in un file di configurazione JSON in una cartella del profilo utente protetta dal sistema nel computer locale:</span><span class="sxs-lookup"><span data-stu-id="8b40b-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="8b40b-135">Windows</span><span class="sxs-lookup"><span data-stu-id="8b40b-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="8b40b-136">Percorso del file System:</span><span class="sxs-lookup"><span data-stu-id="8b40b-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="8b40b-137">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="8b40b-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="8b40b-138">Percorso del file System:</span><span class="sxs-lookup"><span data-stu-id="8b40b-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="8b40b-139">Nei percorsi di file precedenti sostituire `<user_secrets_id>` con il `UserSecretsId` valore specificato nel file con *estensione csproj* .</span><span class="sxs-lookup"><span data-stu-id="8b40b-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="8b40b-140">Non scrivere codice che dipende dalla posizione o dal formato dei dati salvati con lo strumento di gestione dei segreti.</span><span class="sxs-lookup"><span data-stu-id="8b40b-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="8b40b-141">Questi dettagli di implementazione possono cambiare.</span><span class="sxs-lookup"><span data-stu-id="8b40b-141">These implementation details may change.</span></span> <span data-ttu-id="8b40b-142">Ad esempio, i valori dei segreti non sono crittografati, ma potrebbero essere futuri.</span><span class="sxs-lookup"><span data-stu-id="8b40b-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="8b40b-143">Abilita archiviazione segreta</span><span class="sxs-lookup"><span data-stu-id="8b40b-143">Enable secret storage</span></span>

<span data-ttu-id="8b40b-144">Lo strumento Gestione Secret funziona sulle impostazioni di configurazione specifiche del progetto archiviate nel profilo utente.</span><span class="sxs-lookup"><span data-stu-id="8b40b-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="8b40b-145">Lo strumento di gestione dei segreti `init` include un comando in .NET Core SDK 3.0.100 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="8b40b-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="8b40b-146">Per usare i segreti utente, eseguire il comando seguente nella directory del progetto:</span><span class="sxs-lookup"><span data-stu-id="8b40b-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="8b40b-147">Il comando precedente aggiunge un `UserSecretsId` elemento all'interno `PropertyGroup` di un del file con estensione *csproj* .</span><span class="sxs-lookup"><span data-stu-id="8b40b-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="8b40b-148">Per impostazione predefinita, il testo interno `UserSecretsId` di è un GUID.</span><span class="sxs-lookup"><span data-stu-id="8b40b-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="8b40b-149">Il testo interno è arbitrario, ma è univoco per il progetto.</span><span class="sxs-lookup"><span data-stu-id="8b40b-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="8b40b-150">In Visual Studio fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci segreti utente** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="8b40b-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="8b40b-151">Questo movimento aggiunge un `UserSecretsId` elemento, popolato con un GUID, al file con *estensione csproj* .</span><span class="sxs-lookup"><span data-stu-id="8b40b-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="8b40b-152">Imposta un segreto</span><span class="sxs-lookup"><span data-stu-id="8b40b-152">Set a secret</span></span>

<span data-ttu-id="8b40b-153">Definire un segreto dell'app costituito da una chiave e dal relativo valore.</span><span class="sxs-lookup"><span data-stu-id="8b40b-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="8b40b-154">Il segreto è associato al `UserSecretsId` valore del progetto.</span><span class="sxs-lookup"><span data-stu-id="8b40b-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="8b40b-155">Ad esempio, eseguire il comando seguente dalla directory in cui è presente il file con *estensione csproj* :</span><span class="sxs-lookup"><span data-stu-id="8b40b-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="8b40b-156">Nell'esempio precedente, i due punti indicano che `Movies` è un valore letterale di oggetto con `ServiceApiKey` una proprietà.</span><span class="sxs-lookup"><span data-stu-id="8b40b-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="8b40b-157">Lo strumento Gestione Secret può essere usato anche da altre directory.</span><span class="sxs-lookup"><span data-stu-id="8b40b-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="8b40b-158">Utilizzare l' `--project` opzione per specificare il percorso di file System in cui è presente il file con *estensione csproj* .</span><span class="sxs-lookup"><span data-stu-id="8b40b-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="8b40b-159">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8b40b-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="8b40b-160">Flat Structure JSON in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b40b-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="8b40b-161">Il gesto di **gestione dei segreti utente** di Visual Studio apre un file *Secrets. JSON* nell'editor di testo.</span><span class="sxs-lookup"><span data-stu-id="8b40b-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="8b40b-162">Sostituire il contenuto di *Secrets. JSON* con le coppie chiave-valore da archiviare.</span><span class="sxs-lookup"><span data-stu-id="8b40b-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="8b40b-163">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8b40b-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="8b40b-164">La struttura JSON è bidimensionale dopo le modifiche `dotnet user-secrets remove` tramite `dotnet user-secrets set`o.</span><span class="sxs-lookup"><span data-stu-id="8b40b-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="8b40b-165">Ad esempio, l' `dotnet user-secrets remove "Movies:ConnectionString"` esecuzione comprime il `Movies` valore letterale dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="8b40b-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="8b40b-166">Il file modificato è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="8b40b-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="8b40b-167">Impostare più segreti</span><span class="sxs-lookup"><span data-stu-id="8b40b-167">Set multiple secrets</span></span>

<span data-ttu-id="8b40b-168">È possibile impostare un batch di segreti inviando tramite pipe JSON `set` al comando.</span><span class="sxs-lookup"><span data-stu-id="8b40b-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="8b40b-169">Nell'esempio seguente il contenuto del file *input. JSON* viene inviato tramite pipe al `set` comando.</span><span class="sxs-lookup"><span data-stu-id="8b40b-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="8b40b-170">Windows</span><span class="sxs-lookup"><span data-stu-id="8b40b-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="8b40b-171">Aprire una shell dei comandi ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="8b40b-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="8b40b-172">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="8b40b-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="8b40b-173">Aprire una shell dei comandi ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="8b40b-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="8b40b-174">Accedere a un segreto</span><span class="sxs-lookup"><span data-stu-id="8b40b-174">Access a secret</span></span>

<span data-ttu-id="8b40b-175">L' [API di configurazione ASP.NET Core](xref:fundamentals/configuration/index) fornisce l'accesso ai segreti di gestione segreti.</span><span class="sxs-lookup"><span data-stu-id="8b40b-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="8b40b-176">L'origine configurazione dei segreti utente viene aggiunta automaticamente in modalità di sviluppo quando il <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> progetto chiama per inizializzare una nuova istanza dell'host con impostazioni predefinite preconfigurate.</span><span class="sxs-lookup"><span data-stu-id="8b40b-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="8b40b-177">`CreateDefaultBuilder`chiama <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> è <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span><span class="sxs-lookup"><span data-stu-id="8b40b-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="8b40b-178">Quando `CreateDefaultBuilder` non viene chiamato, aggiungere l'origine di configurazione dei segreti utente in <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>modo esplicito chiamando.</span><span class="sxs-lookup"><span data-stu-id="8b40b-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="8b40b-179">Chiamare `AddUserSecrets` solo quando l'app è in esecuzione nell'ambiente di sviluppo, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="8b40b-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="8b40b-180">I segreti utente possono essere recuperati `Configuration` tramite l'API:</span><span class="sxs-lookup"><span data-stu-id="8b40b-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="8b40b-181">Mappare i segreti a un POCO</span><span class="sxs-lookup"><span data-stu-id="8b40b-181">Map secrets to a POCO</span></span>

<span data-ttu-id="8b40b-182">Il mapping di un intero valore letterale di oggetto a un oggetto POCO (una classe .NET semplice con proprietà) è utile per l'aggregazione di proprietà correlate.</span><span class="sxs-lookup"><span data-stu-id="8b40b-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="8b40b-183">Per eseguire il mapping dei segreti precedenti a un POCO, `Configuration` usare la funzionalità di [associazione dell'oggetto grafico](xref:fundamentals/configuration/index#bind-to-an-object-graph) dell'API.</span><span class="sxs-lookup"><span data-stu-id="8b40b-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="8b40b-184">Il codice seguente è associato a un oggetto `MovieSettings` poco personalizzato e accede al `ServiceApiKey` valore della proprietà:</span><span class="sxs-lookup"><span data-stu-id="8b40b-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="8b40b-185">I `Movies:ConnectionString` segreti `Movies:ServiceApiKey` e vengono mappati alle rispettive proprietà `MovieSettings`in:</span><span class="sxs-lookup"><span data-stu-id="8b40b-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="8b40b-186">Sostituzione di stringhe con segreti</span><span class="sxs-lookup"><span data-stu-id="8b40b-186">String replacement with secrets</span></span>

<span data-ttu-id="8b40b-187">L'archiviazione delle password in testo normale non è protetta.</span><span class="sxs-lookup"><span data-stu-id="8b40b-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="8b40b-188">Ad esempio, una stringa di connessione del database archiviata in *appSettings. JSON* può includere una password per l'utente specificato:</span><span class="sxs-lookup"><span data-stu-id="8b40b-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="8b40b-189">Un approccio più sicuro consiste nell'archiviare la password come segreto.</span><span class="sxs-lookup"><span data-stu-id="8b40b-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="8b40b-190">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8b40b-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="8b40b-191">Rimuovere la `Password` coppia chiave-valore dalla stringa di connessione in *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="8b40b-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="8b40b-192">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8b40b-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="8b40b-193">Il valore del segreto può essere impostato sulla <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> proprietà di un oggetto per completare la stringa di connessione:</span><span class="sxs-lookup"><span data-stu-id="8b40b-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="8b40b-194">Elenca i segreti</span><span class="sxs-lookup"><span data-stu-id="8b40b-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="8b40b-195">Eseguire il comando seguente dalla directory in cui è presente il file con *estensione csproj* :</span><span class="sxs-lookup"><span data-stu-id="8b40b-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="8b40b-196">Viene visualizzato l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="8b40b-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="8b40b-197">Nell'esempio precedente, i due punti nei nomi delle chiavi indicano la gerarchia di oggetti all'interno di *Secrets. JSON*.</span><span class="sxs-lookup"><span data-stu-id="8b40b-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="8b40b-198">Rimuovere un singolo segreto</span><span class="sxs-lookup"><span data-stu-id="8b40b-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="8b40b-199">Eseguire il comando seguente dalla directory in cui è presente il file con *estensione csproj* :</span><span class="sxs-lookup"><span data-stu-id="8b40b-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="8b40b-200">Il file *Secrets. JSON* dell'app è stato modificato per rimuovere la coppia chiave-valore associata `MoviesConnectionString` alla chiave:</span><span class="sxs-lookup"><span data-stu-id="8b40b-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="8b40b-201">`dotnet user-secrets list`Visualizza il messaggio seguente:</span><span class="sxs-lookup"><span data-stu-id="8b40b-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="8b40b-202">Rimuovi tutti i segreti</span><span class="sxs-lookup"><span data-stu-id="8b40b-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="8b40b-203">Eseguire il comando seguente dalla directory in cui è presente il file con *estensione csproj* :</span><span class="sxs-lookup"><span data-stu-id="8b40b-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="8b40b-204">Tutti i segreti utente per l'app sono stati eliminati dal file *Secrets. JSON* :</span><span class="sxs-lookup"><span data-stu-id="8b40b-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="8b40b-205">In `dotnet user-secrets list` esecuzione viene visualizzato il messaggio seguente:</span><span class="sxs-lookup"><span data-stu-id="8b40b-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="8b40b-206">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8b40b-206">Additional resources</span></span>

* <span data-ttu-id="8b40b-207">Per informazioni sull'accesso a gestione Secret da IIS, vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="8b40b-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b40b-208">Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)e [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="8b40b-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="8b40b-209">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8b40b-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8b40b-210">Questo documento illustra le tecniche per l'archiviazione e il recupero di dati sensibili durante lo sviluppo di un'app ASP.NET Core in un computer di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="8b40b-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="8b40b-211">Non archiviare mai le password o altri dati sensibili nel codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="8b40b-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="8b40b-212">I segreti di produzione non devono essere usati per lo sviluppo o il test.</span><span class="sxs-lookup"><span data-stu-id="8b40b-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="8b40b-213">I segreti non devono essere distribuiti con l'app.</span><span class="sxs-lookup"><span data-stu-id="8b40b-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="8b40b-214">Al contrario, i segreti devono essere resi disponibili nell'ambiente di produzione tramite un mezzo controllato, come le variabili di ambiente, Azure Key Vault e così via. È possibile archiviare e proteggere i segreti di test e di produzione di Azure con il [provider di configurazione Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="8b40b-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="8b40b-215">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="8b40b-215">Environment variables</span></span>

<span data-ttu-id="8b40b-216">Le variabili di ambiente vengono usate per evitare l'archiviazione di segreti dell'app nel codice o nei file di configurazione locali.</span><span class="sxs-lookup"><span data-stu-id="8b40b-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="8b40b-217">Le variabili di ambiente sostituiscono i valori di configurazione per tutte le origini di configurazione precedentemente specificate.</span><span class="sxs-lookup"><span data-stu-id="8b40b-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="8b40b-218">Si consideri un'app Web ASP.NET Core in cui è abilitata la sicurezza dei **singoli account utente** .</span><span class="sxs-lookup"><span data-stu-id="8b40b-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="8b40b-219">Una stringa di connessione al database predefinita è inclusa nel file *appSettings. JSON* del progetto con la `DefaultConnection`chiave.</span><span class="sxs-lookup"><span data-stu-id="8b40b-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="8b40b-220">La stringa di connessione predefinita è per il database locale, che viene eseguito in modalità utente e non richiede una password.</span><span class="sxs-lookup"><span data-stu-id="8b40b-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="8b40b-221">Durante la distribuzione dell'app `DefaultConnection` , il valore della chiave può essere sostituito con il valore di una variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="8b40b-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="8b40b-222">La variabile di ambiente può archiviare la stringa di connessione completa con credenziali riservate.</span><span class="sxs-lookup"><span data-stu-id="8b40b-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="8b40b-223">Le variabili di ambiente vengono in genere archiviate in testo normale e non crittografato.</span><span class="sxs-lookup"><span data-stu-id="8b40b-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="8b40b-224">Se il computer o il processo è compromesso, le variabili di ambiente possono essere accessibili da entità non attendibili.</span><span class="sxs-lookup"><span data-stu-id="8b40b-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="8b40b-225">Potrebbero essere necessarie misure aggiuntive per impedire la divulgazione di segreti utente.</span><span class="sxs-lookup"><span data-stu-id="8b40b-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="8b40b-226">Gestione segreta</span><span class="sxs-lookup"><span data-stu-id="8b40b-226">Secret Manager</span></span>

<span data-ttu-id="8b40b-227">Lo strumento Gestione Secret archivia i dati sensibili durante lo sviluppo di un progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8b40b-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="8b40b-228">In questo contesto, una parte di dati sensibili è un segreto dell'app.</span><span class="sxs-lookup"><span data-stu-id="8b40b-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="8b40b-229">I segreti dell'app vengono archiviati in un percorso separato dall'albero del progetto.</span><span class="sxs-lookup"><span data-stu-id="8b40b-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="8b40b-230">I segreti dell'app sono associati a un progetto specifico o condivisi tra più progetti.</span><span class="sxs-lookup"><span data-stu-id="8b40b-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="8b40b-231">I segreti dell'app non vengono archiviati nel controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="8b40b-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="8b40b-232">Lo strumento Secret Manager non crittografa i segreti archiviati e non deve essere considerato come un archivio attendibile.</span><span class="sxs-lookup"><span data-stu-id="8b40b-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="8b40b-233">È solo a scopo di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="8b40b-233">It's for development purposes only.</span></span> <span data-ttu-id="8b40b-234">Le chiavi e i valori vengono archiviati in un file di configurazione JSON nella directory del profilo utente.</span><span class="sxs-lookup"><span data-stu-id="8b40b-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="8b40b-235">Funzionamento dello strumento di gestione dei segreti</span><span class="sxs-lookup"><span data-stu-id="8b40b-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="8b40b-236">Lo strumento di gestione dei segreti estrae i dettagli di implementazione, ad esempio dove e come vengono archiviati i valori.</span><span class="sxs-lookup"><span data-stu-id="8b40b-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="8b40b-237">È possibile utilizzare lo strumento senza conoscere i dettagli di implementazione.</span><span class="sxs-lookup"><span data-stu-id="8b40b-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="8b40b-238">I valori vengono archiviati in un file di configurazione JSON in una cartella del profilo utente protetta dal sistema nel computer locale:</span><span class="sxs-lookup"><span data-stu-id="8b40b-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="8b40b-239">Windows</span><span class="sxs-lookup"><span data-stu-id="8b40b-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="8b40b-240">Percorso del file System:</span><span class="sxs-lookup"><span data-stu-id="8b40b-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="8b40b-241">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="8b40b-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="8b40b-242">Percorso del file System:</span><span class="sxs-lookup"><span data-stu-id="8b40b-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="8b40b-243">Nei percorsi di file precedenti sostituire `<user_secrets_id>` con il `UserSecretsId` valore specificato nel file con *estensione csproj* .</span><span class="sxs-lookup"><span data-stu-id="8b40b-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="8b40b-244">Non scrivere codice che dipende dalla posizione o dal formato dei dati salvati con lo strumento di gestione dei segreti.</span><span class="sxs-lookup"><span data-stu-id="8b40b-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="8b40b-245">Questi dettagli di implementazione possono cambiare.</span><span class="sxs-lookup"><span data-stu-id="8b40b-245">These implementation details may change.</span></span> <span data-ttu-id="8b40b-246">Ad esempio, i valori dei segreti non sono crittografati, ma potrebbero essere futuri.</span><span class="sxs-lookup"><span data-stu-id="8b40b-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="8b40b-247">Abilita archiviazione segreta</span><span class="sxs-lookup"><span data-stu-id="8b40b-247">Enable secret storage</span></span>

<span data-ttu-id="8b40b-248">Lo strumento Gestione Secret funziona sulle impostazioni di configurazione specifiche del progetto archiviate nel profilo utente.</span><span class="sxs-lookup"><span data-stu-id="8b40b-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="8b40b-249">Per usare i segreti utente, definire `UserSecretsId` un elemento in `PropertyGroup` un del file con estensione *csproj* .</span><span class="sxs-lookup"><span data-stu-id="8b40b-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="8b40b-250">Il testo interno di `UserSecretsId` è arbitrario, ma è univoco per il progetto.</span><span class="sxs-lookup"><span data-stu-id="8b40b-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="8b40b-251">Gli sviluppatori generano in genere un `UserSecretsId`GUID per il.</span><span class="sxs-lookup"><span data-stu-id="8b40b-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="8b40b-252">In Visual Studio fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci segreti utente** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="8b40b-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="8b40b-253">Questo movimento aggiunge un `UserSecretsId` elemento, popolato con un GUID, al file con *estensione csproj* .</span><span class="sxs-lookup"><span data-stu-id="8b40b-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="8b40b-254">Imposta un segreto</span><span class="sxs-lookup"><span data-stu-id="8b40b-254">Set a secret</span></span>

<span data-ttu-id="8b40b-255">Definire un segreto dell'app costituito da una chiave e dal relativo valore.</span><span class="sxs-lookup"><span data-stu-id="8b40b-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="8b40b-256">Il segreto è associato al `UserSecretsId` valore del progetto.</span><span class="sxs-lookup"><span data-stu-id="8b40b-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="8b40b-257">Ad esempio, eseguire il comando seguente dalla directory in cui è presente il file con *estensione csproj* :</span><span class="sxs-lookup"><span data-stu-id="8b40b-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="8b40b-258">Nell'esempio precedente, i due punti indicano che `Movies` è un valore letterale di oggetto con `ServiceApiKey` una proprietà.</span><span class="sxs-lookup"><span data-stu-id="8b40b-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="8b40b-259">Lo strumento Gestione Secret può essere usato anche da altre directory.</span><span class="sxs-lookup"><span data-stu-id="8b40b-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="8b40b-260">Utilizzare l' `--project` opzione per specificare il percorso di file System in cui è presente il file con *estensione csproj* .</span><span class="sxs-lookup"><span data-stu-id="8b40b-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="8b40b-261">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8b40b-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="8b40b-262">Flat Structure JSON in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b40b-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="8b40b-263">Il gesto di **gestione dei segreti utente** di Visual Studio apre un file *Secrets. JSON* nell'editor di testo.</span><span class="sxs-lookup"><span data-stu-id="8b40b-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="8b40b-264">Sostituire il contenuto di *Secrets. JSON* con le coppie chiave-valore da archiviare.</span><span class="sxs-lookup"><span data-stu-id="8b40b-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="8b40b-265">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8b40b-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="8b40b-266">La struttura JSON è bidimensionale dopo le modifiche `dotnet user-secrets remove` tramite `dotnet user-secrets set`o.</span><span class="sxs-lookup"><span data-stu-id="8b40b-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="8b40b-267">Ad esempio, l' `dotnet user-secrets remove "Movies:ConnectionString"` esecuzione comprime il `Movies` valore letterale dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="8b40b-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="8b40b-268">Il file modificato è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="8b40b-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="8b40b-269">Impostare più segreti</span><span class="sxs-lookup"><span data-stu-id="8b40b-269">Set multiple secrets</span></span>

<span data-ttu-id="8b40b-270">È possibile impostare un batch di segreti inviando tramite pipe JSON `set` al comando.</span><span class="sxs-lookup"><span data-stu-id="8b40b-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="8b40b-271">Nell'esempio seguente il contenuto del file *input. JSON* viene inviato tramite pipe al `set` comando.</span><span class="sxs-lookup"><span data-stu-id="8b40b-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="8b40b-272">Windows</span><span class="sxs-lookup"><span data-stu-id="8b40b-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="8b40b-273">Aprire una shell dei comandi ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="8b40b-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="8b40b-274">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="8b40b-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="8b40b-275">Aprire una shell dei comandi ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="8b40b-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="8b40b-276">Accedere a un segreto</span><span class="sxs-lookup"><span data-stu-id="8b40b-276">Access a secret</span></span>

<span data-ttu-id="8b40b-277">L' [API di configurazione ASP.NET Core](xref:fundamentals/configuration/index) fornisce l'accesso ai segreti di gestione segreti.</span><span class="sxs-lookup"><span data-stu-id="8b40b-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="8b40b-278">Se il progetto è destinato .NET Framework, installare il pacchetto NuGet [Microsoft. Extensions. Configuration. UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .</span><span class="sxs-lookup"><span data-stu-id="8b40b-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="8b40b-279">In ASP.NET Core 2,0 o versioni successive, l'origine configurazione dei segreti utente viene aggiunta automaticamente in modalità di sviluppo quando <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> il progetto chiama per inizializzare una nuova istanza dell'host con impostazioni predefinite preconfigurate.</span><span class="sxs-lookup"><span data-stu-id="8b40b-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="8b40b-280">`CreateDefaultBuilder`chiama <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> è <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span><span class="sxs-lookup"><span data-stu-id="8b40b-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="8b40b-281">Quando `CreateDefaultBuilder` non viene chiamato, aggiungere l'origine di configurazione dei segreti utente in <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> modo esplicito chiamando nel `Startup` costruttore.</span><span class="sxs-lookup"><span data-stu-id="8b40b-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="8b40b-282">Chiamare `AddUserSecrets` solo quando l'app è in esecuzione nell'ambiente di sviluppo, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="8b40b-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="8b40b-283">I segreti utente possono essere recuperati `Configuration` tramite l'API:</span><span class="sxs-lookup"><span data-stu-id="8b40b-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="8b40b-284">Mappare i segreti a un POCO</span><span class="sxs-lookup"><span data-stu-id="8b40b-284">Map secrets to a POCO</span></span>

<span data-ttu-id="8b40b-285">Il mapping di un intero valore letterale di oggetto a un oggetto POCO (una classe .NET semplice con proprietà) è utile per l'aggregazione di proprietà correlate.</span><span class="sxs-lookup"><span data-stu-id="8b40b-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="8b40b-286">Per eseguire il mapping dei segreti precedenti a un POCO, `Configuration` usare la funzionalità di [associazione dell'oggetto grafico](xref:fundamentals/configuration/index#bind-to-an-object-graph) dell'API.</span><span class="sxs-lookup"><span data-stu-id="8b40b-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="8b40b-287">Il codice seguente è associato a un oggetto `MovieSettings` poco personalizzato e accede al `ServiceApiKey` valore della proprietà:</span><span class="sxs-lookup"><span data-stu-id="8b40b-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="8b40b-288">I `Movies:ConnectionString` segreti `Movies:ServiceApiKey` e vengono mappati alle rispettive proprietà `MovieSettings`in:</span><span class="sxs-lookup"><span data-stu-id="8b40b-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="8b40b-289">Sostituzione di stringhe con segreti</span><span class="sxs-lookup"><span data-stu-id="8b40b-289">String replacement with secrets</span></span>

<span data-ttu-id="8b40b-290">L'archiviazione delle password in testo normale non è protetta.</span><span class="sxs-lookup"><span data-stu-id="8b40b-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="8b40b-291">Ad esempio, una stringa di connessione del database archiviata in *appSettings. JSON* può includere una password per l'utente specificato:</span><span class="sxs-lookup"><span data-stu-id="8b40b-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="8b40b-292">Un approccio più sicuro consiste nell'archiviare la password come segreto.</span><span class="sxs-lookup"><span data-stu-id="8b40b-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="8b40b-293">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8b40b-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="8b40b-294">Rimuovere la `Password` coppia chiave-valore dalla stringa di connessione in *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="8b40b-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="8b40b-295">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8b40b-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="8b40b-296">Il valore del segreto può essere impostato sulla <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> proprietà di un oggetto per completare la stringa di connessione:</span><span class="sxs-lookup"><span data-stu-id="8b40b-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="8b40b-297">Elenca i segreti</span><span class="sxs-lookup"><span data-stu-id="8b40b-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="8b40b-298">Eseguire il comando seguente dalla directory in cui è presente il file con *estensione csproj* :</span><span class="sxs-lookup"><span data-stu-id="8b40b-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="8b40b-299">Viene visualizzato l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="8b40b-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="8b40b-300">Nell'esempio precedente, i due punti nei nomi delle chiavi indicano la gerarchia di oggetti all'interno di *Secrets. JSON*.</span><span class="sxs-lookup"><span data-stu-id="8b40b-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="8b40b-301">Rimuovere un singolo segreto</span><span class="sxs-lookup"><span data-stu-id="8b40b-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="8b40b-302">Eseguire il comando seguente dalla directory in cui è presente il file con *estensione csproj* :</span><span class="sxs-lookup"><span data-stu-id="8b40b-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="8b40b-303">Il file *Secrets. JSON* dell'app è stato modificato per rimuovere la coppia chiave-valore associata `MoviesConnectionString` alla chiave:</span><span class="sxs-lookup"><span data-stu-id="8b40b-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="8b40b-304">In `dotnet user-secrets list` esecuzione viene visualizzato il messaggio seguente:</span><span class="sxs-lookup"><span data-stu-id="8b40b-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="8b40b-305">Rimuovi tutti i segreti</span><span class="sxs-lookup"><span data-stu-id="8b40b-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="8b40b-306">Eseguire il comando seguente dalla directory in cui è presente il file con *estensione csproj* :</span><span class="sxs-lookup"><span data-stu-id="8b40b-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="8b40b-307">Tutti i segreti utente per l'app sono stati eliminati dal file *Secrets. JSON* :</span><span class="sxs-lookup"><span data-stu-id="8b40b-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="8b40b-308">In `dotnet user-secrets list` esecuzione viene visualizzato il messaggio seguente:</span><span class="sxs-lookup"><span data-stu-id="8b40b-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="8b40b-309">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8b40b-309">Additional resources</span></span>

* <span data-ttu-id="8b40b-310">Per informazioni sull'accesso a gestione Secret da IIS, vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="8b40b-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end