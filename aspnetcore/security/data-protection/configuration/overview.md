---
title: Configurare la protezione dati di ASP.NET Core
author: rick-anderson
description: Informazioni su come configurare la protezione dei dati in ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 0ff211624b7cf363da393a627c761302d9f3d8ed
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019755"
---
# <a name="configure-aspnet-core-data-protection"></a>Configurare la protezione dati di ASP.NET Core

Quando il sistema di protezione dei dati viene inizializzato, applica [le impostazioni predefinite](xref:security/data-protection/configuration/default-settings) in base all'ambiente operativo. Queste impostazioni sono generalmente appropriate per le app in esecuzione in un singolo computer. In alcuni casi uno sviluppatore potrebbe voler modificare le impostazioni predefinite:

* L'app viene distribuita tra più computer.
* Per motivi di conformità.

Per questi scenari, il sistema di protezione dei dati offre un'API di configurazione avanzata.

> [!WARNING]
> Analogamente ai file di configurazione, l'anello della chiave di protezione dei dati deve essere protetto con le autorizzazioni appropriate. È possibile scegliere di crittografare le chiavi inattive, ma ciò non impedisce agli utenti malintenzionati di creare nuove chiavi. Di conseguenza, si ripercuote sulla sicurezza dell'app. Il percorso di archiviazione configurato con la protezione dati dovrebbe avere accesso limitato all'app stessa, in modo analogo al modo in cui si proteggono i file di configurazione. Ad esempio, se si sceglie di archiviare l'anello chiave su disco, usare file system autorizzazioni. Verificare solo l'identità con cui viene eseguita l'app Web ha accesso in lettura, scrittura e creazione a tale directory. Se si usa l'archiviazione BLOB di Azure, solo l'app Web deve essere in grado di leggere, scrivere o creare nuove voci nell'archivio BLOB e così via.
>
> Il metodo di estensione [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) restituisce un [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder). `IDataProtectionBuilder`espone i metodi di estensione che è possibile concatenare per configurare le opzioni di protezione dati.

::: moniker range=">= aspnetcore-3.0"

Per le estensioni per la protezione dei dati usate in questo articolo sono necessari i pacchetti NuGet seguenti:

* [Microsoft. AspNetCore. dataprotection. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)
* [Microsoft. AspNetCore. dataprotection. AzureKeyVault](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault/)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a>ProtectKeysWithAzureKeyVault

Per archiviare le chiavi in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configurare il sistema con [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) nella `Startup` classe:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Impostare la posizione di archiviazione dell'anello di chiave (ad esempio, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)). Il percorso deve essere impostato perché la chiamata di `ProtectKeysWithAzureKeyVault` implementa un [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) che disabilita le impostazioni di protezione dati automatiche, inclusa la posizione di archiviazione dell'anello di chiave. L'esempio precedente usa l'archivio BLOB di Azure per salvare in modo permanente l'anello di chiave. Per altre informazioni, vedere [provider di archiviazione chiavi: archiviazione di Azure](xref:security/data-protection/implementation/key-storage-providers#azure-storage). È anche possibile salvare l'anello chiave localmente con [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).

`keyIdentifier`È l'identificatore di chiave dell'insieme di credenziali delle chiavi usato per la crittografia delle chiavi. Ad esempio, una chiave creata in Key Vault denominata `dataprotection` in `contosokeyvault` ha l'identificatore di chiave `https://contosokeyvault.vault.azure.net/keys/dataprotection/` . Fornire all'app le autorizzazioni **Unwrap Key** e **Wrap Key** per l'insieme di credenziali delle chiavi.

`ProtectKeysWithAzureKeyVault`Overload

* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) consente l'uso di un [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) per consentire al sistema di protezione dei dati di usare l'insieme di credenziali delle chiavi.
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) consente l'uso di `ClientId` e [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) per consentire al sistema di protezione dei dati di usare l'insieme di credenziali delle chiavi.
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) consente l'uso di `ClientId` e `ClientSecret` per consentire al sistema di protezione dei dati di usare l'insieme di credenziali delle chiavi.

