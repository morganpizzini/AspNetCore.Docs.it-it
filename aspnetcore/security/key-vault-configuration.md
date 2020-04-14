---
title: Azure Key Vault Configuration Provider in ASP.NET Core
author: rick-anderson
description: Informazioni su come usare il provider di configurazione dell'insieme di credenziali delle chiavi di Azure per configurare un'app usando coppie nome-valore caricate in fase di esecuzione.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: security/key-vault-configuration
ms.openlocfilehash: d78584eaa3fcd50425698e4db581060b6ca845f8
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228166"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>Azure Key Vault Configuration Provider in ASP.NET Core

Di [Andrew Stanton-Nurse](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

Questo documento spiega come usare il provider di configurazione [dell'insieme](https://azure.microsoft.com/services/key-vault/) di credenziali delle chiavi di Microsoft Azure per caricare i valori di configurazione dell'app dai segreti dell'insieme di credenziali delle chiavi di Azure.This document explains how to use the Microsoft Azure Key Vault Configuration Provider to load app configuration values from Azure Key Vault secrets. L'insieme di credenziali delle chiavi di Azure è un servizio basato su cloud che facilita la protezione delle chiavi e dei segreti crittografici usati da app e servizi. Gli scenari comuni per l'uso di Archiviazione delle chiavi di Azure con le app di ASP.NET Core includono:Common scenarios for using Azure Key Vault with ASP.NET Core apps include:

* Controllo dell'accesso ai dati di configurazione sensibili.
* Soddisfare il requisito per i moduli hardware di sicurezza hardware convalidati (HSM) convalidati FIPS 140-2 durante l'archiviazione dei dati di configurazione.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Pacchetti

Aggiungere un riferimento al pacchetto [Microsoft.Extensions.Configuration.AzureKeyVault.Add](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) a package reference to the Microsoft.Extensions.Configuration.AzureKeyVault package.

## <a name="sample-app"></a>App di esempio

L'app di esempio viene eseguita `#define` in una delle due modalità determinate dall'istruzione all'inizio del file *Program.cs:*

* `Certificate`&ndash; Viene illustrato l'uso di un ID client di Archiviazione chiavi di Azure e di un certificato X.509 per accedere ai segreti archiviati in Archiviazione chiavi di Azure.Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault. Questa versione dell'esempio può essere eseguita da qualsiasi posizione, distribuita nel servizio app di Azure o in qualsiasi host in grado di servire un'app ASP.NET Core.This version of the sample can be run from any location, deployed to Azure App Service or any host capable to serving an ASP.NET Core app.
* `Managed`&ndash; Viene illustrato come usare [le identità gestite per le risorse](/azure/active-directory/managed-identities-azure-resources/overview) di Azure per autenticare l'app in Un insieme di credenziali delle chiavi di Azure con l'autenticazione di Azure AD senza credenziali archiviate nel codice o nella configurazione dell'app. Quando si usano le identità gestite per l'autenticazione, non sono necessari un ID applicazione e una password di Azure AD (segreto client). La `Managed` versione dell'esempio deve essere distribuita in Azure.The version of the sample must be deployed to Azure. Seguire le indicazioni nella sezione Usare le identità gestite per le risorse di Azure.Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.

Per ulteriori informazioni su come configurare un'app`#define`di <xref:index#preprocessor-directives-in-sample-code>esempio usando direttive per il preprocessore ( ), vedere .

## <a name="secret-storage-in-the-development-environment"></a>Archiviazione segreta nell'ambiente di sviluppo

Impostare i segreti in locale utilizzando [lo strumento Secret Manager](xref:security/app-secrets). Quando l'app di esempio viene eseguita nel computer locale nell'ambiente di sviluppo, i segreti vengono caricati dall'archivio Secret Manager locale.

Lo strumento Secret `<UserSecretsId>` Manager richiede una proprietà nel file di progetto dell'app. Impostare il`{GUID}`valore della proprietà ( ) su qualsiasi GUID univoco:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

I segreti vengono creati come coppie nome-valore. I valori gerarchici (sezioni di configurazione) usano un `:` (due punti) come separatore nei nomi delle chiavi di configurazione ASP.NET Core.Hierarchical values (configuration sections) use a (colon) as a separator in ASP.NET Core [configuration](xref:fundamentals/configuration/index) key names.

Secret Manager viene utilizzato da una shell dei comandi aperta `{SECRET NAME}` alla radice `{SECRET VALUE}` del [contenuto](xref:fundamentals/index#content-root)del progetto, dove è il nome ed è il valore:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Eseguire i comandi seguenti in una shell dei comandi dalla radice del contenuto del progetto per impostare i segreti per l'app di esempio:Execute the following commands in a command shell from the [project's content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Quando questi segreti vengono archiviati in Archiviazione chiave di Azure nella sezione [Archiviazione segreta nell'ambiente](#secret-storage-in-the-production-environment-with-azure-key-vault) di produzione con l'insieme di credenziali delle chiavi di Azure, il `_dev` suffisso viene modificato in . `_prod` Il suffisso fornisce un segnale visivo nell'output dell'app che indica l'origine dei valori di configurazione.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Archiviazione segreta nell'ambiente di produzione con l'insieme di credenziali delle chiavi di AzureSecret storage in the Production environment with Azure Key Vault

Le istruzioni fornite dalla [Guida introduttiva: Impostare e recuperare un segreto dall'argomento dell'interfaccia](/azure/key-vault/quick-create-cli) di rete di Azure usano l'interfaccia della riga di comando di Azure sono riepilogate qui per la creazione di un insieme di credenziali delle chiavi di Azure e l'archiviazione dei segreti usati dall'app di esempio. Fare riferimento all'argomento per ulteriori dettagli.

1. Aprire la shell del cloud di Azure usando uno dei metodi seguenti nel portale di Azure:Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):

   * Selezionare **Prova** nell'angolo superiore destro di un blocco di codice. Usare la stringa di ricerca "Azure CLI" nella casella di testo.
   * Apri Cloud Shell nel browser con il pulsante **Avvia Cloud Shell.**
   * Selezionare il pulsante **Cloud Shell** nel menu nell'angolo in alto a destra del portale di Azure.

   Per altre informazioni, vedere [Interfaccia della riga di comando di Azure](/cli/azure/) e [Panoramica di Azure Cloud Shell.For](/azure/cloud-shell/overview)more information, see Azure CLI and Overview of Azure Cloud Shell .

1. Se non si è già autenticati, `az login` accedere con il comando.

1. Creare un gruppo di risorse `{RESOURCE GROUP NAME}` con il comando seguente, dove `{LOCATION}` è il nome del nuovo gruppo di risorse ed è l'area di Azure (data center):Create a resource group with the following command, where is the resource group name for the new resource group and is the Azure region (datacenter):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Creare un insieme di credenziali delle chiavi `{KEY VAULT NAME}` nel gruppo di risorse con `{LOCATION}` il comando seguente, dove è il nome del nuovo insieme di credenziali delle chiavi ed è l'area di Azure (data center):Create a key vault in the resource group with the following command, where is the name for the new key vault and is the Azure region (datacenter):

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Creare segreti nell'insieme di credenziali delle chiavi come coppie nome-valore.

   I nomi dei segreti dell'insieme di credenziali delle chiavi di Azure sono limitati a caratteri alfanumerici e trattini. I valori gerarchici `--` (sezioni di configurazione) utilizzano (due trattini) come separatore. I due, che in genere vengono utilizzati per delimitare una sezione da una sottochiave in [ASP.NET configurazione Core](xref:fundamentals/configuration/index), non sono consentiti nei nomi dei segreti dell'insieme di credenziali delle chiavi. Pertanto, due trattini vengono utilizzati e scambiati per i due punti quando i segreti vengono caricati nella configurazione dell'app.

   I segreti seguenti sono per l'uso con l'app di esempio. I valori `_prod` includono un suffisso `_dev` per distinguerli dai valori dei suffissi caricati nell'ambiente di sviluppo dai segreti utente. Sostituire `{KEY VAULT NAME}` con il nome dell'insieme di credenziali delle chiavi creato nel passaggio precedente:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Usare l'ID applicazione e il certificato X.509 per le app non ospitate da AzureUse Application ID and X.509 certificate for non-Azure-hosted apps

Configurare Azure AD, L'insieme di credenziali delle chiavi di Azure e l'app per l'uso di un ID applicazione di Azure Active Directory e di un certificato X.509 per l'autenticazione in un insieme di credenziali delle chiavi **quando l'app è ospitata all'esterno di Azure.Configure**Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault when the app is hosted outside of Azure. Per i dettagli, vedere l'articolo relativo alle [informazioni su chiavi, segreti e certificati](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Anche se l'uso di un ID applicazione e di un certificato X.509 è supportato per le app ospitate in Azure, è consigliabile usare [le identità gestite per le risorse](#use-managed-identities-for-azure-resources) di Azure quando si ospita un'app in Azure.Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using Managed identities for Azure resources when hosting an app in Azure. Le identità gestite non richiedono l'archiviazione di un certificato nell'app o nell'ambiente di sviluppo.

L'app di esempio usa un ID applicazione `#define` e un certificato X.509 `Certificate`quando l'istruzione all'inizio del file di *Program.cs* è impostata su .

1. Creare un certificato di archiviazione PKCS-12 (*.pfx*). Le opzioni per la creazione di certificati includono [MakeCert in Windows](/windows/desktop/seccrypto/makecert) e [OpenSSL](https://www.openssl.org/).
1. Installare il certificato nell'archivio certificati personale dell'utente corrente. Contrassegnare la chiave come esportabile è facoltativo. Si noti l'identificazione personale del certificato, che viene utilizzata più avanti in questo processo.
1. Esportare il certificato di archivio PKCS-12 (*.pfx*) come certificato con codifica DER (*.cer*).
1. Registrare l'app con Azure AD (**Registrazioni app**).
1. Caricare il certificato con codifica DER ( .cer ) in Azure AD:Upload the DER-encoded certificate (*.cer*) to Azure AD:
   1. Selezionare l'app in Azure AD.
   1. Passare a **Certificati & segreti**.
   1. Selezionare **Carica certificato** per caricare il certificato, che contiene la chiave pubblica. Un certificato *con estensione cer,* *pem*o *crt* è accettabile.
1. Archiviare il nome dell'insieme di credenziali delle chiavi, l'ID applicazione e l'identificazione personale del certificato nel file *appsettings.json* dell'app.
1. Passare a **Insiemi di credenziali delle chiavi** nel portale di Azure.Navigate to Key vaults in the Azure portal.
1. Selezionare l'insieme di credenziali delle chiavi creato nella sezione [Archiviazione segreta nell'ambiente](#secret-storage-in-the-production-environment-with-azure-key-vault) di produzione con l'insieme di credenziali delle chiavi di Azure.Select the key vault that you created in the Secret storage in the Production environment with Azure Key Vault section.
1. Selezionare **Criteri di accesso**.
1. Selezionare **Aggiungi criterio di accesso**.
1. Aprire **autorizzazioni segrete** e fornire all'app le autorizzazioni **Get** ed **List.**
1. Selezionare **Seleziona entità** e selezionare l'app registrata in base al nome. Fare clic sul pulsante **Seleziona**.
1. Selezionare **OK**.
1. Selezionare **Salva**.
1. Distribuire l'app.

L'app `Certificate` di esempio ottiene `IConfigurationRoot` i valori di configurazione da con lo stesso nome del nome del segreto:The sample app obtains its configuration values from with the same name as the secret name:

* Valori non gerarchici: `SecretName` il valore `config["SecretName"]`per viene ottenuto con .
* Valori gerarchici (sezioni): usare `:` la `GetSection` notazione (due punti) o il metodo di estensione. Utilizzare uno di questi approcci per ottenere il valore di configurazione:Use either of these approaches to obtain the configuration value:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Il certificato X.509 è gestito dal sistema operativo. L'app <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> chiama con i valori forniti dal file *appsettings.json:*

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Valori di esempio:

* Nome dell'insieme di credenziali delle chiavi:`contosovault`
* ID applicazione:`627e911e-43cc-61d4-992e-12db9c81b413`
* Identificazione personale certificato:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

Quando si esegue l'app, una pagina Web mostra i valori segreti caricati. Nell'ambiente di sviluppo, i `_dev` valori segreti vengono caricati con il suffisso. Nell'ambiente di produzione, i `_prod` valori vengono caricati con il suffisso.

## <a name="use-managed-identities-for-azure-resources"></a>Usare le identità gestite per le risorse di AzureUse Managed identities for Azure resources

**Un'app distribuita in Azure** può [sfruttare le identità gestite per le risorse](/azure/active-directory/managed-identities-azure-resources/overview)di Azure, che consente all'app di eseguire l'autenticazione con l'insieme di credenziali delle chiavi di Azure usando l'autenticazione di Azure AD senza credenziali (ID applicazione e segreto password/client) archiviate nell'app.

L'app di esempio usa le identità gestite per le risorse di Azure quando l'istruzione `#define` all'inizio del file *di Program.cs* è impostata su . `Managed`

Immettere il nome dell'insieme di credenziali nel file *appsettings.json* dell'app. L'app di esempio non richiede un ID applicazione e `Managed` una password (segreto client) quando è impostata sulla versione, pertanto è possibile ignorare tali voci di configurazione. L'app viene distribuita in Azure e Azure autentica l'app per accedere all'insieme di credenziali delle chiavi di Azure solo usando il nome dell'insieme di credenziali archiviato nel file *appsettings.json.*

Distribuire l'app di esempio nel servizio app di Azure.Deploy the sample app to Azure App Service.

Un'app distribuita nel servizio app di Azure viene registrata automaticamente con Azure AD quando viene creato il servizio. Ottenere l'ID oggetto dalla distribuzione per l'utilizzo nel comando seguente. L'ID oggetto viene visualizzato nel portale di Azure nel riquadro **Identità** del servizio app.

Usando l'interfaccia della riga di comando di `list` `get` Azure e l'ID oggetto dell'app, fornisci all'app e le autorizzazioni per accedere all'insieme di credenziali delle chiavi:Using Azure CLI and the app's Object ID, provide the app with and permissions to access the key vault:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Riavviare l'app usando l'interfaccia** della riga di comando di Azure, PowerShell o il portale di Azure.Restart the app using Azure CLI, PowerShell, or the Azure portal.

L'app di esempio:The sample app:

* Crea un'istanza `AzureServiceTokenProvider` della classe senza una stringa di connessione. Quando non viene fornita una stringa di connessione, il provider tenta di ottenere un token di accesso da identità gestite per le risorse di Azure.When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.
* Viene <xref:Microsoft.Azure.KeyVault.KeyVaultClient> creato un `AzureServiceTokenProvider` nuovo con il callback del token di istanza.
* L'istanza <xref:Microsoft.Azure.KeyVault.KeyVaultClient> viene utilizzata con <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> un'implementazione predefinita di che carica`--`tutti i valori`:`segreti e sostituisce i trattini doppi ( ) con i due punti ( ) nei nomi delle chiavi.

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Valore di esempio del nome dell'insieme di credenziali delle chiavi:Key vault name example value:`contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Quando si esegue l'app, una pagina Web mostra i valori segreti caricati. Nell'ambiente di sviluppo, `_dev` i valori segreti hanno il suffisso perché vengono forniti da segreti utente. Nell'ambiente di produzione, i `_prod` valori vengono caricati con il suffisso perché vengono forniti dall'insieme di credenziali delle chiavi di Azure.In the Production environment, the values load with the suffix because they're provided by Azure Key Vault.

Se viene `Access denied` visualizzato un errore, verificare che l'app sia registrata con Azure AD e che abbia fornito l'accesso all'insieme di credenziali delle chiavi. Verificare di aver riavviato il servizio in Azure.Confirm that you've restarted the service in Azure.

Per informazioni sull'uso del provider con un'identità gestita e una pipeline DevOps di Azure, vedere Creare una [connessione del servizio Azure Resource Manager a una macchina virtuale con un'identità](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)del servizio gestito.

## <a name="configuration-options"></a>Opzioni di configurazione

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>può accettare <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>un :

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| Proprietà         | Descrizione |
| ---------------- | ----------- |
| `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient>da utilizzare per il recupero dei valori. |
| `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>l'istanza utilizzata per controllare il caricamento segreto. |
| `ReloadInterval` | `Timespan`per attendere tra i tentativi di polling dell'insieme di credenziali delle chiavi per le modifiche. Il valore `null` predefinito è (la configurazione non viene ricaricata). |
| `Vault`          | URI dell'insieme di credenziali delle chiavi. |

## <a name="use-a-key-name-prefix"></a>Usare un prefisso del nome di chiaveUse a key name prefix

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>fornisce un overload che accetta <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>un'implementazione di , che consente di controllare il modo in cui i segreti dell'insieme di credenziali delle chiavi vengono convertiti in chiavi di configurazione. Ad esempio, è possibile implementare l'interfaccia per caricare i valori segreti in base a un valore di prefisso fornito all'avvio dell'app. Ciò consente, ad esempio, di caricare i segreti in base alla versione dell'app.

> [!WARNING]
> Non usare i prefissi sui segreti dell'insieme di credenziali delle chiavi per inserire segreti per più app nello stesso insieme di credenziali delle chiavi o per inserire segreti ambientali (ad esempio, segreti di *sviluppo* e *di produzione)* nello stesso insieme di credenziali. È consigliabile che app e ambienti di sviluppo/produzione diversi utilizzino insiemi di credenziali delle chiavi separati per isolare gli ambienti delle app per ottenere il massimo livello di sicurezza.

Nell'esempio seguente viene stabilito un segreto nell'insieme di credenziali delle chiavi `5000-AppSecret` (e l'utilizzo dello strumento Secret Manager per l'ambiente di sviluppo) per (i punti non sono consentiti nei nomi dei segreti dell'insieme di credenziali delle chiavi). Questo segreto rappresenta un segreto dell'app per la versione 5.0.0.0 dell'app. Per un'altra versione dell'app, 5.1.0.0, viene aggiunto un segreto all'insieme di credenziali delle chiavi (e utilizzando lo strumento Secret Manager) per `5100-AppSecret`. Ogni versione dell'app carica il valore `AppSecret`del segreto con controllo delle versioni nella configurazione come , rimuovendo la versione durante il caricamento del segreto.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>viene chiamato con <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>un:

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

L'implementazione <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> reagisce ai prefissi di versione dei segreti per caricare il segreto corretto nella configurazione:The implementation reacts to the version prefixes of secrets to load the proper secret into configuration:

* `Load`carica un segreto quando il nome inizia con il prefisso. Gli altri segreti non sono cariche.
* `GetKey`:
  * Rimuove il prefisso dal nome del segreto.
  * Sostituisce due trattini in qualsiasi `KeyDelimiter`nome con il , ovvero il delimitatore utilizzato nella configurazione (in genere due punti). L'insieme di credenziali delle chiavi di Azure non consente i due punti nei nomi segreti.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

Il `Load` metodo viene chiamato da un algoritmo del provider che scorre i segreti dell'insieme di credenziali per trovare quelli con il prefisso di versione. Quando viene trovato un `Load`prefisso di `GetKey` versione con , l'algoritmo utilizza il metodo per restituire il nome di configurazione del nome del segreto. Rimuove il prefisso di versione dal nome del segreto e restituisce il resto del nome del segreto per il caricamento nelle coppie nome-valore di configurazione dell'app.

Quando viene implementato questo approccio:When this approach is implemented:

1. Versione dell'app specificata nel file di progetto dell'app. Nell'esempio seguente, la versione dell'app è impostata `5.0.0.0`su:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Verificare `<UserSecretsId>` che una proprietà sia presente nel `{GUID}` file di progetto dell'app, dove è un GUID fornito dall'utente:Confirm that a property is present in the app's project file, where is a user-supplied GUID:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Salvare i seguenti segreti in locale con [lo strumento Secret Manager](xref:security/app-secrets):

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. I segreti vengono salvati nell'insieme di credenziali delle chiavi di Azure usando i comandi dell'interfaccia della riga di comando di Azure seguenti:Secrets are saved in Azure Key Vault using the following Azure CLI commands:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Quando l'app viene eseguita, vengono caricati i segreti dell'insieme di credenziali delle chiavi. La stringa `5000-AppSecret` segreta per viene confrontata con la versione dell'app specificata nel file di progetto dell'app (`5.0.0.0`).

1. La versione `5000` ( con il trattino) viene rimossa dal nome della chiave. In tutta l'app, `AppSecret` la lettura della configurazione con la chiave carica il valore segreto.

1. Se la versione dell'app viene modificata nel file di progetto `5.1.0.0` e l'app viene eseguita di nuovo, il valore segreto restituito si trova `5.1.0.0_secret_value_dev` nell'ambiente di sviluppo e `5.1.0.0_secret_value_prod` in Produzione.

> [!NOTE]
> È inoltre possibile <xref:Microsoft.Azure.KeyVault.KeyVaultClient> fornire <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>la propria implementazione a . Un client personalizzato consente di condividere una singola istanza del client nell'app.

## <a name="bind-an-array-to-a-class"></a>Associare una matrice a una classe

Il provider è in grado di leggere i valori di configurazione in una matrice per l'associazione a una matrice POCO.

Durante la lettura da un'origine di`:`configurazione che consente alle chiavi di contenere separatori di`:0:` `:1:`due &hellip; `:{n}:`punti ( ), viene utilizzato un segmento di chiave numerica per distinguere le chiavi che costituiscono una matrice ( , , ). Per ulteriori informazioni, vedere [Configurazione: associare una matrice a una classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Le chiavi dell'insieme di credenziali delle chiavi di Azure non possono usare i due punti come separatore. L'approccio descritto in questo argomento`--`usa i trattini doppi ( ) come separatore per i valori gerarchici (sezioni). Le chiavi di matrice vengono archiviate in Un insieme`--0--`di `--1--` &hellip; `--{n}--`chiavi di Azure con trattini doppi e segmenti numerici di chiavi ( , , , ).

Esaminare la configurazione del provider di registrazione [Serilog](https://serilog.net/) seguente fornita da un file JSON. Nella matrice sono definiti due `WriteTo` valori letterali oggetto che riflettono due *sink*Serilog , che descrivono le destinazioni per la registrazione dell'output:

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

La configurazione illustrata nel file JSON precedente viene archiviata`--`nell'insieme di credenziali delle chiavi di Azure usando la notazione a doppio trattino ( ) e i segmenti numerici:The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash ( ) notation and numeric segments:

| Chiave | valore |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Ricaricare i segretiReload secrets

I segreti `IConfigurationRoot.Reload()` vengono memorizzati nella cache fino a quando non viene chiamato. I segreti scaduti, disabilitati e aggiornati nell'insieme di credenziali delle chiavi non vengono rispettati dall'app fino all'esecuzione. `Reload`

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Segreti disabilitati e scaduti

Segreti disabilitati e <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>scaduti generano un file . Per impedire la generazione dell'app, fornire la configurazione utilizzando un provider di configurazione diverso o aggiornare il segreto disabilitato o scaduto.

## <a name="troubleshoot"></a>Risolvere problemi

Quando l'app non riesce a caricare la configurazione usando il provider, viene scritto un messaggio di errore [nell'infrastruttura di registrazione di ASP.NET Core](xref:fundamentals/logging/index). Le seguenti condizioni impediranno il caricamento della configurazione:

* L'app o il certificato non è configurato correttamente in Azure Active Directory.The app or certificate isn't configured correctly in Azure Active Directory.
* L'insieme di credenziali delle chiavi non esiste nell'insieme di credenziali delle chiavi di Azure.The key vault doesn't exist in Azure Key Vault.
* L'app non è autorizzata ad accedere all'insieme di credenziali delle chiavi.
* I criteri di accesso `Get` `List` non includono e le autorizzazioni.
* Nell'insieme di credenziali delle chiavi, i dati di configurazione (coppia nome-valore) sono denominati in modo errato, mancanti, disabilitati o scaduti.
* L'app ha il nome`KeyVaultName`dell'insieme di`AzureADApplicationId`credenziali delle chiavi (`AzureADCertThumbprint`), l'ID applicazione di Azure AD ( ) o l'identificazione personale del certificato di Azure AD ( ).
* La chiave di configurazione (nome) non è corretta nell'app per il valore che si sta tentando di caricare.
* Quando si aggiungono i criteri di accesso per l'app all'insieme di credenziali delle chiavi, il criterio è stato creato, ma il pulsante **Salva** non è stato selezionato nell'interfaccia utente dei criteri di **accesso.**

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Key Vault Documentation](/azure/key-vault/)
* [Come generare e trasferire chiavi HSM protette per l'insieme di credenziali delle chiavi di Azure](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient (classe)](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Guida introduttiva: Impostare e recuperare un segreto da Azure Key Vault tramite un'app Web .NET](/azure/key-vault/quick-create-net)
* [Esercitazione: Come usare Azure Key Vault con una macchina virtuale Windows di Azure in .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Questo documento spiega come usare il provider di configurazione [dell'insieme](https://azure.microsoft.com/services/key-vault/) di credenziali delle chiavi di Microsoft Azure per caricare i valori di configurazione dell'app dai segreti dell'insieme di credenziali delle chiavi di Azure.This document explains how to use the Microsoft Azure Key Vault Configuration Provider to load app configuration values from Azure Key Vault secrets. L'insieme di credenziali delle chiavi di Azure è un servizio basato su cloud che facilita la protezione delle chiavi e dei segreti crittografici usati da app e servizi. Gli scenari comuni per l'uso di Archiviazione delle chiavi di Azure con le app di ASP.NET Core includono:Common scenarios for using Azure Key Vault with ASP.NET Core apps include:

* Controllo dell'accesso ai dati di configurazione sensibili.
* Soddisfare il requisito per i moduli hardware di sicurezza hardware convalidati (HSM) convalidati FIPS 140-2 durante l'archiviazione dei dati di configurazione.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Pacchetti

Aggiungere un riferimento al pacchetto [Microsoft.Extensions.Configuration.AzureKeyVault.Add](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) a package reference to the Microsoft.Extensions.Configuration.AzureKeyVault package.

## <a name="sample-app"></a>App di esempio

L'app di esempio viene eseguita `#define` in una delle due modalità determinate dall'istruzione all'inizio del file *Program.cs:*

* `Certificate`&ndash; Viene illustrato l'uso di un ID client di Archiviazione chiavi di Azure e di un certificato X.509 per accedere ai segreti archiviati in Archiviazione chiavi di Azure.Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault. Questa versione dell'esempio può essere eseguita da qualsiasi posizione, distribuita nel servizio app di Azure o in qualsiasi host in grado di servire un'app ASP.NET Core.This version of the sample can be run from any location, deployed to Azure App Service or any host capable to serving an ASP.NET Core app.
* `Managed`&ndash; Viene illustrato come usare [le identità gestite per le risorse](/azure/active-directory/managed-identities-azure-resources/overview) di Azure per autenticare l'app in Un insieme di credenziali delle chiavi di Azure con l'autenticazione di Azure AD senza credenziali archiviate nel codice o nella configurazione dell'app. Quando si usano le identità gestite per l'autenticazione, non sono necessari un ID applicazione e una password di Azure AD (segreto client). La `Managed` versione dell'esempio deve essere distribuita in Azure.The version of the sample must be deployed to Azure. Seguire le indicazioni nella sezione Usare le identità gestite per le risorse di Azure.Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.

Per ulteriori informazioni su come configurare un'app`#define`di <xref:index#preprocessor-directives-in-sample-code>esempio usando direttive per il preprocessore ( ), vedere .

## <a name="secret-storage-in-the-development-environment"></a>Archiviazione segreta nell'ambiente di sviluppo

Impostare i segreti in locale utilizzando [lo strumento Secret Manager](xref:security/app-secrets). Quando l'app di esempio viene eseguita nel computer locale nell'ambiente di sviluppo, i segreti vengono caricati dall'archivio Secret Manager locale.

Lo strumento Secret `<UserSecretsId>` Manager richiede una proprietà nel file di progetto dell'app. Impostare il`{GUID}`valore della proprietà ( ) su qualsiasi GUID univoco:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

I segreti vengono creati come coppie nome-valore. I valori gerarchici (sezioni di configurazione) usano un `:` (due punti) come separatore nei nomi delle chiavi di configurazione ASP.NET Core.Hierarchical values (configuration sections) use a (colon) as a separator in ASP.NET Core [configuration](xref:fundamentals/configuration/index) key names.

Secret Manager viene utilizzato da una shell dei comandi aperta `{SECRET NAME}` alla radice `{SECRET VALUE}` del [contenuto](xref:fundamentals/index#content-root)del progetto, dove è il nome ed è il valore:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Eseguire i comandi seguenti in una shell dei comandi dalla radice del contenuto del progetto per impostare i segreti per l'app di esempio:Execute the following commands in a command shell from the [project's content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Quando questi segreti vengono archiviati in Archiviazione chiave di Azure nella sezione [Archiviazione segreta nell'ambiente](#secret-storage-in-the-production-environment-with-azure-key-vault) di produzione con l'insieme di credenziali delle chiavi di Azure, il `_dev` suffisso viene modificato in . `_prod` Il suffisso fornisce un segnale visivo nell'output dell'app che indica l'origine dei valori di configurazione.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Archiviazione segreta nell'ambiente di produzione con l'insieme di credenziali delle chiavi di AzureSecret storage in the Production environment with Azure Key Vault

Le istruzioni fornite dalla [Guida introduttiva: Impostare e recuperare un segreto dall'argomento dell'interfaccia](/azure/key-vault/quick-create-cli) di rete di Azure usano l'interfaccia della riga di comando di Azure sono riepilogate qui per la creazione di un insieme di credenziali delle chiavi di Azure e l'archiviazione dei segreti usati dall'app di esempio. Fare riferimento all'argomento per ulteriori dettagli.

1. Aprire la shell del cloud di Azure usando uno dei metodi seguenti nel portale di Azure:Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):

   * Selezionare **Prova** nell'angolo superiore destro di un blocco di codice. Usare la stringa di ricerca "Azure CLI" nella casella di testo.
   * Apri Cloud Shell nel browser con il pulsante **Avvia Cloud Shell.**
   * Selezionare il pulsante **Cloud Shell** nel menu nell'angolo in alto a destra del portale di Azure.

   Per altre informazioni, vedere [Interfaccia della riga di comando di Azure](/cli/azure/) e [Panoramica di Azure Cloud Shell.For](/azure/cloud-shell/overview)more information, see Azure CLI and Overview of Azure Cloud Shell .

1. Se non si è già autenticati, `az login` accedere con il comando.

1. Creare un gruppo di risorse `{RESOURCE GROUP NAME}` con il comando seguente, dove `{LOCATION}` è il nome del nuovo gruppo di risorse ed è l'area di Azure (data center):Create a resource group with the following command, where is the resource group name for the new resource group and is the Azure region (datacenter):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Creare un insieme di credenziali delle chiavi `{KEY VAULT NAME}` nel gruppo di risorse con `{LOCATION}` il comando seguente, dove è il nome del nuovo insieme di credenziali delle chiavi ed è l'area di Azure (data center):Create a key vault in the resource group with the following command, where is the name for the new key vault and is the Azure region (datacenter):

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Creare segreti nell'insieme di credenziali delle chiavi come coppie nome-valore.

   I nomi dei segreti dell'insieme di credenziali delle chiavi di Azure sono limitati a caratteri alfanumerici e trattini. I valori gerarchici `--` (sezioni di configurazione) utilizzano (due trattini) come separatore. I due, che in genere vengono utilizzati per delimitare una sezione da una sottochiave in [ASP.NET configurazione Core](xref:fundamentals/configuration/index), non sono consentiti nei nomi dei segreti dell'insieme di credenziali delle chiavi. Pertanto, due trattini vengono utilizzati e scambiati per i due punti quando i segreti vengono caricati nella configurazione dell'app.

   I segreti seguenti sono per l'uso con l'app di esempio. I valori `_prod` includono un suffisso `_dev` per distinguerli dai valori dei suffissi caricati nell'ambiente di sviluppo dai segreti utente. Sostituire `{KEY VAULT NAME}` con il nome dell'insieme di credenziali delle chiavi creato nel passaggio precedente:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Usare l'ID applicazione e il certificato X.509 per le app non ospitate da AzureUse Application ID and X.509 certificate for non-Azure-hosted apps

Configurare Azure AD, L'insieme di credenziali delle chiavi di Azure e l'app per l'uso di un ID applicazione di Azure Active Directory e di un certificato X.509 per l'autenticazione in un insieme di credenziali delle chiavi **quando l'app è ospitata all'esterno di Azure.Configure**Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault when the app is hosted outside of Azure. Per i dettagli, vedere l'articolo relativo alle [informazioni su chiavi, segreti e certificati](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Anche se l'uso di un ID applicazione e di un certificato X.509 è supportato per le app ospitate in Azure, è consigliabile usare [le identità gestite per le risorse](#use-managed-identities-for-azure-resources) di Azure quando si ospita un'app in Azure.Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using Managed identities for Azure resources when hosting an app in Azure. Le identità gestite non richiedono l'archiviazione di un certificato nell'app o nell'ambiente di sviluppo.

L'app di esempio usa un ID applicazione `#define` e un certificato X.509 `Certificate`quando l'istruzione all'inizio del file di *Program.cs* è impostata su .

1. Creare un certificato di archiviazione PKCS-12 (*.pfx*). Le opzioni per la creazione di certificati includono [MakeCert in Windows](/windows/desktop/seccrypto/makecert) e [OpenSSL](https://www.openssl.org/).
1. Installare il certificato nell'archivio certificati personale dell'utente corrente. Contrassegnare la chiave come esportabile è facoltativo. Si noti l'identificazione personale del certificato, che viene utilizzata più avanti in questo processo.
1. Esportare il certificato di archivio PKCS-12 (*.pfx*) come certificato con codifica DER (*.cer*).
1. Registrare l'app con Azure AD (**Registrazioni app**).
1. Caricare il certificato con codifica DER ( .cer ) in Azure AD:Upload the DER-encoded certificate (*.cer*) to Azure AD:
   1. Selezionare l'app in Azure AD.
   1. Passare a **Certificati & segreti**.
   1. Selezionare **Carica certificato** per caricare il certificato, che contiene la chiave pubblica. Un certificato *con estensione cer,* *pem*o *crt* è accettabile.
1. Archiviare il nome dell'insieme di credenziali delle chiavi, l'ID applicazione e l'identificazione personale del certificato nel file *appsettings.json* dell'app.
1. Passare a **Insiemi di credenziali delle chiavi** nel portale di Azure.Navigate to Key vaults in the Azure portal.
1. Selezionare l'insieme di credenziali delle chiavi creato nella sezione [Archiviazione segreta nell'ambiente](#secret-storage-in-the-production-environment-with-azure-key-vault) di produzione con l'insieme di credenziali delle chiavi di Azure.Select the key vault that you created in the Secret storage in the Production environment with Azure Key Vault section.
1. Selezionare **Criteri di accesso**.
1. Selezionare **Aggiungi criterio di accesso**.
1. Aprire **autorizzazioni segrete** e fornire all'app le autorizzazioni **Get** ed **List.**
1. Selezionare **Seleziona entità** e selezionare l'app registrata in base al nome. Fare clic sul pulsante **Seleziona**.
1. Selezionare **OK**.
1. Selezionare **Salva**.
1. Distribuire l'app.

L'app `Certificate` di esempio ottiene `IConfigurationRoot` i valori di configurazione da con lo stesso nome del nome del segreto:The sample app obtains its configuration values from with the same name as the secret name:

* Valori non gerarchici: `SecretName` il valore `config["SecretName"]`per viene ottenuto con .
* Valori gerarchici (sezioni): usare `:` la `GetSection` notazione (due punti) o il metodo di estensione. Utilizzare uno di questi approcci per ottenere il valore di configurazione:Use either of these approaches to obtain the configuration value:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Il certificato X.509 è gestito dal sistema operativo. L'app <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> chiama con i valori forniti dal file *appsettings.json:*

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Valori di esempio:

* Nome dell'insieme di credenziali delle chiavi:`contosovault`
* ID applicazione:`627e911e-43cc-61d4-992e-12db9c81b413`
* Identificazione personale certificato:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

Quando si esegue l'app, una pagina Web mostra i valori segreti caricati. Nell'ambiente di sviluppo, i `_dev` valori segreti vengono caricati con il suffisso. Nell'ambiente di produzione, i `_prod` valori vengono caricati con il suffisso.

## <a name="use-managed-identities-for-azure-resources"></a>Usare le identità gestite per le risorse di AzureUse Managed identities for Azure resources

**Un'app distribuita in Azure** può [sfruttare le identità gestite per le risorse](/azure/active-directory/managed-identities-azure-resources/overview)di Azure, che consente all'app di eseguire l'autenticazione con l'insieme di credenziali delle chiavi di Azure usando l'autenticazione di Azure AD senza credenziali (ID applicazione e segreto password/client) archiviate nell'app.

L'app di esempio usa le identità gestite per le risorse di Azure quando l'istruzione `#define` all'inizio del file *di Program.cs* è impostata su . `Managed`

Immettere il nome dell'insieme di credenziali nel file *appsettings.json* dell'app. L'app di esempio non richiede un ID applicazione e `Managed` una password (segreto client) quando è impostata sulla versione, pertanto è possibile ignorare tali voci di configurazione. L'app viene distribuita in Azure e Azure autentica l'app per accedere all'insieme di credenziali delle chiavi di Azure solo usando il nome dell'insieme di credenziali archiviato nel file *appsettings.json.*

Distribuire l'app di esempio nel servizio app di Azure.Deploy the sample app to Azure App Service.

Un'app distribuita nel servizio app di Azure viene registrata automaticamente con Azure AD quando viene creato il servizio. Ottenere l'ID oggetto dalla distribuzione per l'utilizzo nel comando seguente. L'ID oggetto viene visualizzato nel portale di Azure nel riquadro **Identità** del servizio app.

Usando l'interfaccia della riga di comando di `list` `get` Azure e l'ID oggetto dell'app, fornisci all'app e le autorizzazioni per accedere all'insieme di credenziali delle chiavi:Using Azure CLI and the app's Object ID, provide the app with and permissions to access the key vault:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Riavviare l'app usando l'interfaccia** della riga di comando di Azure, PowerShell o il portale di Azure.Restart the app using Azure CLI, PowerShell, or the Azure portal.

L'app di esempio:The sample app:

* Crea un'istanza `AzureServiceTokenProvider` della classe senza una stringa di connessione. Quando non viene fornita una stringa di connessione, il provider tenta di ottenere un token di accesso da identità gestite per le risorse di Azure.When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.
* Viene <xref:Microsoft.Azure.KeyVault.KeyVaultClient> creato un `AzureServiceTokenProvider` nuovo con il callback del token di istanza.
* L'istanza <xref:Microsoft.Azure.KeyVault.KeyVaultClient> viene utilizzata con <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> un'implementazione predefinita di che carica`--`tutti i valori`:`segreti e sostituisce i trattini doppi ( ) con i due punti ( ) nei nomi delle chiavi.

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Valore di esempio del nome dell'insieme di credenziali delle chiavi:Key vault name example value:`contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Quando si esegue l'app, una pagina Web mostra i valori segreti caricati. Nell'ambiente di sviluppo, `_dev` i valori segreti hanno il suffisso perché vengono forniti da segreti utente. Nell'ambiente di produzione, i `_prod` valori vengono caricati con il suffisso perché vengono forniti dall'insieme di credenziali delle chiavi di Azure.In the Production environment, the values load with the suffix because they're provided by Azure Key Vault.

Se viene `Access denied` visualizzato un errore, verificare che l'app sia registrata con Azure AD e che abbia fornito l'accesso all'insieme di credenziali delle chiavi. Verificare di aver riavviato il servizio in Azure.Confirm that you've restarted the service in Azure.

Per informazioni sull'uso del provider con un'identità gestita e una pipeline DevOps di Azure, vedere Creare una [connessione del servizio Azure Resource Manager a una macchina virtuale con un'identità](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)del servizio gestito.

## <a name="use-a-key-name-prefix"></a>Usare un prefisso del nome di chiaveUse a key name prefix

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>fornisce un overload che accetta <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>un'implementazione di , che consente di controllare il modo in cui i segreti dell'insieme di credenziali delle chiavi vengono convertiti in chiavi di configurazione. Ad esempio, è possibile implementare l'interfaccia per caricare i valori segreti in base a un valore di prefisso fornito all'avvio dell'app. Ciò consente, ad esempio, di caricare i segreti in base alla versione dell'app.

> [!WARNING]
> Non usare i prefissi sui segreti dell'insieme di credenziali delle chiavi per inserire segreti per più app nello stesso insieme di credenziali delle chiavi o per inserire segreti ambientali (ad esempio, segreti di *sviluppo* e *di produzione)* nello stesso insieme di credenziali. È consigliabile che app e ambienti di sviluppo/produzione diversi utilizzino insiemi di credenziali delle chiavi separati per isolare gli ambienti delle app per ottenere il massimo livello di sicurezza.

Nell'esempio seguente viene stabilito un segreto nell'insieme di credenziali delle chiavi `5000-AppSecret` (e l'utilizzo dello strumento Secret Manager per l'ambiente di sviluppo) per (i punti non sono consentiti nei nomi dei segreti dell'insieme di credenziali delle chiavi). Questo segreto rappresenta un segreto dell'app per la versione 5.0.0.0 dell'app. Per un'altra versione dell'app, 5.1.0.0, viene aggiunto un segreto all'insieme di credenziali delle chiavi (e utilizzando lo strumento Secret Manager) per `5100-AppSecret`. Ogni versione dell'app carica il valore `AppSecret`del segreto con controllo delle versioni nella configurazione come , rimuovendo la versione durante il caricamento del segreto.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>viene chiamato con <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>un:

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

L'implementazione <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> reagisce ai prefissi di versione dei segreti per caricare il segreto corretto nella configurazione:The implementation reacts to the version prefixes of secrets to load the proper secret into configuration:

* `Load`carica un segreto quando il nome inizia con il prefisso. Gli altri segreti non sono cariche.
* `GetKey`:
  * Rimuove il prefisso dal nome del segreto.
  * Sostituisce due trattini in qualsiasi `KeyDelimiter`nome con il , ovvero il delimitatore utilizzato nella configurazione (in genere due punti). L'insieme di credenziali delle chiavi di Azure non consente i due punti nei nomi segreti.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

Il `Load` metodo viene chiamato da un algoritmo del provider che scorre i segreti dell'insieme di credenziali per trovare quelli con il prefisso di versione. Quando viene trovato un `Load`prefisso di `GetKey` versione con , l'algoritmo utilizza il metodo per restituire il nome di configurazione del nome del segreto. Rimuove il prefisso di versione dal nome del segreto e restituisce il resto del nome del segreto per il caricamento nelle coppie nome-valore di configurazione dell'app.

Quando viene implementato questo approccio:When this approach is implemented:

1. Versione dell'app specificata nel file di progetto dell'app. Nell'esempio seguente, la versione dell'app è impostata `5.0.0.0`su:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Verificare `<UserSecretsId>` che una proprietà sia presente nel `{GUID}` file di progetto dell'app, dove è un GUID fornito dall'utente:Confirm that a property is present in the app's project file, where is a user-supplied GUID:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Salvare i seguenti segreti in locale con [lo strumento Secret Manager](xref:security/app-secrets):

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. I segreti vengono salvati nell'insieme di credenziali delle chiavi di Azure usando i comandi dell'interfaccia della riga di comando di Azure seguenti:Secrets are saved in Azure Key Vault using the following Azure CLI commands:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Quando l'app viene eseguita, vengono caricati i segreti dell'insieme di credenziali delle chiavi. La stringa `5000-AppSecret` segreta per viene confrontata con la versione dell'app specificata nel file di progetto dell'app (`5.0.0.0`).

1. La versione `5000` ( con il trattino) viene rimossa dal nome della chiave. In tutta l'app, `AppSecret` la lettura della configurazione con la chiave carica il valore segreto.

1. Se la versione dell'app viene modificata nel file di progetto `5.1.0.0` e l'app viene eseguita di nuovo, il valore segreto restituito si trova `5.1.0.0_secret_value_dev` nell'ambiente di sviluppo e `5.1.0.0_secret_value_prod` in Produzione.

> [!NOTE]
> È inoltre possibile <xref:Microsoft.Azure.KeyVault.KeyVaultClient> fornire <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>la propria implementazione a . Un client personalizzato consente di condividere una singola istanza del client nell'app.

## <a name="bind-an-array-to-a-class"></a>Associare una matrice a una classe

Il provider è in grado di leggere i valori di configurazione in una matrice per l'associazione a una matrice POCO.

Durante la lettura da un'origine di`:`configurazione che consente alle chiavi di contenere separatori di`:0:` `:1:`due &hellip; `:{n}:`punti ( ), viene utilizzato un segmento di chiave numerica per distinguere le chiavi che costituiscono una matrice ( , , ). Per ulteriori informazioni, vedere [Configurazione: associare una matrice a una classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Le chiavi dell'insieme di credenziali delle chiavi di Azure non possono usare i due punti come separatore. L'approccio descritto in questo argomento`--`usa i trattini doppi ( ) come separatore per i valori gerarchici (sezioni). Le chiavi di matrice vengono archiviate in Un insieme`--0--`di `--1--` &hellip; `--{n}--`chiavi di Azure con trattini doppi e segmenti numerici di chiavi ( , , , ).

Esaminare la configurazione del provider di registrazione [Serilog](https://serilog.net/) seguente fornita da un file JSON. Nella matrice sono definiti due `WriteTo` valori letterali oggetto che riflettono due *sink*Serilog , che descrivono le destinazioni per la registrazione dell'output:

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

La configurazione illustrata nel file JSON precedente viene archiviata`--`nell'insieme di credenziali delle chiavi di Azure usando la notazione a doppio trattino ( ) e i segmenti numerici:The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash ( ) notation and numeric segments:

| Chiave | valore |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Ricaricare i segretiReload secrets

I segreti `IConfigurationRoot.Reload()` vengono memorizzati nella cache fino a quando non viene chiamato. I segreti scaduti, disabilitati e aggiornati nell'insieme di credenziali delle chiavi non vengono rispettati dall'app fino all'esecuzione. `Reload`

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Segreti disabilitati e scaduti

Segreti disabilitati e <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>scaduti generano un file . Per impedire la generazione dell'app, fornire la configurazione utilizzando un provider di configurazione diverso o aggiornare il segreto disabilitato o scaduto.

## <a name="troubleshoot"></a>Risolvere problemi

Quando l'app non riesce a caricare la configurazione usando il provider, viene scritto un messaggio di errore [nell'infrastruttura di registrazione di ASP.NET Core](xref:fundamentals/logging/index). Le seguenti condizioni impediranno il caricamento della configurazione:

* L'app o il certificato non è configurato correttamente in Azure Active Directory.The app or certificate isn't configured correctly in Azure Active Directory.
* L'insieme di credenziali delle chiavi non esiste nell'insieme di credenziali delle chiavi di Azure.The key vault doesn't exist in Azure Key Vault.
* L'app non è autorizzata ad accedere all'insieme di credenziali delle chiavi.
* I criteri di accesso `Get` `List` non includono e le autorizzazioni.
* Nell'insieme di credenziali delle chiavi, i dati di configurazione (coppia nome-valore) sono denominati in modo errato, mancanti, disabilitati o scaduti.
* L'app ha il nome`KeyVaultName`dell'insieme di`AzureADApplicationId`credenziali delle chiavi (`AzureADCertThumbprint`), l'ID applicazione di Azure AD ( ) o l'identificazione personale del certificato di Azure AD ( ).
* La chiave di configurazione (nome) non è corretta nell'app per il valore che si sta tentando di caricare.
* Quando si aggiungono i criteri di accesso per l'app all'insieme di credenziali delle chiavi, il criterio è stato creato, ma il pulsante **Salva** non è stato selezionato nell'interfaccia utente dei criteri di **accesso.**

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Key Vault Documentation](/azure/key-vault/)
* [Come generare e trasferire chiavi HSM protette per l'insieme di credenziali delle chiavi di Azure](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient (classe)](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Guida introduttiva: Impostare e recuperare un segreto da Azure Key Vault tramite un'app Web .NET](/azure/key-vault/quick-create-net)
* [Esercitazione: Come usare Azure Key Vault con una macchina virtuale Windows di Azure in .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

