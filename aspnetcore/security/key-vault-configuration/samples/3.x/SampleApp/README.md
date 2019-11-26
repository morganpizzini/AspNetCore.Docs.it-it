# <a name="key-vault-configuration-provider-sample-app"></a><span data-ttu-id="25431-101">App di esempio del provider di configurazione Key Vault</span><span class="sxs-lookup"><span data-stu-id="25431-101">Key Vault Configuration Provider Sample App</span></span>

<span data-ttu-id="25431-102">Questo esempio illustra l'uso del provider di configurazione Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="25431-102">This sample illustrates the use of the Azure Key Vault Configuration Provider.</span></span>

<span data-ttu-id="25431-103">L'esempio viene eseguito in una delle due modalità determinate dall'istruzione `#define` nella parte superiore del file *Program.cs* .</span><span class="sxs-lookup"><span data-stu-id="25431-103">The sample runs in one of two modes determined by the `#define` statement at the top of the *Program.cs* file.</span></span> <span data-ttu-id="25431-104">Per istruzioni, vedere [direttive per il preprocessore nel codice di esempio](https://docs.microsoft.com/aspnet/core#preprocessor-directives-in-sample-code):</span><span class="sxs-lookup"><span data-stu-id="25431-104">For instructions, see [Preprocessor directives in sample code](https://docs.microsoft.com/aspnet/core#preprocessor-directives-in-sample-code):</span></span>

* <span data-ttu-id="25431-105">`Certificate` &ndash; illustra l'uso di un ID client Azure Key Vault e di un certificato X. 509 per accedere ai segreti archiviati in Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="25431-105">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="25431-106">Questa versione dell'esempio può essere eseguita da qualsiasi posizione, distribuita nel servizio app Azure o in qualsiasi host in grado di servire un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="25431-106">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="25431-107">`Managed` &ndash; illustra come usare [identità del servizio gestita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) di Azure per autenticare l'app per Azure Key Vault con l'autenticazione Azure ad senza credenziali nel codice o nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="25431-107">`Managed` &ndash; Demonstrates how to use Azure's [Managed Service Identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials in the app's code or configuration.</span></span> <span data-ttu-id="25431-108">Un Azure AD ID client e un segreto non sono necessari per l'autenticazione dell'app con Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="25431-108">An Azure AD Client ID and Secret aren't required for the app to authenticate with Azure Key Vault.</span></span> <span data-ttu-id="25431-109">Questo esempio deve essere distribuito nel servizio app Azure per esplorare l'identità gestita scearnio.</span><span class="sxs-lookup"><span data-stu-id="25431-109">This sample must be deployed to Azure App Service to explore the Managed Identity scearnio.</span></span>

<span data-ttu-id="25431-110">Per ulteriori informazioni, vedere [Azure Key Vault provider di configurazione](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="25431-110">For more information, see [Azure Key Vault Configuration Provider](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration).</span></span>