Quando si usa una combinazione di Key Vault e archiviazione di Azure per archiviare e proteggere le chiavi, `System.UriFormatException` verrà generata un'eccezione se il BLOB in cui archiviare le chiavi non esiste già. Questa operazione può essere creata manualmente prima di eseguire l'applicazione oppure `.ProtectKeysWithAzureKeyVault()` può essere rimossa per la prima esecuzione per creare il BLOB, quindi aggiungerlo per le esecuzioni successive. `.ProtectKeysWithAzureKeyVault()`La rimozione è consigliata, in quanto in questo modo si garantisce che il file venga creato con lo schema e i valori appropriati.

```csharp
var storageAccount = CloudStorageAccount.Parse("<storage account connection string">);
var client = storageAccount.CreateCloudBlobClient();
var container = client.GetContainerReference("<key store container name>");

var azureServiceTokenProvider = new AzureServiceTokenProvider();
var keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(
        azureServiceTokenProvider.KeyVaultTokenCallback));

services.AddDataProtection()
    //This blob must already exist before the application is run
    .PersistKeysToAzureBlobStorage(container, "<key store blob name>")
    //Removing this line below for an initial run will ensure the file is created correctly
    .ProtectKeysWithAzureKeyVault(keyVaultClient, "<keyIdentifier>");
```

::: moniker-end

## <a name="persistkeystofilesystem"></a>PersistKeysToFileSystem

Per archiviare le chiavi in una condivisione UNC anziché nel percorso predefinito *% LocalAppData%* , configurare il sistema con [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> Se si modifica il percorso di persistenza della chiave, il sistema non crittografa più automaticamente le chiavi inattive, perché non è in grado di stabilire se DPAPI è un meccanismo di crittografia appropriato.

## <a name="protectkeyswith"></a>ProtectKeysWith\*

È possibile configurare il sistema in modo da proteggere le chiavi inattive chiamando una delle API di configurazione [ProtectKeysWith \* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) . Si consideri l'esempio seguente, in cui le chiavi vengono archiviate in una condivisione UNC e le chiavi inattive vengono crittografate con un certificato X. 509 specifico:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

In ASP.NET Core 2,1 o versioni successive, è possibile fornire un oggetto [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) a [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), ad esempio un certificato caricato da un file:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
}
```

::: moniker-end

Per ulteriori esempi e discussioni sui meccanismi di crittografia chiavi incorporati, vedere la pagina relativa [alla crittografia dei tasti](xref:security/data-protection/implementation/key-encryption-at-rest) inattivi.

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a>UnprotectKeysWithAnyCertificate

In ASP.NET Core 2,1 o versioni successive è possibile ruotare i certificati e decrittografare le chiavi inattive usando una matrice di certificati [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) con [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
        .UnprotectKeysWithAnyCertificate(
            new X509Certificate2("certificate_old_1.pfx", "password_1"),
            new X509Certificate2("certificate_old_2.pfx", "password_2"));
}
```

::: moniker-end

## <a name="setdefaultkeylifetime"></a>SetDefaultKeyLifetime

