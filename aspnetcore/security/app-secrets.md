---
title: Archiviazione sicura dei segreti delle app in fase di sviluppo in ASP.NET Core
author: rick-anderson
description: Informazioni su come archiviare e recuperare informazioni riservate durante lo sviluppo di un'app ASP.NET Core.
ms.author: scaddie
ms.custom: mvc, contperfq2
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
uid: security/app-secrets
ms.openlocfilehash: 99b7b04076206f95c04da79283010beafdd1cc88
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035853"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="c408b-103">Archiviazione sicura dei segreti delle app in fase di sviluppo in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c408b-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c408b-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)e [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="c408b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="c408b-105">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c408b-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c408b-106">Questo documento illustra come gestire i dati sensibili per un'app ASP.NET Core in un computer di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="c408b-106">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="c408b-107">Non archiviare mai le password o altri dati sensibili nel codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="c408b-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="c408b-108">I segreti di produzione non devono essere usati per lo sviluppo o il test.</span><span class="sxs-lookup"><span data-stu-id="c408b-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="c408b-109">I segreti non devono essere distribuiti con l'app.</span><span class="sxs-lookup"><span data-stu-id="c408b-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="c408b-110">Al contrario, i segreti di produzione devono essere accessibili tramite un mezzo controllato, come le variabili di ambiente o Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="c408b-110">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="c408b-111">È possibile memorizzare e proteggere i segreti relativi al test e alla produzione di Azure usando il [provider di configurazione di Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="c408b-111">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="c408b-112">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="c408b-112">Environment variables</span></span>

<span data-ttu-id="c408b-113">Le variabili di ambiente vengono usate per evitare l'archiviazione di segreti dell'app nel codice o nei file di configurazione locali.</span><span class="sxs-lookup"><span data-stu-id="c408b-113">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="c408b-114">Le variabili di ambiente sostituiscono i valori di configurazione per tutte le origini di configurazione precedentemente specificate.</span><span class="sxs-lookup"><span data-stu-id="c408b-114">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="c408b-115">Si consideri un'app Web ASP.NET Core in cui è abilitata la sicurezza dei **singoli account utente** .</span><span class="sxs-lookup"><span data-stu-id="c408b-115">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="c408b-116">Una stringa di connessione al database predefinita viene inclusa nel file del progetto *appsettings.json* con la chiave `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="c408b-116">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="c408b-117">La stringa di connessione predefinita è per il database locale, che viene eseguito in modalità utente e non richiede una password.</span><span class="sxs-lookup"><span data-stu-id="c408b-117">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="c408b-118">Durante la distribuzione dell'app, il `DefaultConnection` valore della chiave può essere sostituito con il valore di una variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="c408b-118">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="c408b-119">La variabile di ambiente può archiviare la stringa di connessione completa con credenziali riservate.</span><span class="sxs-lookup"><span data-stu-id="c408b-119">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="c408b-120">Le variabili di ambiente vengono in genere archiviate in testo normale e non crittografato.</span><span class="sxs-lookup"><span data-stu-id="c408b-120">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="c408b-121">Se il computer o il processo è compromesso, le variabili di ambiente possono essere accessibili da entità non attendibili.</span><span class="sxs-lookup"><span data-stu-id="c408b-121">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="c408b-122">Potrebbero essere necessarie misure aggiuntive per impedire la divulgazione di segreti utente.</span><span class="sxs-lookup"><span data-stu-id="c408b-122">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="c408b-123">Gestione segreta</span><span class="sxs-lookup"><span data-stu-id="c408b-123">Secret Manager</span></span>

<span data-ttu-id="c408b-124">Lo strumento Gestione Secret archivia i dati sensibili durante lo sviluppo di un progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c408b-124">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="c408b-125">In questo contesto, una parte di dati sensibili è un segreto dell'app.</span><span class="sxs-lookup"><span data-stu-id="c408b-125">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="c408b-126">I segreti dell'app vengono archiviati in un percorso separato dall'albero del progetto.</span><span class="sxs-lookup"><span data-stu-id="c408b-126">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="c408b-127">I segreti dell'app sono associati a un progetto specifico o condivisi tra più progetti.</span><span class="sxs-lookup"><span data-stu-id="c408b-127">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="c408b-128">I segreti dell'app non vengono archiviati nel controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="c408b-128">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="c408b-129">Lo strumento Secret Manager non crittografa i segreti archiviati e non deve essere considerato come un archivio attendibile.</span><span class="sxs-lookup"><span data-stu-id="c408b-129">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="c408b-130">È solo a scopo di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="c408b-130">It's for development purposes only.</span></span> <span data-ttu-id="c408b-131">Le chiavi e i valori vengono archiviati in un file di configurazione JSON nella directory del profilo utente.</span><span class="sxs-lookup"><span data-stu-id="c408b-131">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="c408b-132">Funzionamento dello strumento di gestione dei segreti</span><span class="sxs-lookup"><span data-stu-id="c408b-132">How the Secret Manager tool works</span></span>

<span data-ttu-id="c408b-133">Lo strumento Secret Manager nasconde i dettagli di implementazione, ad esempio dove e come vengono archiviati i valori.</span><span class="sxs-lookup"><span data-stu-id="c408b-133">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="c408b-134">È possibile utilizzare lo strumento senza conoscere i dettagli di implementazione.</span><span class="sxs-lookup"><span data-stu-id="c408b-134">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="c408b-135">I valori vengono archiviati in un file JSON nella cartella del profilo utente del computer locale:</span><span class="sxs-lookup"><span data-stu-id="c408b-135">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="c408b-136">Windows</span><span class="sxs-lookup"><span data-stu-id="c408b-136">Windows</span></span>](#tab/windows)

<span data-ttu-id="c408b-137">Percorso del file System:</span><span class="sxs-lookup"><span data-stu-id="c408b-137">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="c408b-138">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="c408b-138">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="c408b-139">Percorso del file System:</span><span class="sxs-lookup"><span data-stu-id="c408b-139">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="c408b-140">Nei percorsi di file precedenti sostituire `<user_secrets_id>` con il `UserSecretsId` valore specificato nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="c408b-140">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="c408b-141">Non scrivere codice che dipende dalla posizione o dal formato dei dati salvati con lo strumento di gestione dei segreti.</span><span class="sxs-lookup"><span data-stu-id="c408b-141">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="c408b-142">Questi dettagli di implementazione possono cambiare.</span><span class="sxs-lookup"><span data-stu-id="c408b-142">These implementation details may change.</span></span> <span data-ttu-id="c408b-143">Ad esempio, i valori dei segreti non sono crittografati, ma potrebbero essere futuri.</span><span class="sxs-lookup"><span data-stu-id="c408b-143">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="c408b-144">Abilita archiviazione segreta</span><span class="sxs-lookup"><span data-stu-id="c408b-144">Enable secret storage</span></span>

<span data-ttu-id="c408b-145">Lo strumento Gestione Secret funziona sulle impostazioni di configurazione specifiche del progetto archiviate nel profilo utente.</span><span class="sxs-lookup"><span data-stu-id="c408b-145">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="c408b-146">Lo strumento di gestione dei segreti include un `init` comando in .NET Core SDK 3.0.100 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="c408b-146">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="c408b-147">Per usare i segreti utente, eseguire il comando seguente nella directory del progetto:</span><span class="sxs-lookup"><span data-stu-id="c408b-147">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="c408b-148">Il comando precedente aggiunge un `UserSecretsId` elemento all'interno `PropertyGroup` di un oggetto del file di progetto.</span><span class="sxs-lookup"><span data-stu-id="c408b-148">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="c408b-149">Per impostazione predefinita, il testo interno di `UserSecretsId` è un GUID.</span><span class="sxs-lookup"><span data-stu-id="c408b-149">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="c408b-150">Il testo interno è arbitrario, ma è univoco per il progetto.</span><span class="sxs-lookup"><span data-stu-id="c408b-150">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="c408b-151">In Visual Studio fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci segreti utente** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="c408b-151">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="c408b-152">Questo movimento aggiunge un `UserSecretsId` elemento, popolato con un GUID, al file di progetto.</span><span class="sxs-lookup"><span data-stu-id="c408b-152">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="c408b-153">Imposta un segreto</span><span class="sxs-lookup"><span data-stu-id="c408b-153">Set a secret</span></span>

<span data-ttu-id="c408b-154">Definire un segreto dell'app costituito da una chiave e dal relativo valore.</span><span class="sxs-lookup"><span data-stu-id="c408b-154">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="c408b-155">Il segreto è associato al valore del progetto `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="c408b-155">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="c408b-156">Ad esempio, eseguire il comando seguente dalla directory in cui è presente il file di progetto:</span><span class="sxs-lookup"><span data-stu-id="c408b-156">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="c408b-157">Nell'esempio precedente, i due punti indicano che `Movies` è un valore letterale di oggetto con una `ServiceApiKey` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="c408b-157">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="c408b-158">Lo strumento Gestione Secret può essere usato anche da altre directory.</span><span class="sxs-lookup"><span data-stu-id="c408b-158">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="c408b-159">Utilizzare l' `--project` opzione per specificare il percorso di file System in cui è presente il file di progetto.</span><span class="sxs-lookup"><span data-stu-id="c408b-159">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="c408b-160">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c408b-160">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="c408b-161">Flat Structure JSON in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c408b-161">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="c408b-162">Il gesto di **gestione dei segreti utente** di Visual Studio apre un *secrets.js* nel file nell'editor di testo.</span><span class="sxs-lookup"><span data-stu-id="c408b-162">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="c408b-163">Sostituire il contenuto di *secrets.json* con le coppie chiave-valore da archiviare.</span><span class="sxs-lookup"><span data-stu-id="c408b-163">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="c408b-164">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c408b-164">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="c408b-165">La struttura JSON è bidimensionale dopo le modifiche tramite `dotnet user-secrets remove` o `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="c408b-165">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="c408b-166">Ad esempio, `dotnet user-secrets remove "Movies:ConnectionString"` l'esecuzione comprime il `Movies` valore letterale dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="c408b-166">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="c408b-167">Il file modificato è simile al codice JSON seguente:</span><span class="sxs-lookup"><span data-stu-id="c408b-167">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="c408b-168">Impostare più segreti</span><span class="sxs-lookup"><span data-stu-id="c408b-168">Set multiple secrets</span></span>

<span data-ttu-id="c408b-169">È possibile impostare un batch di segreti inviando tramite pipe JSON al `set` comando.</span><span class="sxs-lookup"><span data-stu-id="c408b-169">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="c408b-170">Nell'esempio seguente, il *input.jssul* contenuto del file viene inviato tramite pipe al `set` comando.</span><span class="sxs-lookup"><span data-stu-id="c408b-170">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="c408b-171">Windows</span><span class="sxs-lookup"><span data-stu-id="c408b-171">Windows</span></span>](#tab/windows)

<span data-ttu-id="c408b-172">Aprire una shell dei comandi ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="c408b-172">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="c408b-173">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="c408b-173">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="c408b-174">Aprire una shell dei comandi ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="c408b-174">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="c408b-175">Accedere a un segreto</span><span class="sxs-lookup"><span data-stu-id="c408b-175">Access a secret</span></span>

<span data-ttu-id="c408b-176">Per accedere a un segreto, attenersi alla procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="c408b-176">To access a secret, complete the following steps:</span></span>

1. [<span data-ttu-id="c408b-177">Registrare l'origine di configurazione dei segreti utente</span><span class="sxs-lookup"><span data-stu-id="c408b-177">Register the user secrets configuration source</span></span>](#register-the-user-secrets-configuration-source)
1. [<span data-ttu-id="c408b-178">Leggere il segreto tramite l'API di configurazione</span><span class="sxs-lookup"><span data-stu-id="c408b-178">Read the secret via the Configuration API</span></span>](#read-the-secret-via-the-configuration-api)

### <a name="register-the-user-secrets-configuration-source"></a><span data-ttu-id="c408b-179">Registrare l'origine di configurazione dei segreti utente</span><span class="sxs-lookup"><span data-stu-id="c408b-179">Register the user secrets configuration source</span></span>

<span data-ttu-id="c408b-180">Il provider di [configurazione](/dotnet/core/extensions/configuration-providers) dei segreti utente registra l'origine di configurazione appropriata con l' [API](xref:fundamentals/configuration/index)di configurazione .NET.</span><span class="sxs-lookup"><span data-stu-id="c408b-180">The user secrets [configuration provider](/dotnet/core/extensions/configuration-providers) registers the appropriate configuration source with the .NET [Configuration API](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="c408b-181">L'origine di configurazione dei segreti utente viene aggiunta automaticamente in modalità di sviluppo quando viene chiamato il progetto <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="c408b-181">The user secrets configuration source is automatically added in Development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="c408b-182">`CreateDefaultBuilder` chiama <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> è <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="c408b-182">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="c408b-183">Quando `CreateDefaultBuilder` non viene chiamato, aggiungere l'origine di configurazione dei segreti utente in modo esplicito chiamando <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> .</span><span class="sxs-lookup"><span data-stu-id="c408b-183">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="c408b-184">Chiamare `AddUserSecrets` solo quando l'app è in esecuzione nell'ambiente di sviluppo, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="c408b-184">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Program&highlight=10-13)]

### <a name="read-the-secret-via-the-configuration-api"></a><span data-ttu-id="c408b-185">Leggere il segreto tramite l'API di configurazione</span><span class="sxs-lookup"><span data-stu-id="c408b-185">Read the secret via the Configuration API</span></span>

<span data-ttu-id="c408b-186">Se l'origine di configurazione dei segreti utente è registrata, l'API di configurazione .NET può leggere i segreti.</span><span class="sxs-lookup"><span data-stu-id="c408b-186">If the user secrets configuration source is registered, the .NET Configuration API can read the secrets.</span></span> <span data-ttu-id="c408b-187">L' [inserimento del costruttore](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) può essere usato per ottenere l'accesso all'API di configurazione .NET.</span><span class="sxs-lookup"><span data-stu-id="c408b-187">[Constructor injection](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) can be used to gain access to the .NET Configuration API.</span></span> <span data-ttu-id="c408b-188">Si considerino gli esempi seguenti di lettura della `Movies:ServiceApiKey` chiave:</span><span class="sxs-lookup"><span data-stu-id="c408b-188">Consider the following examples of reading the `Movies:ServiceApiKey` key:</span></span>

<span data-ttu-id="c408b-189">**Classe Startup:**</span><span class="sxs-lookup"><span data-stu-id="c408b-189">**Startup class:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

<span data-ttu-id="c408b-190">**Razor Modello pagina pagine:**</span><span class="sxs-lookup"><span data-stu-id="c408b-190">**Razor Pages page model:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Pages/Index.cshtml.cs?name=snippet_PageModel&highlight=12)]

