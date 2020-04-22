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
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Archiviazione sicura dei segreti delle app in fase di sviluppo in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Di [Rick Anderson](https://twitter.com/RickAndMSFT), Kirk [Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), e [Scott Addie](https://github.com/scottaddie)

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ( come[scaricare](xref:index#how-to-download-a-sample))

Questo documento illustra le tecniche per l'archiviazione e il recupero di dati sensibili durante lo sviluppo di un'app ASP.NET Core in un computer di sviluppo. Non archiviare mai password o altri dati sensibili nel codice sorgente. I segreti di produzione non devono essere utilizzati per lo sviluppo o il test. I segreti non devono essere distribuiti con l'app. Al contrario, i segreti devono essere resi disponibili nell'ambiente di produzione tramite un mezzo controllato come le variabili di ambiente, l'insieme di credenziali delle chiavi di Azure e così via. È possibile archiviare e proteggere i segreti di test e produzione di Azure con il provider di [configurazione dell'insieme](xref:security/key-vault-configuration)di credenziali delle chiavi di Azure.You can store and protect Azure test and production secrets with the Azure Key Vault configuration provider .

## <a name="environment-variables"></a>Variabili di ambiente

Le variabili di ambiente vengono usate per evitare l'archiviazione di segreti dell'app nel codice o nei file di configurazione locali. Le variabili di ambiente eseguono l'override dei valori di configurazione per tutte le origini di configurazione specificate in precedenza.

Si consideri un'app Web ASP.NET Core in cui è abilitata la sicurezza **degli account utente singoli.** Una stringa di connessione al database predefinita è inclusa nel `DefaultConnection`file *appsettings.json* del progetto con la chiave . La stringa di connessione predefinita è per LocalDB, che viene eseguito in modalità utente e non richiede una password. Durante la distribuzione `DefaultConnection` dell'app, il valore della chiave può essere sostituito con il valore di una variabile di ambiente. La variabile di ambiente può archiviare la stringa di connessione completa con credenziali riservate.

> [!WARNING]
> Le variabili di ambiente vengono in genere archiviate in testo normale e non crittografato. Se il computer o il processo è compromesso, le variabili di ambiente sono accessibili da parti non attendibili. Potrebbero essere necessarie misure aggiuntive per impedire la divulgazione dei segreti utente.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Responsabile Segreto

Lo strumento Secret Manager memorizza i dati sensibili durante lo sviluppo di un progetto ASP.NET Core. In questo contesto, una parte di dati sensibili è un segreto dell'app. I segreti dell'app vengono archiviati in un percorso separato dall'albero del progetto. I segreti dell'app sono associati a un progetto specifico o condivisi tra più progetti. I segreti dell'app non vengono archiviati nel controllo del codice sorgente.

> [!WARNING]
> Lo strumento Secret Manager non crittografa i segreti archiviati e non deve essere considerato come un archivio attendibile. E 'solo per scopi di sviluppo. Le chiavi e i valori vengono archiviati in un file di configurazione JSON nella directory del profilo utente.

## <a name="how-the-secret-manager-tool-works"></a>Funzionamento dello strumento Secret Manager

Lo strumento Secret Manager astrae i dettagli di implementazione, ad esempio dove e come vengono archiviati i valori. È possibile utilizzare lo strumento senza conoscere questi dettagli di implementazione. I valori vengono archiviati in un file di configurazione JSON in una cartella del profilo utente protetta dal sistema nel computer locale:The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:

# <a name="windows"></a>[Windows](#tab/windows)

Percorso del file system:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Percorso del file system:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Nei percorsi dei file `<user_secrets_id>` precedenti `UserSecretsId` sostituire con il valore specificato nel file *con estensione csproj.*

Non scrivere codice che dipende dalla posizione o dal formato dei dati salvati con lo strumento Secret Manager. Questi dettagli di implementazione possono cambiare. Ad esempio, i valori segreti non sono crittografati, ma potrebbero essere in futuro.

## <a name="enable-secret-storage"></a>Abilitare l'archiviazione segretaEnable secret storage

Lo strumento Secret Manager si baserà sulle impostazioni di configurazione specifiche del progetto archiviate nel profilo utente.

Lo strumento Secret `init` Manager include un comando in .NET Core SDK 3.0.100 o versione successiva. Per utilizzare i segreti utente, eseguire il comando seguente nella directory del progetto:

```dotnetcli
dotnet user-secrets init
```

Il comando precedente `UserSecretsId` aggiunge un `PropertyGroup` elemento all'interno di un file *con estensione csproj.* Per impostazione predefinita, `UserSecretsId` il testo interno di è un GUID. Il testo interno è arbitrario, ma è univoco per il progetto.

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

In Visual Studio fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci segreti utente** dal menu di scelta rapida. Questo gesto `UserSecretsId` aggiunge un elemento, popolato con un GUID, al file *con estensione csproj.*

## <a name="set-a-secret"></a>Impostare un segreto

Definire un segreto dell'app costituito da una chiave e dal relativo valore. Il segreto è associato `UserSecretsId` al valore del progetto. Ad esempio, eseguire il comando seguente dalla directory in cui è presente il file *con estensione csproj:*

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Nell'esempio precedente, i due `Movies` punti indicano che `ServiceApiKey` è un valore letterale oggetto con una proprietà.

Lo strumento Secret Manager può essere utilizzato anche da altre directory. Utilizzare `--project` l'opzione per fornire il percorso del file system in cui è presente il file *con estensione csproj.* Ad esempio:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Appiattimento della struttura JSON in Visual Studio

Il movimento **Gestisci segreti utente** di Visual Studio apre un file *secrets.json* nell'editor di testo. Sostituire il contenuto di *secrets.json* con le coppie chiave-valore da archiviare. Ad esempio:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

La struttura JSON viene appiattita dopo le modifiche tramite `dotnet user-secrets remove` o `dotnet user-secrets set`. Ad esempio, `dotnet user-secrets remove "Movies:ConnectionString"` l'esecuzione comprime il valore letterale dell'oggetto. `Movies` Il file modificato è simile al seguente:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Impostare più segreti

È possibile impostare un batch di `set` segreti eseguendo il piping JSON al comando. Nell'esempio seguente, il contenuto del file *input.json* viene reindirizzato al `set` comando.

# <a name="windows"></a>[Windows](#tab/windows)

Aprire una shell dei comandi ed eseguire il comando seguente:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Aprire una shell dei comandi ed eseguire il comando seguente:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Accedi a un segreto

[L'API di configurazione di ASP.NET core](xref:fundamentals/configuration/index) fornisce l'accesso ai segreti di Secret Manager.The You You Code Base Configuration API provides access to Secret Manager secrets.

L'origine di configurazione dei segreti utente viene <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> aggiunta automaticamente in modalità di sviluppo quando il progetto chiama per inizializzare una nuova istanza dell'host con i valori predefiniti preconfigurati. `CreateDefaultBuilder`chiamate <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>il è :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

Quando `CreateDefaultBuilder` non viene chiamato, aggiungere l'origine <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>di configurazione dei segreti utente in modo esplicito chiamando . Chiama `AddUserSecrets` solo quando l'app viene eseguita nell'ambiente di sviluppo, come illustrato nell'esempio seguente:Call only when the app runs in the Development environment, as shown in the following example:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

I segreti utente possono `Configuration` essere recuperati tramite l'API:User secrets can be retrieved via the API:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Mappare i segreti a un POCO

Il mapping di un intero valore letterale oggetto a un POCO (una semplice classe .NET con proprietà) è utile per aggregare le proprietà correlate.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Per eseguire il mapping dei segreti `Configuration` precedenti a un POCO, usare la funzionalità di [associazione dell'oggetto grafico](xref:fundamentals/configuration/index#bind-to-an-object-graph) dell'API. Il codice seguente esegue `MovieSettings` l'associazione a `ServiceApiKey` un POCO personalizzato e accede al valore della proprietà:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

I `Movies:ConnectionString` `Movies:ServiceApiKey` segreti e sono mappati `MovieSettings`alle rispettive proprietà in :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Sostituzione di stringhe con segreti

L'archiviazione delle password in testo normale non è sicura. Ad esempio, una stringa di connessione al database archiviata in appsettings.json può includere una password per l'utente specificato:For example, a database connection string stored in *appsettings.json* may include a password for the specified user:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Un approccio più sicuro consiste nell'archiviare la password come segreto. Ad esempio:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Rimuovere `Password` la coppia chiave-valore dalla stringa di connessione in *appsettings.json*. Ad esempio:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

Il valore del segreto può <xref:System.Data.SqlClient.SqlConnectionStringBuilder> essere <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> impostato sulla proprietà di un oggetto per completare la stringa di connessione:The secret's value can be set on a object's property to complete the connection string:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Elencare i segreti

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Eseguire il comando seguente dalla directory in cui è presente il file *con estensione csproj:*

```dotnetcli
dotnet user-secrets list
```

Viene visualizzato l'output seguente:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Nell'esempio precedente, i due punti nei nomi delle chiavi indicano la gerarchia di oggetti all'interno di *secrets.json*.

## <a name="remove-a-single-secret"></a>Rimuovere un singolo segreto

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Eseguire il comando seguente dalla directory in cui è presente il file *con estensione csproj:*

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Il file *secrets.json* dell'app è stato modificato per `MoviesConnectionString` rimuovere la coppia chiave-valore associata alla chiave:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`viene visualizzato il seguente messaggio:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Rimuovere tutti i segreti

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Eseguire il comando seguente dalla directory in cui è presente il file *con estensione csproj:*

```dotnetcli
dotnet user-secrets clear
```

Tutti i segreti utente per l'app sono stati eliminati dal file *secrets.json:*

```json
{}
```

In `dotnet user-secrets list` esecuzione viene visualizzato il seguente messaggio:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Risorse aggiuntive

* Vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) per informazioni sull'accesso a Secret Manager da IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Di [Rick Anderson](https://twitter.com/RickAndMSFT), Daniel [Roth](https://github.com/danroth27), e [Scott Addie](https://github.com/scottaddie)

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ( come[scaricare](xref:index#how-to-download-a-sample))

Questo documento illustra le tecniche per l'archiviazione e il recupero di dati sensibili durante lo sviluppo di un'app ASP.NET Core in un computer di sviluppo. Non archiviare mai password o altri dati sensibili nel codice sorgente. I segreti di produzione non devono essere utilizzati per lo sviluppo o il test. I segreti non devono essere distribuiti con l'app. Al contrario, i segreti devono essere resi disponibili nell'ambiente di produzione tramite un mezzo controllato come le variabili di ambiente, l'insieme di credenziali delle chiavi di Azure e così via. È possibile archiviare e proteggere i segreti di test e produzione di Azure con il provider di [configurazione dell'insieme](xref:security/key-vault-configuration)di credenziali delle chiavi di Azure.You can store and protect Azure test and production secrets with the Azure Key Vault configuration provider .

## <a name="environment-variables"></a>Variabili di ambiente

Le variabili di ambiente vengono usate per evitare l'archiviazione di segreti dell'app nel codice o nei file di configurazione locali. Le variabili di ambiente eseguono l'override dei valori di configurazione per tutte le origini di configurazione specificate in precedenza.

Si consideri un'app Web ASP.NET Core in cui è abilitata la sicurezza **degli account utente singoli.** Una stringa di connessione al database predefinita è inclusa nel `DefaultConnection`file *appsettings.json* del progetto con la chiave . La stringa di connessione predefinita è per LocalDB, che viene eseguito in modalità utente e non richiede una password. Durante la distribuzione `DefaultConnection` dell'app, il valore della chiave può essere sostituito con il valore di una variabile di ambiente. La variabile di ambiente può archiviare la stringa di connessione completa con credenziali riservate.

> [!WARNING]
> Le variabili di ambiente vengono in genere archiviate in testo normale e non crittografato. Se il computer o il processo è compromesso, le variabili di ambiente sono accessibili da parti non attendibili. Potrebbero essere necessarie misure aggiuntive per impedire la divulgazione dei segreti utente.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Responsabile Segreto

Lo strumento Secret Manager memorizza i dati sensibili durante lo sviluppo di un progetto ASP.NET Core. In questo contesto, una parte di dati sensibili è un segreto dell'app. I segreti dell'app vengono archiviati in un percorso separato dall'albero del progetto. I segreti dell'app sono associati a un progetto specifico o condivisi tra più progetti. I segreti dell'app non vengono archiviati nel controllo del codice sorgente.

> [!WARNING]
> Lo strumento Secret Manager non crittografa i segreti archiviati e non deve essere considerato come un archivio attendibile. E 'solo per scopi di sviluppo. Le chiavi e i valori vengono archiviati in un file di configurazione JSON nella directory del profilo utente.

## <a name="how-the-secret-manager-tool-works"></a>Funzionamento dello strumento Secret Manager

Lo strumento Secret Manager astrae i dettagli di implementazione, ad esempio dove e come vengono archiviati i valori. È possibile utilizzare lo strumento senza conoscere questi dettagli di implementazione. I valori vengono archiviati in un file di configurazione JSON in una cartella del profilo utente protetta dal sistema nel computer locale:The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:

# <a name="windows"></a>[Windows](#tab/windows)

Percorso del file system:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Percorso del file system:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Nei percorsi dei file `<user_secrets_id>` precedenti `UserSecretsId` sostituire con il valore specificato nel file *con estensione csproj.*

Non scrivere codice che dipende dalla posizione o dal formato dei dati salvati con lo strumento Secret Manager. Questi dettagli di implementazione possono cambiare. Ad esempio, i valori segreti non sono crittografati, ma potrebbero essere in futuro.

## <a name="enable-secret-storage"></a>Abilitare l'archiviazione segretaEnable secret storage

Lo strumento Secret Manager si baserà sulle impostazioni di configurazione specifiche del progetto archiviate nel profilo utente.

Per utilizzare i segreti `UserSecretsId` utente, `PropertyGroup` definire un elemento all'interno di un file *con estensione csproj.* Il testo `UserSecretsId` interno di è arbitrario, ma è univoco per il progetto. Gli sviluppatori generano `UserSecretsId`in genere un GUID per il file .

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> In Visual Studio fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci segreti utente** dal menu di scelta rapida. Questo gesto `UserSecretsId` aggiunge un elemento, popolato con un GUID, al file *con estensione csproj.*

## <a name="set-a-secret"></a>Impostare un segreto

Definire un segreto dell'app costituito da una chiave e dal relativo valore. Il segreto è associato `UserSecretsId` al valore del progetto. Ad esempio, eseguire il comando seguente dalla directory in cui è presente il file *con estensione csproj:*

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Nell'esempio precedente, i due `Movies` punti indicano che `ServiceApiKey` è un valore letterale oggetto con una proprietà.

Lo strumento Secret Manager può essere utilizzato anche da altre directory. Utilizzare `--project` l'opzione per fornire il percorso del file system in cui è presente il file *con estensione csproj.* Ad esempio:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Appiattimento della struttura JSON in Visual Studio

Il movimento **Gestisci segreti utente** di Visual Studio apre un file *secrets.json* nell'editor di testo. Sostituire il contenuto di *secrets.json* con le coppie chiave-valore da archiviare. Ad esempio:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

La struttura JSON viene appiattita dopo le modifiche tramite `dotnet user-secrets remove` o `dotnet user-secrets set`. Ad esempio, `dotnet user-secrets remove "Movies:ConnectionString"` l'esecuzione comprime il valore letterale dell'oggetto. `Movies` Il file modificato è simile al seguente:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Impostare più segreti

È possibile impostare un batch di `set` segreti eseguendo il piping JSON al comando. Nell'esempio seguente, il contenuto del file *input.json* viene reindirizzato al `set` comando.

# <a name="windows"></a>[Windows](#tab/windows)

Aprire una shell dei comandi ed eseguire il comando seguente:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Aprire una shell dei comandi ed eseguire il comando seguente:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Accedi a un segreto

[L'API di configurazione di ASP.NET core](xref:fundamentals/configuration/index) fornisce l'accesso ai segreti di Secret Manager.The You You Code Base Configuration API provides access to Secret Manager secrets.

Se il progetto è destinato a .NET Framework, installare il pacchetto [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet.

In ASP.NET Core 2.0 o versioni successive, l'origine di configurazione <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> dei segreti utente viene aggiunta automaticamente in modalità di sviluppo quando il progetto chiama per inizializzare una nuova istanza dell'host con i valori predefiniti preconfigurati. `CreateDefaultBuilder`chiamate <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>il è :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

Quando `CreateDefaultBuilder` non viene chiamato, aggiungere l'origine <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> di `Startup` configurazione dei segreti utente in modo esplicito chiamando il costruttore. Chiama `AddUserSecrets` solo quando l'app viene eseguita nell'ambiente di sviluppo, come illustrato nell'esempio seguente:Call only when the app runs in the Development environment, as shown in the following example:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

I segreti utente possono `Configuration` essere recuperati tramite l'API:User secrets can be retrieved via the API:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Mappare i segreti a un POCO

Il mapping di un intero valore letterale oggetto a un POCO (una semplice classe .NET con proprietà) è utile per aggregare le proprietà correlate.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Per eseguire il mapping dei segreti `Configuration` precedenti a un POCO, usare la funzionalità di [associazione dell'oggetto grafico](xref:fundamentals/configuration/index#bind-to-an-object-graph) dell'API. Il codice seguente esegue `MovieSettings` l'associazione a `ServiceApiKey` un POCO personalizzato e accede al valore della proprietà:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

I `Movies:ConnectionString` `Movies:ServiceApiKey` segreti e sono mappati `MovieSettings`alle rispettive proprietà in :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Sostituzione di stringhe con segreti

L'archiviazione delle password in testo normale non è sicura. Ad esempio, una stringa di connessione al database archiviata in appsettings.json può includere una password per l'utente specificato:For example, a database connection string stored in *appsettings.json* may include a password for the specified user:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Un approccio più sicuro consiste nell'archiviare la password come segreto. Ad esempio:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Rimuovere `Password` la coppia chiave-valore dalla stringa di connessione in *appsettings.json*. Ad esempio:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Il valore del segreto può <xref:System.Data.SqlClient.SqlConnectionStringBuilder> essere <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> impostato sulla proprietà di un oggetto per completare la stringa di connessione:The secret's value can be set on a object's property to complete the connection string:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Elencare i segreti

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Eseguire il comando seguente dalla directory in cui è presente il file *con estensione csproj:*

```dotnetcli
dotnet user-secrets list
```

Viene visualizzato l'output seguente:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Nell'esempio precedente, i due punti nei nomi delle chiavi indicano la gerarchia di oggetti all'interno di *secrets.json*.

## <a name="remove-a-single-secret"></a>Rimuovere un singolo segreto

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Eseguire il comando seguente dalla directory in cui è presente il file *con estensione csproj:*

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Il file *secrets.json* dell'app è stato modificato per `MoviesConnectionString` rimuovere la coppia chiave-valore associata alla chiave:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

In `dotnet user-secrets list` esecuzione viene visualizzato il seguente messaggio:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Rimuovere tutti i segreti

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Eseguire il comando seguente dalla directory in cui è presente il file *con estensione csproj:*

```dotnetcli
dotnet user-secrets clear
```

Tutti i segreti utente per l'app sono stati eliminati dal file *secrets.json:*

```json
{}
```

In `dotnet user-secrets list` esecuzione viene visualizzato il seguente messaggio:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Risorse aggiuntive

* Vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) per informazioni sull'accesso a Secret Manager da IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end