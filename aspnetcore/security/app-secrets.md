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
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Archiviazione sicura dei segreti delle app in fase di sviluppo in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)e [Scott Addie](https://github.com/scottaddie)

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

Questo documento illustra le tecniche per l'archiviazione e il recupero di dati sensibili durante lo sviluppo di un'app ASP.NET Core in un computer di sviluppo. Non archiviare mai le password o altri dati sensibili nel codice sorgente. I segreti di produzione non devono essere usati per lo sviluppo o il test. I segreti non devono essere distribuiti con l'app. Al contrario, i segreti devono essere resi disponibili nell'ambiente di produzione tramite un mezzo controllato, come le variabili di ambiente, Azure Key Vault e così via. È possibile archiviare e proteggere i segreti di test e di produzione di Azure con il [provider di configurazione Azure Key Vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Variabili di ambiente

Le variabili di ambiente vengono usate per evitare l'archiviazione di segreti dell'app nel codice o nei file di configurazione locali. Le variabili di ambiente sostituiscono i valori di configurazione per tutte le origini di configurazione precedentemente specificate.

Si consideri un'app Web ASP.NET Core in cui è abilitata la sicurezza dei **singoli account utente** . Una stringa di connessione al database predefinita è inclusa nel file *appSettings. JSON* del progetto con la `DefaultConnection`chiave. La stringa di connessione predefinita è per il database locale, che viene eseguito in modalità utente e non richiede una password. Durante la distribuzione dell'app `DefaultConnection` , il valore della chiave può essere sostituito con il valore di una variabile di ambiente. La variabile di ambiente può archiviare la stringa di connessione completa con credenziali riservate.

> [!WARNING]
> Le variabili di ambiente vengono in genere archiviate in testo normale e non crittografato. Se il computer o il processo è compromesso, le variabili di ambiente possono essere accessibili da entità non attendibili. Potrebbero essere necessarie misure aggiuntive per impedire la divulgazione di segreti utente.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Gestione segreta

Lo strumento Gestione Secret archivia i dati sensibili durante lo sviluppo di un progetto ASP.NET Core. In questo contesto, una parte di dati sensibili è un segreto dell'app. I segreti dell'app vengono archiviati in un percorso separato dall'albero del progetto. I segreti dell'app sono associati a un progetto specifico o condivisi tra più progetti. I segreti dell'app non vengono archiviati nel controllo del codice sorgente.

> [!WARNING]
> Lo strumento Secret Manager non crittografa i segreti archiviati e non deve essere considerato come un archivio attendibile. È solo a scopo di sviluppo. Le chiavi e i valori vengono archiviati in un file di configurazione JSON nella directory del profilo utente.

## <a name="how-the-secret-manager-tool-works"></a>Funzionamento dello strumento di gestione dei segreti

Lo strumento di gestione dei segreti estrae i dettagli di implementazione, ad esempio dove e come vengono archiviati i valori. È possibile utilizzare lo strumento senza conoscere i dettagli di implementazione. I valori vengono archiviati in un file di configurazione JSON in una cartella del profilo utente protetta dal sistema nel computer locale:

# <a name="windows"></a>[Windows](#tab/windows)

Percorso del file System:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Percorso del file System:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Nei percorsi di file precedenti sostituire `<user_secrets_id>` con il `UserSecretsId` valore specificato nel file con *estensione csproj* .

Non scrivere codice che dipende dalla posizione o dal formato dei dati salvati con lo strumento di gestione dei segreti. Questi dettagli di implementazione possono cambiare. Ad esempio, i valori dei segreti non sono crittografati, ma potrebbero essere futuri.

## <a name="enable-secret-storage"></a>Abilita archiviazione segreta

Lo strumento Gestione Secret funziona sulle impostazioni di configurazione specifiche del progetto archiviate nel profilo utente.

Lo strumento di gestione dei segreti `init` include un comando in .NET Core SDK 3.0.100 o versione successiva. Per usare i segreti utente, eseguire il comando seguente nella directory del progetto:

```dotnetcli
dotnet user-secrets init
```

Il comando precedente aggiunge un `UserSecretsId` elemento all'interno `PropertyGroup` di un del file con estensione *csproj* . Per impostazione predefinita, il testo interno `UserSecretsId` di è un GUID. Il testo interno è arbitrario, ma è univoco per il progetto.

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

In Visual Studio fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci segreti utente** dal menu di scelta rapida. Questo movimento aggiunge un `UserSecretsId` elemento, popolato con un GUID, al file con *estensione csproj* .

## <a name="set-a-secret"></a>Imposta un segreto

Definire un segreto dell'app costituito da una chiave e dal relativo valore. Il segreto è associato al `UserSecretsId` valore del progetto. Ad esempio, eseguire il comando seguente dalla directory in cui è presente il file con *estensione csproj* :

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Nell'esempio precedente, i due punti indicano che `Movies` è un valore letterale di oggetto con `ServiceApiKey` una proprietà.

Lo strumento Gestione Secret può essere usato anche da altre directory. Utilizzare l' `--project` opzione per specificare il percorso di file System in cui è presente il file con *estensione csproj* . Ad esempio:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Flat Structure JSON in Visual Studio

Il gesto di **gestione dei segreti utente** di Visual Studio apre un file *Secrets. JSON* nell'editor di testo. Sostituire il contenuto di *Secrets. JSON* con le coppie chiave-valore da archiviare. Ad esempio:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

La struttura JSON è bidimensionale dopo le modifiche `dotnet user-secrets remove` tramite `dotnet user-secrets set`o. Ad esempio, l' `dotnet user-secrets remove "Movies:ConnectionString"` esecuzione comprime il `Movies` valore letterale dell'oggetto. Il file modificato è simile al seguente:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Impostare più segreti

È possibile impostare un batch di segreti inviando tramite pipe JSON `set` al comando. Nell'esempio seguente il contenuto del file *input. JSON* viene inviato tramite pipe al `set` comando.

# <a name="windows"></a>[Windows](#tab/windows)

Aprire una shell dei comandi ed eseguire il comando seguente:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Aprire una shell dei comandi ed eseguire il comando seguente:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Accedere a un segreto

L' [API di configurazione ASP.NET Core](xref:fundamentals/configuration/index) fornisce l'accesso ai segreti di gestione segreti.

L'origine configurazione dei segreti utente viene aggiunta automaticamente in modalità di sviluppo quando il <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> progetto chiama per inizializzare una nuova istanza dell'host con impostazioni predefinite preconfigurate. `CreateDefaultBuilder`chiama <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> è <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

Quando `CreateDefaultBuilder` non viene chiamato, aggiungere l'origine di configurazione dei segreti utente in <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>modo esplicito chiamando. Chiamare `AddUserSecrets` solo quando l'app è in esecuzione nell'ambiente di sviluppo, come illustrato nell'esempio seguente:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

I segreti utente possono essere recuperati `Configuration` tramite l'API:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Mappare i segreti a un POCO

Il mapping di un intero valore letterale di oggetto a un oggetto POCO (una classe .NET semplice con proprietà) è utile per l'aggregazione di proprietà correlate.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Per eseguire il mapping dei segreti precedenti a un POCO, `Configuration` usare la funzionalità di [associazione dell'oggetto grafico](xref:fundamentals/configuration/index#bind-to-an-object-graph) dell'API. Il codice seguente è associato a un oggetto `MovieSettings` poco personalizzato e accede al `ServiceApiKey` valore della proprietà:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

I `Movies:ConnectionString` segreti `Movies:ServiceApiKey` e vengono mappati alle rispettive proprietà `MovieSettings`in:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Sostituzione di stringhe con segreti

L'archiviazione delle password in testo normale non è protetta. Ad esempio, una stringa di connessione del database archiviata in *appSettings. JSON* può includere una password per l'utente specificato:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Un approccio più sicuro consiste nell'archiviare la password come segreto. Ad esempio:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Rimuovere la `Password` coppia chiave-valore dalla stringa di connessione in *appSettings. JSON*. Ad esempio:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

Il valore del segreto può essere impostato sulla <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> proprietà di un oggetto per completare la stringa di connessione:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Elenca i segreti

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Eseguire il comando seguente dalla directory in cui è presente il file con *estensione csproj* :

```dotnetcli
dotnet user-secrets list
```

Viene visualizzato l'output seguente:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Nell'esempio precedente, i due punti nei nomi delle chiavi indicano la gerarchia di oggetti all'interno di *Secrets. JSON*.

## <a name="remove-a-single-secret"></a>Rimuovere un singolo segreto

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Eseguire il comando seguente dalla directory in cui è presente il file con *estensione csproj* :

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Il file *Secrets. JSON* dell'app è stato modificato per rimuovere la coppia chiave-valore associata `MoviesConnectionString` alla chiave:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`Visualizza il messaggio seguente:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Rimuovi tutti i segreti

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Eseguire il comando seguente dalla directory in cui è presente il file con *estensione csproj* :

```dotnetcli
dotnet user-secrets clear
```

Tutti i segreti utente per l'app sono stati eliminati dal file *Secrets. JSON* :

```json
{}
```

In `dotnet user-secrets list` esecuzione viene visualizzato il messaggio seguente:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Risorse aggiuntive

* Per informazioni sull'accesso a gestione Secret da IIS, vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)e [Scott Addie](https://github.com/scottaddie)

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

Questo documento illustra le tecniche per l'archiviazione e il recupero di dati sensibili durante lo sviluppo di un'app ASP.NET Core in un computer di sviluppo. Non archiviare mai le password o altri dati sensibili nel codice sorgente. I segreti di produzione non devono essere usati per lo sviluppo o il test. I segreti non devono essere distribuiti con l'app. Al contrario, i segreti devono essere resi disponibili nell'ambiente di produzione tramite un mezzo controllato, come le variabili di ambiente, Azure Key Vault e così via. È possibile archiviare e proteggere i segreti di test e di produzione di Azure con il [provider di configurazione Azure Key Vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Variabili di ambiente

Le variabili di ambiente vengono usate per evitare l'archiviazione di segreti dell'app nel codice o nei file di configurazione locali. Le variabili di ambiente sostituiscono i valori di configurazione per tutte le origini di configurazione precedentemente specificate.

Si consideri un'app Web ASP.NET Core in cui è abilitata la sicurezza dei **singoli account utente** . Una stringa di connessione al database predefinita è inclusa nel file *appSettings. JSON* del progetto con la `DefaultConnection`chiave. La stringa di connessione predefinita è per il database locale, che viene eseguito in modalità utente e non richiede una password. Durante la distribuzione dell'app `DefaultConnection` , il valore della chiave può essere sostituito con il valore di una variabile di ambiente. La variabile di ambiente può archiviare la stringa di connessione completa con credenziali riservate.

> [!WARNING]
> Le variabili di ambiente vengono in genere archiviate in testo normale e non crittografato. Se il computer o il processo è compromesso, le variabili di ambiente possono essere accessibili da entità non attendibili. Potrebbero essere necessarie misure aggiuntive per impedire la divulgazione di segreti utente.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Gestione segreta

Lo strumento Gestione Secret archivia i dati sensibili durante lo sviluppo di un progetto ASP.NET Core. In questo contesto, una parte di dati sensibili è un segreto dell'app. I segreti dell'app vengono archiviati in un percorso separato dall'albero del progetto. I segreti dell'app sono associati a un progetto specifico o condivisi tra più progetti. I segreti dell'app non vengono archiviati nel controllo del codice sorgente.

> [!WARNING]
> Lo strumento Secret Manager non crittografa i segreti archiviati e non deve essere considerato come un archivio attendibile. È solo a scopo di sviluppo. Le chiavi e i valori vengono archiviati in un file di configurazione JSON nella directory del profilo utente.

## <a name="how-the-secret-manager-tool-works"></a>Funzionamento dello strumento di gestione dei segreti

Lo strumento di gestione dei segreti estrae i dettagli di implementazione, ad esempio dove e come vengono archiviati i valori. È possibile utilizzare lo strumento senza conoscere i dettagli di implementazione. I valori vengono archiviati in un file di configurazione JSON in una cartella del profilo utente protetta dal sistema nel computer locale:

# <a name="windows"></a>[Windows](#tab/windows)

Percorso del file System:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Percorso del file System:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Nei percorsi di file precedenti sostituire `<user_secrets_id>` con il `UserSecretsId` valore specificato nel file con *estensione csproj* .

Non scrivere codice che dipende dalla posizione o dal formato dei dati salvati con lo strumento di gestione dei segreti. Questi dettagli di implementazione possono cambiare. Ad esempio, i valori dei segreti non sono crittografati, ma potrebbero essere futuri.

## <a name="enable-secret-storage"></a>Abilita archiviazione segreta

Lo strumento Gestione Secret funziona sulle impostazioni di configurazione specifiche del progetto archiviate nel profilo utente.

Per usare i segreti utente, definire `UserSecretsId` un elemento in `PropertyGroup` un del file con estensione *csproj* . Il testo interno di `UserSecretsId` è arbitrario, ma è univoco per il progetto. Gli sviluppatori generano in genere un `UserSecretsId`GUID per il.

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> In Visual Studio fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci segreti utente** dal menu di scelta rapida. Questo movimento aggiunge un `UserSecretsId` elemento, popolato con un GUID, al file con *estensione csproj* .

## <a name="set-a-secret"></a>Imposta un segreto

Definire un segreto dell'app costituito da una chiave e dal relativo valore. Il segreto è associato al `UserSecretsId` valore del progetto. Ad esempio, eseguire il comando seguente dalla directory in cui è presente il file con *estensione csproj* :

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Nell'esempio precedente, i due punti indicano che `Movies` è un valore letterale di oggetto con `ServiceApiKey` una proprietà.

Lo strumento Gestione Secret può essere usato anche da altre directory. Utilizzare l' `--project` opzione per specificare il percorso di file System in cui è presente il file con *estensione csproj* . Ad esempio:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Flat Structure JSON in Visual Studio

Il gesto di **gestione dei segreti utente** di Visual Studio apre un file *Secrets. JSON* nell'editor di testo. Sostituire il contenuto di *Secrets. JSON* con le coppie chiave-valore da archiviare. Ad esempio:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

La struttura JSON è bidimensionale dopo le modifiche `dotnet user-secrets remove` tramite `dotnet user-secrets set`o. Ad esempio, l' `dotnet user-secrets remove "Movies:ConnectionString"` esecuzione comprime il `Movies` valore letterale dell'oggetto. Il file modificato è simile al seguente:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Impostare più segreti

È possibile impostare un batch di segreti inviando tramite pipe JSON `set` al comando. Nell'esempio seguente il contenuto del file *input. JSON* viene inviato tramite pipe al `set` comando.

# <a name="windows"></a>[Windows](#tab/windows)

Aprire una shell dei comandi ed eseguire il comando seguente:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Aprire una shell dei comandi ed eseguire il comando seguente:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Accedere a un segreto

L' [API di configurazione ASP.NET Core](xref:fundamentals/configuration/index) fornisce l'accesso ai segreti di gestione segreti.

Se il progetto è destinato .NET Framework, installare il pacchetto NuGet [Microsoft. Extensions. Configuration. UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .

In ASP.NET Core 2,0 o versioni successive, l'origine configurazione dei segreti utente viene aggiunta automaticamente in modalità di sviluppo quando <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> il progetto chiama per inizializzare una nuova istanza dell'host con impostazioni predefinite preconfigurate. `CreateDefaultBuilder`chiama <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> è <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

Quando `CreateDefaultBuilder` non viene chiamato, aggiungere l'origine di configurazione dei segreti utente in <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> modo esplicito chiamando nel `Startup` costruttore. Chiamare `AddUserSecrets` solo quando l'app è in esecuzione nell'ambiente di sviluppo, come illustrato nell'esempio seguente:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

I segreti utente possono essere recuperati `Configuration` tramite l'API:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Mappare i segreti a un POCO

Il mapping di un intero valore letterale di oggetto a un oggetto POCO (una classe .NET semplice con proprietà) è utile per l'aggregazione di proprietà correlate.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Per eseguire il mapping dei segreti precedenti a un POCO, `Configuration` usare la funzionalità di [associazione dell'oggetto grafico](xref:fundamentals/configuration/index#bind-to-an-object-graph) dell'API. Il codice seguente è associato a un oggetto `MovieSettings` poco personalizzato e accede al `ServiceApiKey` valore della proprietà:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

I `Movies:ConnectionString` segreti `Movies:ServiceApiKey` e vengono mappati alle rispettive proprietà `MovieSettings`in:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Sostituzione di stringhe con segreti

L'archiviazione delle password in testo normale non è protetta. Ad esempio, una stringa di connessione del database archiviata in *appSettings. JSON* può includere una password per l'utente specificato:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Un approccio più sicuro consiste nell'archiviare la password come segreto. Ad esempio:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Rimuovere la `Password` coppia chiave-valore dalla stringa di connessione in *appSettings. JSON*. Ad esempio:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Il valore del segreto può essere impostato sulla <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> proprietà di un oggetto per completare la stringa di connessione:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Elenca i segreti

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Eseguire il comando seguente dalla directory in cui è presente il file con *estensione csproj* :

```dotnetcli
dotnet user-secrets list
```

Viene visualizzato l'output seguente:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Nell'esempio precedente, i due punti nei nomi delle chiavi indicano la gerarchia di oggetti all'interno di *Secrets. JSON*.

## <a name="remove-a-single-secret"></a>Rimuovere un singolo segreto

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Eseguire il comando seguente dalla directory in cui è presente il file con *estensione csproj* :

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Il file *Secrets. JSON* dell'app è stato modificato per rimuovere la coppia chiave-valore associata `MoviesConnectionString` alla chiave:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

In `dotnet user-secrets list` esecuzione viene visualizzato il messaggio seguente:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Rimuovi tutti i segreti

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Eseguire il comando seguente dalla directory in cui è presente il file con *estensione csproj* :

```dotnetcli
dotnet user-secrets clear
```

Tutti i segreti utente per l'app sono stati eliminati dal file *Secrets. JSON* :

```json
{}
```

In `dotnet user-secrets list` esecuzione viene visualizzato il messaggio seguente:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Risorse aggiuntive

* Per informazioni sull'accesso a gestione Secret da IIS, vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end