<span data-ttu-id="c408b-191">Per ulteriori informazioni, vedere Configurazione dell' [accesso in](xref:fundamentals/configuration/index#access-configuration-in-startup) configurazione di avvio e [accesso in Razor pagine](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span><span class="sxs-lookup"><span data-stu-id="c408b-191">For more information, see [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup) and [Access configuration in Razor Pages](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span></span>

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="c408b-192">Mappare i segreti a un POCO</span><span class="sxs-lookup"><span data-stu-id="c408b-192">Map secrets to a POCO</span></span>

<span data-ttu-id="c408b-193">Il mapping di un intero valore letterale di oggetto a un oggetto POCO (una classe .NET semplice con proprietà) è utile per l'aggregazione di proprietà correlate.</span><span class="sxs-lookup"><span data-stu-id="c408b-193">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c408b-194">Per eseguire il mapping dei segreti precedenti a un POCO, usare la funzionalità di [associazione dell'oggetto grafico](xref:fundamentals/configuration/index#bind-to-an-object-graph) dell'API di configurazione .NET.</span><span class="sxs-lookup"><span data-stu-id="c408b-194">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="c408b-195">Il codice seguente è associato a un oggetto `MovieSettings` poco personalizzato e accede al `ServiceApiKey` valore della proprietà:</span><span class="sxs-lookup"><span data-stu-id="c408b-195">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="c408b-196">I `Movies:ConnectionString` `Movies:ServiceApiKey` segreti e vengono mappati alle rispettive proprietà in `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="c408b-196">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="c408b-197">Sostituzione di stringhe con segreti</span><span class="sxs-lookup"><span data-stu-id="c408b-197">String replacement with secrets</span></span>

<span data-ttu-id="c408b-198">L'archiviazione delle password in testo normale non è protetta.</span><span class="sxs-lookup"><span data-stu-id="c408b-198">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="c408b-199">Ad esempio, una stringa di connessione del database archiviata in *appsettings.json* può includere una password per l'utente specificato:</span><span class="sxs-lookup"><span data-stu-id="c408b-199">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="c408b-200">Un approccio più sicuro consiste nell'archiviare la password come segreto.</span><span class="sxs-lookup"><span data-stu-id="c408b-200">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="c408b-201">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c408b-201">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="c408b-202">Rimuovere la `Password` coppia chiave-valore dalla stringa di connessione in *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="c408b-202">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="c408b-203">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c408b-203">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="c408b-204">Il valore del segreto può essere impostato sulla proprietà di un <xref:System.Data.SqlClient.SqlConnectionStringBuilder> oggetto <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> per completare la stringa di connessione:</span><span class="sxs-lookup"><span data-stu-id="c408b-204">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="c408b-205">Elenca i segreti</span><span class="sxs-lookup"><span data-stu-id="c408b-205">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c408b-206">Eseguire il comando seguente dalla directory in cui è presente il file di progetto:</span><span class="sxs-lookup"><span data-stu-id="c408b-206">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="c408b-207">Viene visualizzato l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="c408b-207">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="c408b-208">Nell'esempio precedente, i due punti nei nomi delle chiavi indicano la gerarchia di oggetti all'interno *secrets.js*.</span><span class="sxs-lookup"><span data-stu-id="c408b-208">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="c408b-209">Rimuovere un singolo segreto</span><span class="sxs-lookup"><span data-stu-id="c408b-209">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c408b-210">Eseguire il comando seguente dalla directory in cui è presente il file di progetto:</span><span class="sxs-lookup"><span data-stu-id="c408b-210">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="c408b-211">Ilsecrets.jsdell'app *nel* file è stato modificato per rimuovere la coppia chiave-valore associata alla `MoviesConnectionString` chiave:</span><span class="sxs-lookup"><span data-stu-id="c408b-211">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="c408b-212">`dotnet user-secrets list` Visualizza il messaggio seguente:</span><span class="sxs-lookup"><span data-stu-id="c408b-212">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="c408b-213">Rimuovi tutti i segreti</span><span class="sxs-lookup"><span data-stu-id="c408b-213">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c408b-214">Eseguire il comando seguente dalla directory in cui è presente il file di progetto:</span><span class="sxs-lookup"><span data-stu-id="c408b-214">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="c408b-215">Tutti i segreti utente per l'app sono stati eliminati dal *secrets.jsnel* file:</span><span class="sxs-lookup"><span data-stu-id="c408b-215">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="c408b-216">In esecuzione `dotnet user-secrets list` viene visualizzato il messaggio seguente:</span><span class="sxs-lookup"><span data-stu-id="c408b-216">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="c408b-217">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="c408b-217">Additional resources</span></span>

* <span data-ttu-id="c408b-218">Per informazioni sull'accesso ai segreti utente da IIS, vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="c408b-218">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c408b-219">Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)e [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="c408b-219">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="c408b-220">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c408b-220">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c408b-221">Questo documento illustra come gestire i dati sensibili per un'app ASP.NET Core in un computer di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="c408b-221">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="c408b-222">Non archiviare mai le password o altri dati sensibili nel codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="c408b-222">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="c408b-223">I segreti di produzione non devono essere usati per lo sviluppo o il test.</span><span class="sxs-lookup"><span data-stu-id="c408b-223">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="c408b-224">I segreti non devono essere distribuiti con l'app.</span><span class="sxs-lookup"><span data-stu-id="c408b-224">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="c408b-225">Al contrario, i segreti di produzione devono essere accessibili tramite un mezzo controllato, come le variabili di ambiente o Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="c408b-225">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="c408b-226">È possibile memorizzare e proteggere i segreti relativi al test e alla produzione di Azure usando il [provider di configurazione di Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="c408b-226">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="c408b-227">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="c408b-227">Environment variables</span></span>

<span data-ttu-id="c408b-228">Le variabili di ambiente vengono usate per evitare l'archiviazione di segreti dell'app nel codice o nei file di configurazione locali.</span><span class="sxs-lookup"><span data-stu-id="c408b-228">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="c408b-229">Le variabili di ambiente sostituiscono i valori di configurazione per tutte le origini di configurazione precedentemente specificate.</span><span class="sxs-lookup"><span data-stu-id="c408b-229">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="c408b-230">Si consideri un'app Web ASP.NET Core in cui è abilitata la sicurezza dei **singoli account utente** .</span><span class="sxs-lookup"><span data-stu-id="c408b-230">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="c408b-231">Una stringa di connessione al database predefinita viene inclusa nel file del progetto *appsettings.json* con la chiave `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="c408b-231">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="c408b-232">La stringa di connessione predefinita è per il database locale, che viene eseguito in modalità utente e non richiede una password.</span><span class="sxs-lookup"><span data-stu-id="c408b-232">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="c408b-233">Durante la distribuzione dell'app, il `DefaultConnection` valore della chiave può essere sostituito con il valore di una variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="c408b-233">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="c408b-234">La variabile di ambiente può archiviare la stringa di connessione completa con credenziali riservate.</span><span class="sxs-lookup"><span data-stu-id="c408b-234">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="c408b-235">Le variabili di ambiente vengono in genere archiviate in testo normale e non crittografato.</span><span class="sxs-lookup"><span data-stu-id="c408b-235">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="c408b-236">Se il computer o il processo è compromesso, le variabili di ambiente possono essere accessibili da entità non attendibili.</span><span class="sxs-lookup"><span data-stu-id="c408b-236">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="c408b-237">Potrebbero essere necessarie misure aggiuntive per impedire la divulgazione di segreti utente.</span><span class="sxs-lookup"><span data-stu-id="c408b-237">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="c408b-238">Gestione segreta</span><span class="sxs-lookup"><span data-stu-id="c408b-238">Secret Manager</span></span>

<span data-ttu-id="c408b-239">Lo strumento Gestione Secret archivia i dati sensibili durante lo sviluppo di un progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c408b-239">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="c408b-240">In questo contesto, una parte di dati sensibili è un segreto dell'app.</span><span class="sxs-lookup"><span data-stu-id="c408b-240">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="c408b-241">I segreti dell'app vengono archiviati in un percorso separato dall'albero del progetto.</span><span class="sxs-lookup"><span data-stu-id="c408b-241">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="c408b-242">I segreti dell'app sono associati a un progetto specifico o condivisi tra più progetti.</span><span class="sxs-lookup"><span data-stu-id="c408b-242">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="c408b-243">I segreti dell'app non vengono archiviati nel controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="c408b-243">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="c408b-244">Lo strumento Secret Manager non crittografa i segreti archiviati e non deve essere considerato come un archivio attendibile.</span><span class="sxs-lookup"><span data-stu-id="c408b-244">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="c408b-245">È solo a scopo di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="c408b-245">It's for development purposes only.</span></span> <span data-ttu-id="c408b-246">Le chiavi e i valori vengono archiviati in un file di configurazione JSON nella directory del profilo utente.</span><span class="sxs-lookup"><span data-stu-id="c408b-246">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="c408b-247">Funzionamento dello strumento di gestione dei segreti</span><span class="sxs-lookup"><span data-stu-id="c408b-247">How the Secret Manager tool works</span></span>

<span data-ttu-id="c408b-248">Lo strumento Secret Manager nasconde i dettagli di implementazione, ad esempio dove e come vengono archiviati i valori.</span><span class="sxs-lookup"><span data-stu-id="c408b-248">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="c408b-249">È possibile utilizzare lo strumento senza conoscere i dettagli di implementazione.</span><span class="sxs-lookup"><span data-stu-id="c408b-249">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="c408b-250">I valori vengono archiviati in un file JSON nella cartella del profilo utente del computer locale:</span><span class="sxs-lookup"><span data-stu-id="c408b-250">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="c408b-251">Windows</span><span class="sxs-lookup"><span data-stu-id="c408b-251">Windows</span></span>](#tab/windows)

<span data-ttu-id="c408b-252">Percorso del file System:</span><span class="sxs-lookup"><span data-stu-id="c408b-252">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="c408b-253">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="c408b-253">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="c408b-254">Percorso del file System:</span><span class="sxs-lookup"><span data-stu-id="c408b-254">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="c408b-255">Nei percorsi di file precedenti sostituire `<user_secrets_id>` con il `UserSecretsId` valore specificato nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="c408b-255">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="c408b-256">Non scrivere codice che dipende dalla posizione o dal formato dei dati salvati con lo strumento di gestione dei segreti.</span><span class="sxs-lookup"><span data-stu-id="c408b-256">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="c408b-257">Questi dettagli di implementazione possono cambiare.</span><span class="sxs-lookup"><span data-stu-id="c408b-257">These implementation details may change.</span></span> <span data-ttu-id="c408b-258">Ad esempio, i valori dei segreti non sono crittografati, ma potrebbero essere futuri.</span><span class="sxs-lookup"><span data-stu-id="c408b-258">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="c408b-259">Abilita archiviazione segreta</span><span class="sxs-lookup"><span data-stu-id="c408b-259">Enable secret storage</span></span>

<span data-ttu-id="c408b-260">Lo strumento Gestione Secret funziona sulle impostazioni di configurazione specifiche del progetto archiviate nel profilo utente.</span><span class="sxs-lookup"><span data-stu-id="c408b-260">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="c408b-261">Per usare i segreti utente, definire un `UserSecretsId` elemento in un `PropertyGroup` del file di progetto.</span><span class="sxs-lookup"><span data-stu-id="c408b-261">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="c408b-262">Il testo interno di `UserSecretsId` è arbitrario, ma è univoco per il progetto.</span><span class="sxs-lookup"><span data-stu-id="c408b-262">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="c408b-263">Gli sviluppatori generano in genere un GUID per il `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="c408b-263">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="c408b-264">In Visual Studio fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci segreti utente** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="c408b-264">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="c408b-265">Questo movimento aggiunge un `UserSecretsId` elemento, popolato con un GUID, al file di progetto.</span><span class="sxs-lookup"><span data-stu-id="c408b-265">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="c408b-266">Imposta un segreto</span><span class="sxs-lookup"><span data-stu-id="c408b-266">Set a secret</span></span>

<span data-ttu-id="c408b-267">Definire un segreto dell'app costituito da una chiave e dal relativo valore.</span><span class="sxs-lookup"><span data-stu-id="c408b-267">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="c408b-268">Il segreto è associato al valore del progetto `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="c408b-268">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="c408b-269">Ad esempio, eseguire il comando seguente dalla directory in cui è presente il file di progetto:</span><span class="sxs-lookup"><span data-stu-id="c408b-269">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="c408b-270">Nell'esempio precedente, i due punti indicano che `Movies` è un valore letterale di oggetto con una `ServiceApiKey` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="c408b-270">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="c408b-271">Lo strumento Gestione Secret può essere usato anche da altre directory.</span><span class="sxs-lookup"><span data-stu-id="c408b-271">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="c408b-272">Utilizzare l' `--project` opzione per specificare il percorso di file System in cui è presente il file di progetto.</span><span class="sxs-lookup"><span data-stu-id="c408b-272">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="c408b-273">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c408b-273">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="c408b-274">Flat Structure JSON in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c408b-274">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="c408b-275">Il gesto di **gestione dei segreti utente** di Visual Studio apre un *secrets.js* nel file nell'editor di testo.</span><span class="sxs-lookup"><span data-stu-id="c408b-275">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="c408b-276">Sostituire il contenuto di *secrets.json* con le coppie chiave-valore da archiviare.</span><span class="sxs-lookup"><span data-stu-id="c408b-276">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="c408b-277">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c408b-277">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="c408b-278">La struttura JSON è bidimensionale dopo le modifiche tramite `dotnet user-secrets remove` o `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="c408b-278">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="c408b-279">Ad esempio, `dotnet user-secrets remove "Movies:ConnectionString"` l'esecuzione comprime il `Movies` valore letterale dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="c408b-279">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="c408b-280">Il file modificato è simile al codice JSON seguente:</span><span class="sxs-lookup"><span data-stu-id="c408b-280">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="c408b-281">Impostare più segreti</span><span class="sxs-lookup"><span data-stu-id="c408b-281">Set multiple secrets</span></span>

<span data-ttu-id="c408b-282">È possibile impostare un batch di segreti inviando tramite pipe JSON al `set` comando.</span><span class="sxs-lookup"><span data-stu-id="c408b-282">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="c408b-283">Nell'esempio seguente, il *input.jssul* contenuto del file viene inviato tramite pipe al `set` comando.</span><span class="sxs-lookup"><span data-stu-id="c408b-283">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="c408b-284">Windows</span><span class="sxs-lookup"><span data-stu-id="c408b-284">Windows</span></span>](#tab/windows)

<span data-ttu-id="c408b-285">Aprire una shell dei comandi ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="c408b-285">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="c408b-286">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="c408b-286">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="c408b-287">Aprire una shell dei comandi ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="c408b-287">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="c408b-288">Accedere a un segreto</span><span class="sxs-lookup"><span data-stu-id="c408b-288">Access a secret</span></span>

<span data-ttu-id="c408b-289">L' [API di configurazione](xref:fundamentals/configuration/index) consente di accedere ai segreti dell'utente.</span><span class="sxs-lookup"><span data-stu-id="c408b-289">The [Configuration API](xref:fundamentals/configuration/index) provides access to user secrets.</span></span>

<span data-ttu-id="c408b-290">Se il progetto è destinato .NET Framework, installare il [Microsoft.Extensions.Configuration. ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) Pacchetto NuGet UserSecrets.</span><span class="sxs-lookup"><span data-stu-id="c408b-290">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="c408b-291">In ASP.NET Core 2,0 o versioni successive, l'origine di configurazione dei segreti utente viene aggiunta automaticamente in modalità di sviluppo quando il progetto chiama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="c408b-291">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="c408b-292">`CreateDefaultBuilder` chiama <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> è <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="c408b-292">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="c408b-293">Quando `CreateDefaultBuilder` non viene chiamato, aggiungere l'origine di configurazione dei segreti utente in modo esplicito chiamando <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> nel `Startup` costruttore.</span><span class="sxs-lookup"><span data-stu-id="c408b-293">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="c408b-294">Chiamare `AddUserSecrets` solo quando l'app è in esecuzione nell'ambiente di sviluppo, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="c408b-294">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="c408b-295">I segreti utente possono essere recuperati tramite l'API di configurazione .NET:</span><span class="sxs-lookup"><span data-stu-id="c408b-295">User secrets can be retrieved via the .NET Configuration API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="c408b-296">Mappare i segreti a un POCO</span><span class="sxs-lookup"><span data-stu-id="c408b-296">Map secrets to a POCO</span></span>

<span data-ttu-id="c408b-297">Il mapping di un intero valore letterale di oggetto a un oggetto POCO (una classe .NET semplice con proprietà) è utile per l'aggregazione di proprietà correlate.</span><span class="sxs-lookup"><span data-stu-id="c408b-297">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c408b-298">Per eseguire il mapping dei segreti precedenti a un POCO, usare la funzionalità di [associazione dell'oggetto grafico](xref:fundamentals/configuration/index#bind-to-an-object-graph) dell'API di configurazione .NET.</span><span class="sxs-lookup"><span data-stu-id="c408b-298">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="c408b-299">Il codice seguente è associato a un oggetto `MovieSettings` poco personalizzato e accede al `ServiceApiKey` valore della proprietà:</span><span class="sxs-lookup"><span data-stu-id="c408b-299">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="c408b-300">I `Movies:ConnectionString` `Movies:ServiceApiKey` segreti e vengono mappati alle rispettive proprietà in `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="c408b-300">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="c408b-301">Sostituzione di stringhe con segreti</span><span class="sxs-lookup"><span data-stu-id="c408b-301">String replacement with secrets</span></span>

<span data-ttu-id="c408b-302">L'archiviazione delle password in testo normale non è protetta.</span><span class="sxs-lookup"><span data-stu-id="c408b-302">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="c408b-303">Ad esempio, una stringa di connessione del database archiviata in *appsettings.json* può includere una password per l'utente specificato:</span><span class="sxs-lookup"><span data-stu-id="c408b-303">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="c408b-304">Un approccio più sicuro consiste nell'archiviare la password come segreto.</span><span class="sxs-lookup"><span data-stu-id="c408b-304">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="c408b-305">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c408b-305">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="c408b-306">Rimuovere la `Password` coppia chiave-valore dalla stringa di connessione in *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="c408b-306">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="c408b-307">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c408b-307">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="c408b-308">Il valore del segreto può essere impostato sulla proprietà di un <xref:System.Data.SqlClient.SqlConnectionStringBuilder> oggetto <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> per completare la stringa di connessione:</span><span class="sxs-lookup"><span data-stu-id="c408b-308">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="c408b-309">Elenca i segreti</span><span class="sxs-lookup"><span data-stu-id="c408b-309">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c408b-310">Eseguire il comando seguente dalla directory in cui è presente il file di progetto:</span><span class="sxs-lookup"><span data-stu-id="c408b-310">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="c408b-311">Viene visualizzato l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="c408b-311">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="c408b-312">Nell'esempio precedente, i due punti nei nomi delle chiavi indicano la gerarchia di oggetti all'interno *secrets.js*.</span><span class="sxs-lookup"><span data-stu-id="c408b-312">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="c408b-313">Rimuovere un singolo segreto</span><span class="sxs-lookup"><span data-stu-id="c408b-313">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c408b-314">Eseguire il comando seguente dalla directory in cui è presente il file di progetto:</span><span class="sxs-lookup"><span data-stu-id="c408b-314">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="c408b-315">Ilsecrets.jsdell'app *nel* file è stato modificato per rimuovere la coppia chiave-valore associata alla `MoviesConnectionString` chiave:</span><span class="sxs-lookup"><span data-stu-id="c408b-315">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="c408b-316">In esecuzione `dotnet user-secrets list` viene visualizzato il messaggio seguente:</span><span class="sxs-lookup"><span data-stu-id="c408b-316">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="c408b-317">Rimuovi tutti i segreti</span><span class="sxs-lookup"><span data-stu-id="c408b-317">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c408b-318">Eseguire il comando seguente dalla directory in cui è presente il file di progetto:</span><span class="sxs-lookup"><span data-stu-id="c408b-318">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="c408b-319">Tutti i segreti utente per l'app sono stati eliminati dal *secrets.jsnel* file:</span><span class="sxs-lookup"><span data-stu-id="c408b-319">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="c408b-320">In esecuzione `dotnet user-secrets list` viene visualizzato il messaggio seguente:</span><span class="sxs-lookup"><span data-stu-id="c408b-320">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="c408b-321">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="c408b-321">Additional resources</span></span>

* <span data-ttu-id="c408b-322">Per informazioni sull'accesso ai segreti utente da IIS, vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="c408b-322">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end