Per configurare il sistema in modo da usare una durata della chiave di 14 giorni anziché i 90 giorni predefiniti, usare [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>SetApplicationName

Per impostazione predefinita, il sistema di protezione dei dati isola le app l'una dall'altra in base ai percorsi [radice del contenuto](xref:fundamentals/index#content-root) , anche se condividono lo stesso repository di chiavi fisiche. In questo modo si impedisce alle app di comprendere i payload protetti degli altri.

Per condividere i payload protetti tra le app:

* Configurare <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in ogni app con lo stesso valore.
* Usare la stessa versione dello stack di Data Protection API tra le app. Eseguire **una** delle operazioni seguenti nei file di progetto delle app:
  * Fare riferimento alla stessa versione del Framework condiviso tramite il [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).
  * Fare riferimento alla stessa versione del [pacchetto di protezione dati](xref:security/data-protection/introduction#package-layout) .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a>DisableAutomaticKeyGeneration

Si potrebbe avere uno scenario in cui non si vuole che un'app richieda automaticamente le chiavi (creare nuove chiavi) Man mano che si avvicinano alla scadenza. Un esempio potrebbe essere costituito da app configurate in una relazione primaria/secondaria, in cui solo l'app principale è responsabile per i problemi di gestione delle chiavi e le app secondarie dispongono semplicemente di una visualizzazione di sola lettura dell'anello chiave. Le app secondarie possono essere configurate in modo da considerare l'anello chiave come di sola lettura configurando il sistema con <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*> :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a>Isolamento per applicazione

Quando il sistema di protezione dei dati viene fornito da un host ASP.NET Core, isola automaticamente le app l'una dall'altra, anche se tali app sono in esecuzione con lo stesso account del processo di lavoro e usano lo stesso materiale per le chiavi master. Si tratta di un tipo simile al modificatore IsolateApps dell'elemento System. Web `<machineKey>` .

Il meccanismo di isolamento funziona tenendo conto di ogni app nel computer locale come tenant univoco, in modo che la <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> radice di una determinata app includa automaticamente l'ID app come discriminatore. L'ID univoco dell'app è il percorso fisico dell'app:

* Per le app ospitate in IIS, l'ID univoco è il percorso fisico IIS dell'app. Se un'app viene distribuita in un ambiente di Web farm, questo valore è stabile supponendo che gli ambienti IIS siano configurati in modo analogo in tutti i computer del Web farm.
* Per le app indipendenti in esecuzione nel [Server gheppio](xref:fundamentals/servers/index#kestrel), l'ID univoco è il percorso fisico dell'app su disco.

L'identificatore univoco è progettato per sopravvivere per reimpostare &mdash; sia la singola app che quella del computer.

Questo meccanismo di isolamento presuppone che le app non siano dannose. Un'app dannosa può sempre influenzare qualsiasi altra app in esecuzione con lo stesso account del processo di lavoro. In un ambiente host condiviso in cui le app non sono attendibili reciprocamente, il provider di hosting deve eseguire le operazioni necessarie per garantire l'isolamento a livello di sistema operativo tra le app, inclusa la separazione dei repository di chiavi sottostanti delle app.

Se il sistema di protezione dei dati non è fornito da un host ASP.NET Core (ad esempio, se si crea un'istanza tramite il `DataProtectionProvider` tipo concreto) l'isolamento delle app è disabilitato per impostazione predefinita. Quando l'isolamento delle app è disabilitato, tutte le app supportate dallo stesso materiale di chiave possono condividere i payload purché forniscano gli [scopi](xref:security/data-protection/consumer-apis/purpose-strings)appropriati. Per garantire l'isolamento delle app in questo ambiente, chiamare il metodo [Seapplicationname](#setapplicationname) nell'oggetto Configuration e specificare un nome univoco per ogni app.

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>Modifica degli algoritmi con UseCryptographicAlgorithms

Lo stack di protezione dei dati consente di modificare l'algoritmo predefinito usato dalle chiavi appena generate. Il modo più semplice per eseguire questa operazione consiste nel chiamare [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) dal callback di configurazione:

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

Il valore predefinito di EncryptionAlgorithm è AES-256-CBC e il valore predefinito di ValidationAlgorithm è HMACSHA256. I criteri predefiniti possono essere impostati da un amministratore di sistema tramite [criteri a livello di computer](xref:security/data-protection/configuration/machine-wide-policy), ma una chiamata esplicita a `UseCryptographicAlgorithms` sostituisce i criteri predefiniti.

`UseCryptographicAlgorithms`La chiamata a consente di specificare l'algoritmo desiderato da un elenco predefinito predefinito. Non è necessario preoccuparsi dell'implementazione dell'algoritmo. Nello scenario precedente, il sistema di protezione dei dati tenta di usare l'implementazione CNG di AES se eseguita in Windows. In caso contrario, viene eseguito il fallback alla classe gestita [System. Security. Cryptography. AES](/dotnet/api/system.security.cryptography.aes) .

È possibile specificare manualmente un'implementazione tramite una chiamata a [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).

> [!TIP]
> La modifica degli algoritmi non influisce sulle chiavi esistenti nell'anello di chiave. Influiscono solo sulle chiavi appena generate.

### <a name="specifying-custom-managed-algorithms"></a>Specifica di algoritmi gestiti personalizzati

::: moniker range=">= aspnetcore-2.0"

Per specificare algoritmi gestiti personalizzati, creare un'istanza di [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) che punti ai tipi di implementazione:

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Per specificare algoritmi gestiti personalizzati, creare un'istanza di [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) che punti ai tipi di implementazione:

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

In genere \* , le proprietà del tipo devono puntare alle implementazioni concrete, istanziabile (tramite un ctor pubblico senza parametri) di [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) e [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), anche se il sistema specifica alcuni valori come `typeof(Aes)` per praticità.

> [!NOTE]
> SymmetricAlgorithm deve avere una lunghezza della chiave di ≥ 128 bit e una dimensione del blocco di ≥ 64 bit e deve supportare la crittografia in modalità CBC con la spaziatura interna PKCS #7. Il valore di KeyedHashAlgorithm deve avere una dimensione del digest di >= 128 bit e deve supportare chiavi di lunghezza uguale alla lunghezza del digest dell'algoritmo hash. Il KeyedHashAlgorithm non deve necessariamente essere HMAC.

### <a name="specifying-custom-windows-cng-algorithms"></a>Specifica di algoritmi CNG personalizzati di Windows

::: moniker range=">= aspnetcore-2.0"

Per specificare un algoritmo CNG di Windows personalizzato utilizzando la crittografia in modalità CBC con la convalida HMAC, creare un'istanza di [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) contenente le informazioni algoritmiche:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Per specificare un algoritmo CNG di Windows personalizzato utilizzando la crittografia in modalità CBC con la convalida HMAC, creare un'istanza di [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) contenente le informazioni algoritmiche:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

> [!NOTE]
> L'algoritmo di crittografia a blocchi simmetrico deve avere una lunghezza della chiave di >= 128 bit, una dimensione del blocco di >= 64 bit e deve supportare la crittografia in modalità CBC con la spaziatura interna PKCS #7. L'algoritmo hash deve avere una dimensione del digest di >= 128 bit e deve supportare l'apertura con il \_ \_ flag di \_ flag HMAC handle BCRYPT ALG \_ . Le \* proprietà del provider possono essere impostate su null per utilizzare il provider predefinito per l'algoritmo specificato. Per ulteriori informazioni, vedere la documentazione di [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) .

::: moniker range=">= aspnetcore-2.0"

Per specificare un algoritmo CNG di Windows personalizzato con la crittografia della modalità di crittografia/contatore con la convalida, creare un'istanza di [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) contenente le informazioni algoritmiche:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Per specificare un algoritmo CNG di Windows personalizzato con la crittografia della modalità di crittografia/contatore con la convalida, creare un'istanza di [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) contenente le informazioni algoritmiche:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

> [!NOTE]
> L'algoritmo di crittografia a blocchi simmetrico deve avere una lunghezza della chiave di >= 128 bit, una dimensione del blocco di esattamente 128 bit e deve supportare la crittografia GCM. È possibile impostare la proprietà [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) su null per utilizzare il provider predefinito per l'algoritmo specificato. Per ulteriori informazioni, vedere la documentazione di [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) .

### <a name="specifying-other-custom-algorithms"></a>Specifica di altri algoritmi personalizzati

Sebbene non venga esposto come API di prima classe, il sistema di protezione dei dati è sufficientemente estensibile per consentire l'impostazione di quasi tutti i tipi di algoritmo. Ad esempio, è possibile salvare tutte le chiavi contenute in un modulo di protezione hardware (HSM) e fornire un'implementazione personalizzata delle routine di crittografia e decrittografia di base. Per altre informazioni, vedere [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) nell' [estensibilità di crittografia di base](xref:security/data-protection/extensibility/core-crypto) .

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>Salvataggio permanente delle chiavi durante l'hosting in un contenitore Docker

Quando si esegue l'hosting in un contenitore [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) , le chiavi devono essere mantenute in uno degli:

* Una cartella che è un volume Docker che è permanente oltre la durata del contenitore, ad esempio un volume condiviso o un volume montato dall'host.
* Provider esterno, ad esempio [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) o [Redis](https://redis.io/).

## <a name="persisting-keys-with-redis"></a>Salvataggio permanente delle chiavi con Redis

Per archiviare le chiavi è necessario usare solo le versioni di redis che supportano la [persistenza dei dati Redis](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) . L' [archiviazione BLOB di Azure](/azure/storage/blobs/storage-blobs-introduction) è persistente e può essere usata per archiviare le chiavi. Per altre informazioni, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore/issues/13476).

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
