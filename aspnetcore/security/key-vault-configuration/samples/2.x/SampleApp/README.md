# <a name="key-vault-configuration-provider-sample-app"></a>App di esempio del provider di configurazione Key Vault

Questo esempio illustra l'uso del provider di configurazione Azure Key Vault.

L'esempio viene eseguito in una delle due modalità determinate dall' `#define` istruzione all'inizio del file *Program.cs* . Per istruzioni, vedere [direttive per il preprocessore nel codice di esempio](https://docs.microsoft.com/aspnet/core#preprocessor-directives-in-sample-code):

* `Certificate`: Illustra l'uso di un ID client Azure Key Vault e di un certificato X. 509 per accedere ai segreti archiviati nel Azure Key Vault. Questa versione dell'esempio può essere eseguita da qualsiasi posizione, distribuita nel servizio app Azure o in qualsiasi host in grado di servire un'app ASP.NET Core.
* `Managed`: Illustra come usare [identità del servizio gestita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) di Azure per autenticare l'app per Azure Key Vault con l'autenticazione Azure ad senza credenziali nel codice o nella configurazione dell'app. Un Azure AD ID client e un segreto non sono necessari per l'autenticazione dell'app con Azure Key Vault. Questo esempio deve essere distribuito nel servizio app Azure per esplorare l'identità gestita scearnio.

Per ulteriori informazioni, vedere [Azure Key Vault provider di configurazione](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration).
