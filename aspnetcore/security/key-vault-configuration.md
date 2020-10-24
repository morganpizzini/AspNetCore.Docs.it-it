---
title: Provider di configurazione di Azure Key Vault in ASP.NET Core
author: rick-anderson
description: Informazioni su come usare il provider di configurazione Azure Key Vault per configurare un'app usando coppie nome-valore caricate in fase di esecuzione.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli
ms.date: 02/07/2020
no-loc:
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
uid: security/key-vault-configuration
ms.openlocfilehash: e3adbe127f618b8851b3a83025b27c066947e8b4
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491574"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="8545f-103">Provider di configurazione di Azure Key Vault in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8545f-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="8545f-104">Di [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="8545f-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8545f-105">Questo documento illustra come usare il provider di configurazione [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) per caricare i valori di configurazione dell'app da Azure Key Vault segreti.</span><span class="sxs-lookup"><span data-stu-id="8545f-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="8545f-106">Azure Key Vault è un servizio basato sul cloud che aiuta a proteggere le chiavi crittografiche e i segreti usati da app e servizi.</span><span class="sxs-lookup"><span data-stu-id="8545f-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="8545f-107">Gli scenari comuni per l'uso di Azure Key Vault con le app ASP.NET Core includono:</span><span class="sxs-lookup"><span data-stu-id="8545f-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="8545f-108">Controllo dell'accesso ai dati di configurazione riservati.</span><span class="sxs-lookup"><span data-stu-id="8545f-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="8545f-109">Soddisfare i requisiti per i moduli di protezione hardware convalidati FIPS 140-2 Level 2 (HSM) quando si archiviano i dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="8545f-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="8545f-110">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8545f-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="8545f-111">Pacchetti</span><span class="sxs-lookup"><span data-stu-id="8545f-111">Packages</span></span>

<span data-ttu-id="8545f-112">Aggiungere un riferimento al pacchetto [Microsoft.Extensions.Configuration. ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) Pacchetto di AzureKeyVault.</span><span class="sxs-lookup"><span data-stu-id="8545f-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="8545f-113">App di esempio</span><span class="sxs-lookup"><span data-stu-id="8545f-113">Sample app</span></span>

<span data-ttu-id="8545f-114">L'app di esempio viene eseguita in una delle due modalità determinate dall' `#define` istruzione all'inizio del file *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="8545f-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="8545f-115">`Certificate`: Illustra l'uso di un ID client Azure Key Vault e di un certificato X. 509 per accedere ai segreti archiviati nel Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8545f-115">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="8545f-116">Questa versione dell'esempio può essere eseguita da qualsiasi posizione, distribuita nel servizio app Azure o in qualsiasi host in grado di servire un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8545f-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="8545f-117">`Managed`: Illustra come usare le [identità gestite per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/overview) per autenticare l'app Azure Key Vault con Azure ad autenticazione senza credenziali archiviate nel codice o nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-117">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="8545f-118">Quando si usano identità gestite per l'autenticazione, non è necessario un Azure AD ID applicazione e una password (segreto client).</span><span class="sxs-lookup"><span data-stu-id="8545f-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="8545f-119">La `Managed` versione dell'esempio deve essere distribuita in Azure.</span><span class="sxs-lookup"><span data-stu-id="8545f-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="8545f-120">Seguire le istruzioni riportate nella sezione [usare le identità gestite per le risorse di Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="8545f-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="8545f-121">Per ulteriori informazioni su come configurare un'app di esempio utilizzando le direttive per il preprocessore ( `#define` ), vedere <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="8545f-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="8545f-122">Archiviazione segreta nell'ambiente di sviluppo</span><span class="sxs-lookup"><span data-stu-id="8545f-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="8545f-123">Impostare i segreti in locale usando lo [strumento di gestione dei segreti](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="8545f-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="8545f-124">Quando l'app di esempio viene eseguita nel computer locale nell'ambiente di sviluppo, i segreti vengono caricati dall'archivio di gestione dei segreti locali.</span><span class="sxs-lookup"><span data-stu-id="8545f-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="8545f-125">Lo strumento di gestione dei segreti richiede una `<UserSecretsId>` proprietà nel file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="8545f-126">Impostare il valore della proprietà ( `{GUID}` ) su un GUID univoco:</span><span class="sxs-lookup"><span data-stu-id="8545f-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="8545f-127">I segreti vengono creati come coppie nome/valore.</span><span class="sxs-lookup"><span data-stu-id="8545f-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="8545f-128">I valori gerarchici (sezioni di configurazione) utilizzano un `:` (due punti) come separatore nei nomi delle chiavi di [configurazione ASP.NET Core](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="8545f-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="8545f-129">Il gestore del segreto viene usato da una shell dei comandi aperta alla [radice del contenuto](xref:fundamentals/index#content-root)del progetto, dove `{SECRET NAME}` è il nome e `{SECRET VALUE}` è il valore:</span><span class="sxs-lookup"><span data-stu-id="8545f-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="8545f-130">Eseguire i comandi seguenti in una shell dei comandi dalla radice del [contenuto](xref:fundamentals/index#content-root) del progetto per impostare i segreti per l'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="8545f-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="8545f-131">Quando questi segreti vengono archiviati in Azure Key Vault nell' [archiviazione segreta nell'ambiente di produzione con Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) sezione, il `_dev` suffisso viene modificato in `_prod` .</span><span class="sxs-lookup"><span data-stu-id="8545f-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="8545f-132">Il suffisso fornisce un segnale visivo nell'output dell'app che indica l'origine dei valori di configurazione.</span><span class="sxs-lookup"><span data-stu-id="8545f-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="8545f-133">Archiviazione segreta nell'ambiente di produzione con Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8545f-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="8545f-134">Le istruzioni fornite nell'argomento [avvio rapido: impostare e recuperare un segreto da Azure Key Vault usando l'interfaccia](/azure/key-vault/quick-create-cli) della riga di comando di Azure sono riepilogate qui per la creazione di un Azure Key Vault e l'archiviazione di segreti usati dall'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="8545f-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="8545f-135">Per ulteriori informazioni, vedere l'argomento.</span><span class="sxs-lookup"><span data-stu-id="8545f-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="8545f-136">Aprire Azure cloud shell usando uno dei metodi seguenti nel [portale di Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="8545f-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="8545f-137">Selezionare **Prova** nell'angolo superiore destro di un blocco di codice.</span><span class="sxs-lookup"><span data-stu-id="8545f-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="8545f-138">Usare la stringa di ricerca "interfaccia della riga di comando di Azure" nella casella di testo.</span><span class="sxs-lookup"><span data-stu-id="8545f-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="8545f-139">Aprire Cloud Shell nel browser con il pulsante **avvia cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="8545f-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="8545f-140">Selezionare il pulsante **Cloud Shell** nel menu nell'angolo in alto a destra del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="8545f-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="8545f-141">Per altre informazioni, vedere l'interfaccia della riga di comando di [Azure](/cli/azure/) e [Panoramica di Azure cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="8545f-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="8545f-142">Se non è già stato eseguito l'autenticazione, effettuare l'accesso con il `az login` comando.</span><span class="sxs-lookup"><span data-stu-id="8545f-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="8545f-143">Creare un gruppo di risorse con il comando seguente, dove `{RESOURCE GROUP NAME}` è il nome del gruppo di risorse per il nuovo gruppo di risorse e `{LOCATION}` è l'area di Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="8545f-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="8545f-144">Creare un insieme di credenziali delle chiavi nel gruppo di risorse con il comando seguente, dove è il nome del nuovo insieme di credenziali delle `{KEY VAULT NAME}` chiavi e `{LOCATION}` è l'area di Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="8545f-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="8545f-145">Creare segreti nell'insieme di credenziali delle chiavi come coppie nome/valore.</span><span class="sxs-lookup"><span data-stu-id="8545f-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="8545f-146">Azure Key Vault nomi di segreto sono limitati a caratteri alfanumerici e trattini.</span><span class="sxs-lookup"><span data-stu-id="8545f-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="8545f-147">I valori gerarchici (sezioni di configurazione) usano `--` (due trattini) come separatore.</span><span class="sxs-lookup"><span data-stu-id="8545f-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8545f-148">I due punti, che in genere vengono usati per delimitare una sezione da una sottochiave in [ASP.NET Core configurazione](xref:fundamentals/configuration/index), non sono consentiti nei nomi dei segreti di Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8545f-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="8545f-149">Pertanto, vengono usati due trattini e scambiati per i due punti quando i segreti vengono caricati nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="8545f-150">I segreti seguenti sono da usare con l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="8545f-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="8545f-151">I valori includono un `_prod` suffisso per distinguerli dai `_dev` valori dei suffissi caricati nell'ambiente di sviluppo da segreti utente.</span><span class="sxs-lookup"><span data-stu-id="8545f-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="8545f-152">Sostituire `{KEY VAULT NAME}` con il nome dell'insieme di credenziali delle chiavi creato nel passaggio precedente:</span><span class="sxs-lookup"><span data-stu-id="8545f-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="8545f-153">Usare l'ID applicazione e il certificato X. 509 per le app non ospitate in Azure</span><span class="sxs-lookup"><span data-stu-id="8545f-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="8545f-154">Configurare Azure AD, Azure Key Vault e l'app per usare un ID applicazione Azure Active Directory e un certificato X. 509 per l'autenticazione in un insieme di credenziali **delle chiavi quando l'app è ospitata all'esterno di Azure**.</span><span class="sxs-lookup"><span data-stu-id="8545f-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="8545f-155">Per i dettagli, vedere l'articolo relativo alle [informazioni su chiavi, segreti e certificati](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="8545f-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="8545f-156">Sebbene l'uso di un ID applicazione e di un certificato X. 509 sia supportato per le app ospitate in Azure, è consigliabile usare [identità gestite per le risorse di Azure](#use-managed-identities-for-azure-resources) quando si ospita un'app in Azure.</span><span class="sxs-lookup"><span data-stu-id="8545f-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="8545f-157">Le identità gestite non richiedono l'archiviazione di un certificato nell'app o nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="8545f-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="8545f-158">L'app di esempio usa un ID applicazione e un certificato X. 509 quando l' `#define` istruzione all'inizio del file *Program.cs* è impostata su `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="8545f-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="8545f-159">Creare un certificato di archivio PKCS # 12 (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="8545f-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="8545f-160">Le opzioni per la creazione di certificati includono [Makecert in Windows](/windows/desktop/seccrypto/makecert) e [openssl](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="8545f-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="8545f-161">Installare il certificato nell'archivio certificati personale dell'utente corrente.</span><span class="sxs-lookup"><span data-stu-id="8545f-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="8545f-162">Contrassegnare la chiave come esportabile è facoltativa.</span><span class="sxs-lookup"><span data-stu-id="8545f-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="8545f-163">Prendere nota dell'identificazione personale del certificato, che verrà usato più avanti in questo processo.</span><span class="sxs-lookup"><span data-stu-id="8545f-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="8545f-164">Esportare il certificato di archiviazione PKCS # 12 (*. pfx*) come certificato con codifica der (*. cer*).</span><span class="sxs-lookup"><span data-stu-id="8545f-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="8545f-165">Registrare l'app con Azure AD (**registrazioni app**).</span><span class="sxs-lookup"><span data-stu-id="8545f-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="8545f-166">Caricare il certificato con codifica DER (*. cer*) in Azure ad:</span><span class="sxs-lookup"><span data-stu-id="8545f-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="8545f-167">Selezionare l'app in Azure AD.</span><span class="sxs-lookup"><span data-stu-id="8545f-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="8545f-168">Passare a **certificati & segreti**.</span><span class="sxs-lookup"><span data-stu-id="8545f-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="8545f-169">Selezionare **Carica certificato** per caricare il certificato, che contiene la chiave pubblica.</span><span class="sxs-lookup"><span data-stu-id="8545f-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="8545f-170">Un certificato con *estensione cer*, *PEM*o *CRT* è accettabile.</span><span class="sxs-lookup"><span data-stu-id="8545f-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="8545f-171">Archiviare il nome dell'insieme di credenziali delle chiavi, l'ID applicazione e l'identificazione personale del certificato nel file *appsettings.js* dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="8545f-172">Passare a insiemi di credenziali delle **chiavi** nella portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="8545f-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="8545f-173">Selezionare l'insieme di credenziali delle chiavi creato nell' [Archivio Secret nell'ambiente di produzione con Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) sezione.</span><span class="sxs-lookup"><span data-stu-id="8545f-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="8545f-174">Selezionare **Criteri di accesso**.</span><span class="sxs-lookup"><span data-stu-id="8545f-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="8545f-175">Selezionare **Aggiungi criterio di accesso**.</span><span class="sxs-lookup"><span data-stu-id="8545f-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="8545f-176">Aprire **autorizzazioni segrete** e fornire all'app le autorizzazioni **Get** ed **List** .</span><span class="sxs-lookup"><span data-stu-id="8545f-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="8545f-177">Selezionare **Seleziona entità** e selezionare l'app registrata in base al nome.</span><span class="sxs-lookup"><span data-stu-id="8545f-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="8545f-178">Fare clic sul pulsante **Seleziona**.</span><span class="sxs-lookup"><span data-stu-id="8545f-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="8545f-179">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="8545f-179">Select **OK**.</span></span>
1. <span data-ttu-id="8545f-180">Selezionare **Salva**.</span><span class="sxs-lookup"><span data-stu-id="8545f-180">Select **Save**.</span></span>
1. <span data-ttu-id="8545f-181">Distribuire l'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-181">Deploy the app.</span></span>

<span data-ttu-id="8545f-182">L' `Certificate` app di esempio ottiene i valori di configurazione da `IConfigurationRoot` con lo stesso nome del nome del segreto:</span><span class="sxs-lookup"><span data-stu-id="8545f-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="8545f-183">Valori non gerarchici: il valore per `SecretName` viene ottenuto con `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="8545f-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="8545f-184">Valori gerarchici (sezioni): `:` notazione use (due punti) o `GetSection` metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="8545f-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="8545f-185">Per ottenere il valore di configurazione, usare uno di questi approcci:</span><span class="sxs-lookup"><span data-stu-id="8545f-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="8545f-186">Il certificato X. 509 è gestito dal sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="8545f-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="8545f-187">L'app chiama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> con i valori forniti dal *appsettings.jssul* file:</span><span class="sxs-lookup"><span data-stu-id="8545f-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="8545f-188">Valori di esempio</span><span class="sxs-lookup"><span data-stu-id="8545f-188">Example values:</span></span>

* <span data-ttu-id="8545f-189">Nome dell'insieme di credenziali delle chiavi: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="8545f-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="8545f-190">ID applicazione: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="8545f-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="8545f-191">Identificazione personale del certificato: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="8545f-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="8545f-192">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="8545f-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="8545f-193">Quando si esegue l'app, in una pagina Web vengono visualizzati i valori dei segreti caricati.</span><span class="sxs-lookup"><span data-stu-id="8545f-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="8545f-194">Nell'ambiente di sviluppo, i valori Secret vengono caricati con il `_dev` suffisso.</span><span class="sxs-lookup"><span data-stu-id="8545f-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="8545f-195">Nell'ambiente di produzione, i valori vengono caricati con il `_prod` suffisso.</span><span class="sxs-lookup"><span data-stu-id="8545f-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="8545f-196">Usare identità gestite per le risorse di Azure</span><span class="sxs-lookup"><span data-stu-id="8545f-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="8545f-197">**Un'app distribuita in Azure** può trarre vantaggio dalle [identità gestite per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/overview), che consente all'app di eseguire l'autenticazione con Azure Key Vault usando Azure ad autenticazione senza credenziali (ID applicazione e password/segreto client) archiviate nell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="8545f-198">L'app di esempio usa le identità gestite per le risorse di Azure quando l' `#define` istruzione all'inizio del file *Program.cs* è impostata su `Managed` .</span><span class="sxs-lookup"><span data-stu-id="8545f-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="8545f-199">Immettere il nome dell'insieme di credenziali nel *appsettings.js* del file dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="8545f-200">L'app di esempio non richiede un ID applicazione e una password (segreto client) quando è impostata sulla `Managed` versione, in modo che sia possibile ignorare tali voci di configurazione.</span><span class="sxs-lookup"><span data-stu-id="8545f-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="8545f-201">L'app viene distribuita in Azure e Azure autentica l'app per accedere Azure Key Vault solo usando il nome dell'insieme di credenziali archiviato nel *appsettings.jssu* file.</span><span class="sxs-lookup"><span data-stu-id="8545f-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="8545f-202">Distribuire l'app di esempio nel servizio app Azure.</span><span class="sxs-lookup"><span data-stu-id="8545f-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="8545f-203">Un'app distribuita nel servizio app Azure viene registrata automaticamente con Azure AD quando viene creato il servizio.</span><span class="sxs-lookup"><span data-stu-id="8545f-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="8545f-204">Ottenere l'ID oggetto dalla distribuzione per utilizzarlo nel comando seguente.</span><span class="sxs-lookup"><span data-stu-id="8545f-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="8545f-205">L'ID oggetto viene visualizzato nella portale di Azure nel **Identity** Pannello del servizio app.</span><span class="sxs-lookup"><span data-stu-id="8545f-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="8545f-206">Usando l'interfaccia della riga di comando di Azure e l'ID oggetto dell'app, fornire all'app le `list` `get` autorizzazioni e per accedere all'insieme di credenziali delle chiavi:</span><span class="sxs-lookup"><span data-stu-id="8545f-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="8545f-207">**Riavviare l'app** usando l'interfaccia della riga di comando di Azure, PowerShell o la portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="8545f-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="8545f-208">App di esempio:</span><span class="sxs-lookup"><span data-stu-id="8545f-208">The sample app:</span></span>

* <span data-ttu-id="8545f-209">Crea un'istanza della `AzureServiceTokenProvider` classe senza una stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="8545f-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="8545f-210">Quando non viene specificata una stringa di connessione, il provider tenta di ottenere un token di accesso dalle identità gestite per le risorse di Azure.</span><span class="sxs-lookup"><span data-stu-id="8545f-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="8545f-211">Viene creato un nuovo oggetto <xref:Microsoft.Azure.KeyVault.KeyVaultClient> con il `AzureServiceTokenProvider` callback del token dell'istanza.</span><span class="sxs-lookup"><span data-stu-id="8545f-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="8545f-212">L' <xref:Microsoft.Azure.KeyVault.KeyVaultClient> istanza viene utilizzata con un'implementazione predefinita di <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> che carica tutti i valori del segreto e sostituisce i doppi trattini ( `--` ) con i due punti ( `:` ) nei nomi delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="8545f-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="8545f-213">Valore di esempio del nome dell'insieme di credenziali delle chiavi: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="8545f-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="8545f-214">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="8545f-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="8545f-215">Quando si esegue l'app, in una pagina Web vengono visualizzati i valori dei segreti caricati.</span><span class="sxs-lookup"><span data-stu-id="8545f-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="8545f-216">Nell'ambiente di sviluppo i valori Secret hanno il `_dev` suffisso perché sono forniti da segreti utente.</span><span class="sxs-lookup"><span data-stu-id="8545f-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="8545f-217">Nell'ambiente di produzione, i valori vengono caricati con il `_prod` suffisso perché sono forniti da Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8545f-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="8545f-218">Se viene visualizzato un `Access denied` errore, verificare che l'app sia registrata con Azure ad e che l'accesso sia stato fornito all'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="8545f-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="8545f-219">Confermare di aver riavviato il servizio in Azure.</span><span class="sxs-lookup"><span data-stu-id="8545f-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="8545f-220">Per informazioni sull'uso del provider con un'identità gestita e una pipeline di Azure DevOps, vedere [creare una connessione del servizio Azure Resource Manager a una macchina virtuale con un'identità del servizio gestito](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="8545f-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="8545f-221">Opzioni di configurazione</span><span class="sxs-lookup"><span data-stu-id="8545f-221">Configuration options</span></span>

<span data-ttu-id="8545f-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> può accettare un <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> :</span><span class="sxs-lookup"><span data-stu-id="8545f-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="8545f-223">Proprietà</span><span class="sxs-lookup"><span data-stu-id="8545f-223">Property</span></span>         | <span data-ttu-id="8545f-224">Descrizione</span><span class="sxs-lookup"><span data-stu-id="8545f-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="8545f-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> da utilizzare per il recupero di valori.</span><span class="sxs-lookup"><span data-stu-id="8545f-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="8545f-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> istanza di utilizzata per controllare il caricamento del segreto.</span><span class="sxs-lookup"><span data-stu-id="8545f-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="8545f-227">`Timespan` per attendere tra i tentativi di polling dell'insieme di credenziali delle chiavi per le modifiche.</span><span class="sxs-lookup"><span data-stu-id="8545f-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="8545f-228">Il valore predefinito è `null` (la configurazione non viene ricaricata).</span><span class="sxs-lookup"><span data-stu-id="8545f-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="8545f-229">URI dell'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="8545f-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="8545f-230">Usa prefisso nome chiave</span><span class="sxs-lookup"><span data-stu-id="8545f-230">Use a key name prefix</span></span>

<span data-ttu-id="8545f-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> fornisce un overload che accetta un'implementazione di <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , che consente di controllare la modalità di conversione dei segreti dell'insieme di credenziali delle chiavi in chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="8545f-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="8545f-232">Ad esempio, è possibile implementare l'interfaccia per caricare i valori dei segreti in base a un valore di prefisso fornito all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="8545f-233">Questo consente, ad esempio, di caricare i segreti in base alla versione dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="8545f-234">Non usare i prefissi sui segreti dell'insieme di credenziali delle chiavi per collocare i segreti per più app nello stesso insieme di credenziali delle chiavi o per collocare i segreti ambientali (ad esempio, *sviluppo* rispetto ai segreti di *produzione* ) nello stesso insieme di credenziali.</span><span class="sxs-lookup"><span data-stu-id="8545f-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="8545f-235">È consigliabile che app e ambienti di sviluppo/produzione diversi usino insiemi di credenziali delle chiavi distinti per isolare gli ambienti app per il massimo livello di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="8545f-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="8545f-236">Nell'esempio seguente viene stabilito un segreto nell'insieme di credenziali delle chiavi (e usando lo strumento di gestione dei segreti per l'ambiente di sviluppo) per `5000-AppSecret` (i periodi non sono consentiti nei nomi dei segreti dell'insieme di credenziali delle chiavi).</span><span class="sxs-lookup"><span data-stu-id="8545f-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="8545f-237">Questo segreto rappresenta un segreto app per la versione 5.0.0.0 dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="8545f-238">Per un'altra versione dell'app, 5.1.0.0, viene aggiunto un segreto all'insieme di credenziali delle chiavi (e usando lo strumento di gestione dei segreti) per `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="8545f-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="8545f-239">Ogni versione dell'app carica il valore del segreto con versione nella configurazione come `AppSecret` , rimuovendo la versione durante il caricamento del segreto.</span><span class="sxs-lookup"><span data-stu-id="8545f-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="8545f-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> viene chiamato con un oggetto personalizzato <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="8545f-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="8545f-241">L' <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementazione reagisce ai prefissi di versione dei segreti per caricare il segreto appropriato nella configurazione:</span><span class="sxs-lookup"><span data-stu-id="8545f-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="8545f-242">`Load` carica un segreto quando il nome inizia con il prefisso.</span><span class="sxs-lookup"><span data-stu-id="8545f-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="8545f-243">Non vengono caricati altri segreti.</span><span class="sxs-lookup"><span data-stu-id="8545f-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="8545f-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="8545f-244">`GetKey`:</span></span>
  * <span data-ttu-id="8545f-245">Rimuove il prefisso dal nome del segreto.</span><span class="sxs-lookup"><span data-stu-id="8545f-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="8545f-246">Sostituisce due trattini in qualsiasi nome con `KeyDelimiter` , che è il delimitatore usato nella configurazione (in genere i due punti).</span><span class="sxs-lookup"><span data-stu-id="8545f-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="8545f-247">Azure Key Vault non consente i due punti nei nomi dei segreti.</span><span class="sxs-lookup"><span data-stu-id="8545f-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="8545f-248">Il `Load` metodo viene chiamato da un algoritmo del provider che scorre i segreti dell'insieme di credenziali per trovare quelli con il prefisso della versione.</span><span class="sxs-lookup"><span data-stu-id="8545f-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="8545f-249">Quando viene trovato un prefisso di versione con `Load` , l'algoritmo usa il `GetKey` metodo per restituire il nome della configurazione del nome del segreto.</span><span class="sxs-lookup"><span data-stu-id="8545f-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="8545f-250">Rimuove il prefisso della versione dal nome del segreto e restituisce il resto del nome del segreto per il caricamento nelle coppie nome-valore della configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="8545f-251">Quando viene implementato questo approccio:</span><span class="sxs-lookup"><span data-stu-id="8545f-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="8545f-252">La versione dell'app specificata nel file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="8545f-253">Nell'esempio seguente la versione dell'app è impostata su `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="8545f-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="8545f-254">Verificare che `<UserSecretsId>` sia presente una proprietà nel file di progetto dell'app, dove `{GUID}` è un GUID fornito dall'utente:</span><span class="sxs-lookup"><span data-stu-id="8545f-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="8545f-255">Salvare i segreti seguenti localmente con lo [strumento di gestione dei segreti](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="8545f-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="8545f-256">I segreti vengono salvati in Azure Key Vault usando i comandi dell'interfaccia della riga di comando di Azure seguenti:</span><span class="sxs-lookup"><span data-stu-id="8545f-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="8545f-257">Quando l'app viene eseguita, vengono caricati i segreti dell'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="8545f-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="8545f-258">La stringa segreta per `5000-AppSecret` viene confrontata con la versione dell'app specificata nel file di progetto dell'app ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="8545f-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="8545f-259">La versione `5000` (con il trattino) viene rimossa dal nome della chiave.</span><span class="sxs-lookup"><span data-stu-id="8545f-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="8545f-260">In tutta l'app, la lettura della configurazione con la chiave `AppSecret` carica il valore del segreto.</span><span class="sxs-lookup"><span data-stu-id="8545f-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="8545f-261">Se la versione dell'app viene modificata nel file di progetto in `5.1.0.0` e l'app viene nuovamente eseguita, il valore del segreto restituito è `5.1.0.0_secret_value_dev` nell'ambiente di sviluppo e `5.1.0.0_secret_value_prod` in produzione.</span><span class="sxs-lookup"><span data-stu-id="8545f-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="8545f-262">È anche possibile fornire un' <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementazione personalizzata a <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="8545f-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="8545f-263">Un client personalizzato consente la condivisione di una singola istanza del client nell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8545f-264">Associare una matrice a una classe</span><span class="sxs-lookup"><span data-stu-id="8545f-264">Bind an array to a class</span></span>

<span data-ttu-id="8545f-265">Il provider è in grado di leggere i valori di configurazione in una matrice per l'associazione a una matrice POCO.</span><span class="sxs-lookup"><span data-stu-id="8545f-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="8545f-266">Quando si legge da un'origine di configurazione che consente alle chiavi di contenere separatori di due punti ( `:` ), viene usato un segmento di chiave numerico per distinguere le chiavi che costituiscono una matrice ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="8545f-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="8545f-267">Per altre informazioni, vedere [Configuration: associare una matrice a una classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="8545f-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="8545f-268">Azure Key Vault chiavi non possono utilizzare i due punti come separatore.</span><span class="sxs-lookup"><span data-stu-id="8545f-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="8545f-269">L'approccio descritto in questo argomento USA trattini doppi ( `--` ) come separatore per i valori gerarchici (sezioni).</span><span class="sxs-lookup"><span data-stu-id="8545f-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="8545f-270">Le chiavi di matrice vengono archiviate in Azure Key Vault con trattini doppi e segmenti di chiave numerica ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="8545f-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="8545f-271">Esaminare la seguente configurazione del provider di registrazione [Serilog](https://serilog.net/) fornita da un file JSON.</span><span class="sxs-lookup"><span data-stu-id="8545f-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="8545f-272">Nella matrice sono definiti due valori letterali di oggetto `WriteTo` che riflettono due *sink*Serilog, che descrivono le destinazioni per la registrazione dell'output:</span><span class="sxs-lookup"><span data-stu-id="8545f-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="8545f-273">La configurazione mostrata nel file JSON precedente viene archiviata in Azure Key Vault usando la notazione a doppio trattino ( `--` ) e i segmenti numerici:</span><span class="sxs-lookup"><span data-stu-id="8545f-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="8545f-274">Chiave</span><span class="sxs-lookup"><span data-stu-id="8545f-274">Key</span></span> | <span data-ttu-id="8545f-275">valore</span><span class="sxs-lookup"><span data-stu-id="8545f-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="8545f-276">Ricarica segreti</span><span class="sxs-lookup"><span data-stu-id="8545f-276">Reload secrets</span></span>

<span data-ttu-id="8545f-277">I segreti vengono memorizzati nella cache finché non `IConfigurationRoot.Reload()` viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="8545f-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="8545f-278">I segreti scaduti, disabilitati e aggiornati nell'insieme di credenziali delle chiavi non vengono rispettati dall'app fino a quando non `Reload` viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="8545f-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="8545f-279">Segreti disabilitati e scaduti</span><span class="sxs-lookup"><span data-stu-id="8545f-279">Disabled and expired secrets</span></span>

<span data-ttu-id="8545f-280">I segreti disabilitati e scaduti generano un' <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="8545f-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="8545f-281">Per impedire che l'app venga generata, fornire la configurazione usando un provider di configurazione diverso o aggiornare il segreto disabilitato o scaduto.</span><span class="sxs-lookup"><span data-stu-id="8545f-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="8545f-282">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="8545f-282">Troubleshoot</span></span>

<span data-ttu-id="8545f-283">Quando l'app non riesce a caricare la configurazione usando il provider, viene scritto un messaggio di errore nell' [infrastruttura di registrazione ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="8545f-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="8545f-284">Le condizioni seguenti impediranno il caricamento della configurazione:</span><span class="sxs-lookup"><span data-stu-id="8545f-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="8545f-285">L'app o il certificato non è configurato correttamente in Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="8545f-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="8545f-286">L'insieme di credenziali delle chiavi non esiste in Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8545f-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="8545f-287">L'app non è autorizzata ad accedere all'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="8545f-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="8545f-288">Il criterio di accesso non include le `Get` `List` autorizzazioni e.</span><span class="sxs-lookup"><span data-stu-id="8545f-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="8545f-289">Nell'insieme di credenziali delle chiavi i dati di configurazione (coppia nome-valore) sono denominati, mancanti, disabilitati o scaduti in modo errato.</span><span class="sxs-lookup"><span data-stu-id="8545f-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="8545f-290">L'app presenta il nome dell'insieme di credenziali delle chiavi errato ( `KeyVaultName` ), Azure ad ID applicazione ( `AzureADApplicationId` ) o l'identificazione personale del certificato Azure ad ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="8545f-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="8545f-291">La chiave di configurazione (nome) non è corretta nell'app per il valore che si sta provando a caricare.</span><span class="sxs-lookup"><span data-stu-id="8545f-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="8545f-292">Quando si aggiungono i criteri di accesso per l'app all'insieme di credenziali delle chiavi, il criterio è stato creato, ma il pulsante **Salva** non è stato selezionato nell'interfaccia utente dei **criteri di accesso** .</span><span class="sxs-lookup"><span data-stu-id="8545f-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8545f-293">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8545f-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="8545f-294">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="8545f-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="8545f-295">Microsoft Azure: Key Vault documentazione</span><span class="sxs-lookup"><span data-stu-id="8545f-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="8545f-296">Come generare e trasferire chiavi HSM protette per Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8545f-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="8545f-297">Classe KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="8545f-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="8545f-298">Guida introduttiva: Impostare e recuperare un segreto da Azure Key Vault tramite un'app Web .NET</span><span class="sxs-lookup"><span data-stu-id="8545f-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="8545f-299">Esercitazione: Come usare Azure Key Vault con una macchina virtuale Windows di Azure in .NET</span><span class="sxs-lookup"><span data-stu-id="8545f-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8545f-300">Questo documento illustra come usare il provider di configurazione [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) per caricare i valori di configurazione dell'app da Azure Key Vault segreti.</span><span class="sxs-lookup"><span data-stu-id="8545f-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="8545f-301">Azure Key Vault è un servizio basato sul cloud che aiuta a proteggere le chiavi crittografiche e i segreti usati da app e servizi.</span><span class="sxs-lookup"><span data-stu-id="8545f-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="8545f-302">Gli scenari comuni per l'uso di Azure Key Vault con le app ASP.NET Core includono:</span><span class="sxs-lookup"><span data-stu-id="8545f-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="8545f-303">Controllo dell'accesso ai dati di configurazione riservati.</span><span class="sxs-lookup"><span data-stu-id="8545f-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="8545f-304">Soddisfare i requisiti per i moduli di protezione hardware convalidati FIPS 140-2 Level 2 (HSM) quando si archiviano i dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="8545f-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="8545f-305">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8545f-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="8545f-306">Pacchetti</span><span class="sxs-lookup"><span data-stu-id="8545f-306">Packages</span></span>

<span data-ttu-id="8545f-307">Aggiungere un riferimento al pacchetto [Microsoft.Extensions.Configuration. ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) Pacchetto di AzureKeyVault.</span><span class="sxs-lookup"><span data-stu-id="8545f-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="8545f-308">App di esempio</span><span class="sxs-lookup"><span data-stu-id="8545f-308">Sample app</span></span>

<span data-ttu-id="8545f-309">L'app di esempio viene eseguita in una delle due modalità determinate dall' `#define` istruzione all'inizio del file *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="8545f-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="8545f-310">`Certificate`: Illustra l'uso di un ID client Azure Key Vault e di un certificato X. 509 per accedere ai segreti archiviati nel Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8545f-310">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="8545f-311">Questa versione dell'esempio può essere eseguita da qualsiasi posizione, distribuita nel servizio app Azure o in qualsiasi host in grado di servire un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8545f-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="8545f-312">`Managed`: Illustra come usare le [identità gestite per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/overview) per autenticare l'app Azure Key Vault con Azure ad autenticazione senza credenziali archiviate nel codice o nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-312">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="8545f-313">Quando si usano identità gestite per l'autenticazione, non è necessario un Azure AD ID applicazione e una password (segreto client).</span><span class="sxs-lookup"><span data-stu-id="8545f-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="8545f-314">La `Managed` versione dell'esempio deve essere distribuita in Azure.</span><span class="sxs-lookup"><span data-stu-id="8545f-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="8545f-315">Seguire le istruzioni riportate nella sezione [usare le identità gestite per le risorse di Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="8545f-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="8545f-316">Per ulteriori informazioni su come configurare un'app di esempio utilizzando le direttive per il preprocessore ( `#define` ), vedere <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="8545f-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="8545f-317">Archiviazione segreta nell'ambiente di sviluppo</span><span class="sxs-lookup"><span data-stu-id="8545f-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="8545f-318">Impostare i segreti in locale usando lo [strumento di gestione dei segreti](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="8545f-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="8545f-319">Quando l'app di esempio viene eseguita nel computer locale nell'ambiente di sviluppo, i segreti vengono caricati dall'archivio di gestione dei segreti locali.</span><span class="sxs-lookup"><span data-stu-id="8545f-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="8545f-320">Lo strumento di gestione dei segreti richiede una `<UserSecretsId>` proprietà nel file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="8545f-321">Impostare il valore della proprietà ( `{GUID}` ) su un GUID univoco:</span><span class="sxs-lookup"><span data-stu-id="8545f-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="8545f-322">I segreti vengono creati come coppie nome/valore.</span><span class="sxs-lookup"><span data-stu-id="8545f-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="8545f-323">I valori gerarchici (sezioni di configurazione) utilizzano un `:` (due punti) come separatore nei nomi delle chiavi di [configurazione ASP.NET Core](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="8545f-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="8545f-324">Il gestore del segreto viene usato da una shell dei comandi aperta alla [radice del contenuto](xref:fundamentals/index#content-root)del progetto, dove `{SECRET NAME}` è il nome e `{SECRET VALUE}` è il valore:</span><span class="sxs-lookup"><span data-stu-id="8545f-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="8545f-325">Eseguire i comandi seguenti in una shell dei comandi dalla radice del [contenuto](xref:fundamentals/index#content-root) del progetto per impostare i segreti per l'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="8545f-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="8545f-326">Quando questi segreti vengono archiviati in Azure Key Vault nell' [archiviazione segreta nell'ambiente di produzione con Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) sezione, il `_dev` suffisso viene modificato in `_prod` .</span><span class="sxs-lookup"><span data-stu-id="8545f-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="8545f-327">Il suffisso fornisce un segnale visivo nell'output dell'app che indica l'origine dei valori di configurazione.</span><span class="sxs-lookup"><span data-stu-id="8545f-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="8545f-328">Archiviazione segreta nell'ambiente di produzione con Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8545f-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="8545f-329">Le istruzioni fornite nell'argomento [avvio rapido: impostare e recuperare un segreto da Azure Key Vault usando l'interfaccia](/azure/key-vault/quick-create-cli) della riga di comando di Azure sono riepilogate qui per la creazione di un Azure Key Vault e l'archiviazione di segreti usati dall'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="8545f-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="8545f-330">Per ulteriori informazioni, vedere l'argomento.</span><span class="sxs-lookup"><span data-stu-id="8545f-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="8545f-331">Aprire Azure cloud shell usando uno dei metodi seguenti nel [portale di Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="8545f-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="8545f-332">Selezionare **Prova** nell'angolo superiore destro di un blocco di codice.</span><span class="sxs-lookup"><span data-stu-id="8545f-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="8545f-333">Usare la stringa di ricerca "interfaccia della riga di comando di Azure" nella casella di testo.</span><span class="sxs-lookup"><span data-stu-id="8545f-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="8545f-334">Aprire Cloud Shell nel browser con il pulsante **avvia cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="8545f-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="8545f-335">Selezionare il pulsante **Cloud Shell** nel menu nell'angolo in alto a destra del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="8545f-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="8545f-336">Per altre informazioni, vedere l'interfaccia della riga di comando di [Azure](/cli/azure/) e [Panoramica di Azure cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="8545f-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="8545f-337">Se non è già stato eseguito l'autenticazione, effettuare l'accesso con il `az login` comando.</span><span class="sxs-lookup"><span data-stu-id="8545f-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="8545f-338">Creare un gruppo di risorse con il comando seguente, dove `{RESOURCE GROUP NAME}` è il nome del gruppo di risorse per il nuovo gruppo di risorse e `{LOCATION}` è l'area di Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="8545f-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="8545f-339">Creare un insieme di credenziali delle chiavi nel gruppo di risorse con il comando seguente, dove è il nome del nuovo insieme di credenziali delle `{KEY VAULT NAME}` chiavi e `{LOCATION}` è l'area di Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="8545f-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="8545f-340">Creare segreti nell'insieme di credenziali delle chiavi come coppie nome/valore.</span><span class="sxs-lookup"><span data-stu-id="8545f-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="8545f-341">Azure Key Vault nomi di segreto sono limitati a caratteri alfanumerici e trattini.</span><span class="sxs-lookup"><span data-stu-id="8545f-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="8545f-342">I valori gerarchici (sezioni di configurazione) usano `--` (due trattini) come separatore.</span><span class="sxs-lookup"><span data-stu-id="8545f-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8545f-343">I due punti, che in genere vengono usati per delimitare una sezione da una sottochiave in [ASP.NET Core configurazione](xref:fundamentals/configuration/index), non sono consentiti nei nomi dei segreti di Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8545f-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="8545f-344">Pertanto, vengono usati due trattini e scambiati per i due punti quando i segreti vengono caricati nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="8545f-345">I segreti seguenti sono da usare con l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="8545f-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="8545f-346">I valori includono un `_prod` suffisso per distinguerli dai `_dev` valori dei suffissi caricati nell'ambiente di sviluppo da segreti utente.</span><span class="sxs-lookup"><span data-stu-id="8545f-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="8545f-347">Sostituire `{KEY VAULT NAME}` con il nome dell'insieme di credenziali delle chiavi creato nel passaggio precedente:</span><span class="sxs-lookup"><span data-stu-id="8545f-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="8545f-348">Usare l'ID applicazione e il certificato X. 509 per le app non ospitate in Azure</span><span class="sxs-lookup"><span data-stu-id="8545f-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="8545f-349">Configurare Azure AD, Azure Key Vault e l'app per usare un ID applicazione Azure Active Directory e un certificato X. 509 per l'autenticazione in un insieme di credenziali **delle chiavi quando l'app è ospitata all'esterno di Azure**.</span><span class="sxs-lookup"><span data-stu-id="8545f-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="8545f-350">Per i dettagli, vedere l'articolo relativo alle [informazioni su chiavi, segreti e certificati](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="8545f-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="8545f-351">Sebbene l'uso di un ID applicazione e di un certificato X. 509 sia supportato per le app ospitate in Azure, è consigliabile usare [identità gestite per le risorse di Azure](#use-managed-identities-for-azure-resources) quando si ospita un'app in Azure.</span><span class="sxs-lookup"><span data-stu-id="8545f-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="8545f-352">Le identità gestite non richiedono l'archiviazione di un certificato nell'app o nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="8545f-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="8545f-353">L'app di esempio usa un ID applicazione e un certificato X. 509 quando l' `#define` istruzione all'inizio del file *Program.cs* è impostata su `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="8545f-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="8545f-354">Creare un certificato di archivio PKCS # 12 (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="8545f-354">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="8545f-355">Le opzioni per la creazione di certificati includono [Makecert in Windows](/windows/desktop/seccrypto/makecert) e [openssl](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="8545f-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="8545f-356">Installare il certificato nell'archivio certificati personale dell'utente corrente.</span><span class="sxs-lookup"><span data-stu-id="8545f-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="8545f-357">Contrassegnare la chiave come esportabile è facoltativa.</span><span class="sxs-lookup"><span data-stu-id="8545f-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="8545f-358">Prendere nota dell'identificazione personale del certificato, che verrà usato più avanti in questo processo.</span><span class="sxs-lookup"><span data-stu-id="8545f-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="8545f-359">Esportare il certificato di archiviazione PKCS # 12 (*. pfx*) come certificato con codifica der (*. cer*).</span><span class="sxs-lookup"><span data-stu-id="8545f-359">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="8545f-360">Registrare l'app con Azure AD (**registrazioni app**).</span><span class="sxs-lookup"><span data-stu-id="8545f-360">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="8545f-361">Caricare il certificato con codifica DER (*. cer*) in Azure ad:</span><span class="sxs-lookup"><span data-stu-id="8545f-361">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="8545f-362">Selezionare l'app in Azure AD.</span><span class="sxs-lookup"><span data-stu-id="8545f-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="8545f-363">Passare a **certificati & segreti**.</span><span class="sxs-lookup"><span data-stu-id="8545f-363">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="8545f-364">Selezionare **Carica certificato** per caricare il certificato, che contiene la chiave pubblica.</span><span class="sxs-lookup"><span data-stu-id="8545f-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="8545f-365">Un certificato con *estensione cer*, *PEM*o *CRT* è accettabile.</span><span class="sxs-lookup"><span data-stu-id="8545f-365">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="8545f-366">Archiviare il nome dell'insieme di credenziali delle chiavi, l'ID applicazione e l'identificazione personale del certificato nel file *appsettings.js* dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="8545f-367">Passare a insiemi di credenziali delle **chiavi** nella portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="8545f-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="8545f-368">Selezionare l'insieme di credenziali delle chiavi creato nell' [Archivio Secret nell'ambiente di produzione con Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) sezione.</span><span class="sxs-lookup"><span data-stu-id="8545f-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="8545f-369">Selezionare **Criteri di accesso**.</span><span class="sxs-lookup"><span data-stu-id="8545f-369">Select **Access policies**.</span></span>
1. <span data-ttu-id="8545f-370">Selezionare **Aggiungi criterio di accesso**.</span><span class="sxs-lookup"><span data-stu-id="8545f-370">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="8545f-371">Aprire **autorizzazioni segrete** e fornire all'app le autorizzazioni **Get** ed **List** .</span><span class="sxs-lookup"><span data-stu-id="8545f-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="8545f-372">Selezionare **Seleziona entità** e selezionare l'app registrata in base al nome.</span><span class="sxs-lookup"><span data-stu-id="8545f-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="8545f-373">Fare clic sul pulsante **Seleziona**.</span><span class="sxs-lookup"><span data-stu-id="8545f-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="8545f-374">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="8545f-374">Select **OK**.</span></span>
1. <span data-ttu-id="8545f-375">Selezionare **Salva**.</span><span class="sxs-lookup"><span data-stu-id="8545f-375">Select **Save**.</span></span>
1. <span data-ttu-id="8545f-376">Distribuire l'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-376">Deploy the app.</span></span>

<span data-ttu-id="8545f-377">L' `Certificate` app di esempio ottiene i valori di configurazione da `IConfigurationRoot` con lo stesso nome del nome del segreto:</span><span class="sxs-lookup"><span data-stu-id="8545f-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="8545f-378">Valori non gerarchici: il valore per `SecretName` viene ottenuto con `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="8545f-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="8545f-379">Valori gerarchici (sezioni): `:` notazione use (due punti) o `GetSection` metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="8545f-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="8545f-380">Per ottenere il valore di configurazione, usare uno di questi approcci:</span><span class="sxs-lookup"><span data-stu-id="8545f-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="8545f-381">Il certificato X. 509 è gestito dal sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="8545f-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="8545f-382">L'app chiama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> con i valori forniti dal *appsettings.jssul* file:</span><span class="sxs-lookup"><span data-stu-id="8545f-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="8545f-383">Valori di esempio</span><span class="sxs-lookup"><span data-stu-id="8545f-383">Example values:</span></span>

* <span data-ttu-id="8545f-384">Nome dell'insieme di credenziali delle chiavi: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="8545f-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="8545f-385">ID applicazione: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="8545f-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="8545f-386">Identificazione personale del certificato: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="8545f-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="8545f-387">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="8545f-387">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="8545f-388">Quando si esegue l'app, in una pagina Web vengono visualizzati i valori dei segreti caricati.</span><span class="sxs-lookup"><span data-stu-id="8545f-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="8545f-389">Nell'ambiente di sviluppo, i valori Secret vengono caricati con il `_dev` suffisso.</span><span class="sxs-lookup"><span data-stu-id="8545f-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="8545f-390">Nell'ambiente di produzione, i valori vengono caricati con il `_prod` suffisso.</span><span class="sxs-lookup"><span data-stu-id="8545f-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="8545f-391">Usare identità gestite per le risorse di Azure</span><span class="sxs-lookup"><span data-stu-id="8545f-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="8545f-392">**Un'app distribuita in Azure** può trarre vantaggio dalle [identità gestite per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/overview), che consente all'app di eseguire l'autenticazione con Azure Key Vault usando Azure ad autenticazione senza credenziali (ID applicazione e password/segreto client) archiviate nell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="8545f-393">L'app di esempio usa le identità gestite per le risorse di Azure quando l' `#define` istruzione all'inizio del file *Program.cs* è impostata su `Managed` .</span><span class="sxs-lookup"><span data-stu-id="8545f-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="8545f-394">Immettere il nome dell'insieme di credenziali nel *appsettings.js* del file dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-394">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="8545f-395">L'app di esempio non richiede un ID applicazione e una password (segreto client) quando è impostata sulla `Managed` versione, in modo che sia possibile ignorare tali voci di configurazione.</span><span class="sxs-lookup"><span data-stu-id="8545f-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="8545f-396">L'app viene distribuita in Azure e Azure autentica l'app per accedere Azure Key Vault solo usando il nome dell'insieme di credenziali archiviato nel *appsettings.jssu* file.</span><span class="sxs-lookup"><span data-stu-id="8545f-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="8545f-397">Distribuire l'app di esempio nel servizio app Azure.</span><span class="sxs-lookup"><span data-stu-id="8545f-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="8545f-398">Un'app distribuita nel servizio app Azure viene registrata automaticamente con Azure AD quando viene creato il servizio.</span><span class="sxs-lookup"><span data-stu-id="8545f-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="8545f-399">Ottenere l'ID oggetto dalla distribuzione per utilizzarlo nel comando seguente.</span><span class="sxs-lookup"><span data-stu-id="8545f-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="8545f-400">L'ID oggetto viene visualizzato nella portale di Azure nel **Identity** Pannello del servizio app.</span><span class="sxs-lookup"><span data-stu-id="8545f-400">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="8545f-401">Usando l'interfaccia della riga di comando di Azure e l'ID oggetto dell'app, fornire all'app le `list` `get` autorizzazioni e per accedere all'insieme di credenziali delle chiavi:</span><span class="sxs-lookup"><span data-stu-id="8545f-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="8545f-402">**Riavviare l'app** usando l'interfaccia della riga di comando di Azure, PowerShell o la portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="8545f-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="8545f-403">App di esempio:</span><span class="sxs-lookup"><span data-stu-id="8545f-403">The sample app:</span></span>

* <span data-ttu-id="8545f-404">Crea un'istanza della `AzureServiceTokenProvider` classe senza una stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="8545f-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="8545f-405">Quando non viene specificata una stringa di connessione, il provider tenta di ottenere un token di accesso dalle identità gestite per le risorse di Azure.</span><span class="sxs-lookup"><span data-stu-id="8545f-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="8545f-406">Viene creato un nuovo oggetto <xref:Microsoft.Azure.KeyVault.KeyVaultClient> con il `AzureServiceTokenProvider` callback del token dell'istanza.</span><span class="sxs-lookup"><span data-stu-id="8545f-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="8545f-407">L' <xref:Microsoft.Azure.KeyVault.KeyVaultClient> istanza viene utilizzata con un'implementazione predefinita di <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> che carica tutti i valori del segreto e sostituisce i doppi trattini ( `--` ) con i due punti ( `:` ) nei nomi delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="8545f-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="8545f-408">Valore di esempio del nome dell'insieme di credenziali delle chiavi: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="8545f-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="8545f-409">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="8545f-409">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="8545f-410">Quando si esegue l'app, in una pagina Web vengono visualizzati i valori dei segreti caricati.</span><span class="sxs-lookup"><span data-stu-id="8545f-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="8545f-411">Nell'ambiente di sviluppo i valori Secret hanno il `_dev` suffisso perché sono forniti da segreti utente.</span><span class="sxs-lookup"><span data-stu-id="8545f-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="8545f-412">Nell'ambiente di produzione, i valori vengono caricati con il `_prod` suffisso perché sono forniti da Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8545f-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="8545f-413">Se viene visualizzato un `Access denied` errore, verificare che l'app sia registrata con Azure ad e che l'accesso sia stato fornito all'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="8545f-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="8545f-414">Confermare di aver riavviato il servizio in Azure.</span><span class="sxs-lookup"><span data-stu-id="8545f-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="8545f-415">Per informazioni sull'uso del provider con un'identità gestita e una pipeline di Azure DevOps, vedere [creare una connessione del servizio Azure Resource Manager a una macchina virtuale con un'identità del servizio gestito](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="8545f-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="8545f-416">Usa prefisso nome chiave</span><span class="sxs-lookup"><span data-stu-id="8545f-416">Use a key name prefix</span></span>

<span data-ttu-id="8545f-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> fornisce un overload che accetta un'implementazione di <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , che consente di controllare la modalità di conversione dei segreti dell'insieme di credenziali delle chiavi in chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="8545f-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="8545f-418">Ad esempio, è possibile implementare l'interfaccia per caricare i valori dei segreti in base a un valore di prefisso fornito all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="8545f-419">Questo consente, ad esempio, di caricare i segreti in base alla versione dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="8545f-420">Non usare i prefissi sui segreti dell'insieme di credenziali delle chiavi per collocare i segreti per più app nello stesso insieme di credenziali delle chiavi o per collocare i segreti ambientali (ad esempio, *sviluppo* rispetto ai segreti di *produzione* ) nello stesso insieme di credenziali.</span><span class="sxs-lookup"><span data-stu-id="8545f-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="8545f-421">È consigliabile che app e ambienti di sviluppo/produzione diversi usino insiemi di credenziali delle chiavi distinti per isolare gli ambienti app per il massimo livello di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="8545f-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="8545f-422">Nell'esempio seguente viene stabilito un segreto nell'insieme di credenziali delle chiavi (e usando lo strumento di gestione dei segreti per l'ambiente di sviluppo) per `5000-AppSecret` (i periodi non sono consentiti nei nomi dei segreti dell'insieme di credenziali delle chiavi).</span><span class="sxs-lookup"><span data-stu-id="8545f-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="8545f-423">Questo segreto rappresenta un segreto app per la versione 5.0.0.0 dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="8545f-424">Per un'altra versione dell'app, 5.1.0.0, viene aggiunto un segreto all'insieme di credenziali delle chiavi (e usando lo strumento di gestione dei segreti) per `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="8545f-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="8545f-425">Ogni versione dell'app carica il valore del segreto con versione nella configurazione come `AppSecret` , rimuovendo la versione durante il caricamento del segreto.</span><span class="sxs-lookup"><span data-stu-id="8545f-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="8545f-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> viene chiamato con un oggetto personalizzato <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="8545f-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="8545f-427">L' <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementazione reagisce ai prefissi di versione dei segreti per caricare il segreto appropriato nella configurazione:</span><span class="sxs-lookup"><span data-stu-id="8545f-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="8545f-428">`Load` carica un segreto quando il nome inizia con il prefisso.</span><span class="sxs-lookup"><span data-stu-id="8545f-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="8545f-429">Non vengono caricati altri segreti.</span><span class="sxs-lookup"><span data-stu-id="8545f-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="8545f-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="8545f-430">`GetKey`:</span></span>
  * <span data-ttu-id="8545f-431">Rimuove il prefisso dal nome del segreto.</span><span class="sxs-lookup"><span data-stu-id="8545f-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="8545f-432">Sostituisce due trattini in qualsiasi nome con `KeyDelimiter` , che è il delimitatore usato nella configurazione (in genere i due punti).</span><span class="sxs-lookup"><span data-stu-id="8545f-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="8545f-433">Azure Key Vault non consente i due punti nei nomi dei segreti.</span><span class="sxs-lookup"><span data-stu-id="8545f-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="8545f-434">Il `Load` metodo viene chiamato da un algoritmo del provider che scorre i segreti dell'insieme di credenziali per trovare quelli con il prefisso della versione.</span><span class="sxs-lookup"><span data-stu-id="8545f-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="8545f-435">Quando viene trovato un prefisso di versione con `Load` , l'algoritmo usa il `GetKey` metodo per restituire il nome della configurazione del nome del segreto.</span><span class="sxs-lookup"><span data-stu-id="8545f-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="8545f-436">Rimuove il prefisso della versione dal nome del segreto e restituisce il resto del nome del segreto per il caricamento nelle coppie nome-valore della configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="8545f-437">Quando viene implementato questo approccio:</span><span class="sxs-lookup"><span data-stu-id="8545f-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="8545f-438">La versione dell'app specificata nel file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="8545f-439">Nell'esempio seguente la versione dell'app è impostata su `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="8545f-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="8545f-440">Verificare che `<UserSecretsId>` sia presente una proprietà nel file di progetto dell'app, dove `{GUID}` è un GUID fornito dall'utente:</span><span class="sxs-lookup"><span data-stu-id="8545f-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="8545f-441">Salvare i segreti seguenti localmente con lo [strumento di gestione dei segreti](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="8545f-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="8545f-442">I segreti vengono salvati in Azure Key Vault usando i comandi dell'interfaccia della riga di comando di Azure seguenti:</span><span class="sxs-lookup"><span data-stu-id="8545f-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="8545f-443">Quando l'app viene eseguita, vengono caricati i segreti dell'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="8545f-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="8545f-444">La stringa segreta per `5000-AppSecret` viene confrontata con la versione dell'app specificata nel file di progetto dell'app ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="8545f-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="8545f-445">La versione `5000` (con il trattino) viene rimossa dal nome della chiave.</span><span class="sxs-lookup"><span data-stu-id="8545f-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="8545f-446">In tutta l'app, la lettura della configurazione con la chiave `AppSecret` carica il valore del segreto.</span><span class="sxs-lookup"><span data-stu-id="8545f-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="8545f-447">Se la versione dell'app viene modificata nel file di progetto in `5.1.0.0` e l'app viene nuovamente eseguita, il valore del segreto restituito è `5.1.0.0_secret_value_dev` nell'ambiente di sviluppo e `5.1.0.0_secret_value_prod` in produzione.</span><span class="sxs-lookup"><span data-stu-id="8545f-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="8545f-448">È anche possibile fornire un' <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementazione personalizzata a <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="8545f-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="8545f-449">Un client personalizzato consente la condivisione di una singola istanza del client nell'app.</span><span class="sxs-lookup"><span data-stu-id="8545f-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8545f-450">Associare una matrice a una classe</span><span class="sxs-lookup"><span data-stu-id="8545f-450">Bind an array to a class</span></span>

<span data-ttu-id="8545f-451">Il provider è in grado di leggere i valori di configurazione in una matrice per l'associazione a una matrice POCO.</span><span class="sxs-lookup"><span data-stu-id="8545f-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="8545f-452">Quando si legge da un'origine di configurazione che consente alle chiavi di contenere separatori di due punti ( `:` ), viene usato un segmento di chiave numerico per distinguere le chiavi che costituiscono una matrice ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="8545f-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="8545f-453">Per altre informazioni, vedere [Configuration: associare una matrice a una classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="8545f-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="8545f-454">Azure Key Vault chiavi non possono utilizzare i due punti come separatore.</span><span class="sxs-lookup"><span data-stu-id="8545f-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="8545f-455">L'approccio descritto in questo argomento USA trattini doppi ( `--` ) come separatore per i valori gerarchici (sezioni).</span><span class="sxs-lookup"><span data-stu-id="8545f-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="8545f-456">Le chiavi di matrice vengono archiviate in Azure Key Vault con trattini doppi e segmenti di chiave numerica ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="8545f-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="8545f-457">Esaminare la seguente configurazione del provider di registrazione [Serilog](https://serilog.net/) fornita da un file JSON.</span><span class="sxs-lookup"><span data-stu-id="8545f-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="8545f-458">Nella matrice sono definiti due valori letterali di oggetto `WriteTo` che riflettono due *sink*Serilog, che descrivono le destinazioni per la registrazione dell'output:</span><span class="sxs-lookup"><span data-stu-id="8545f-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="8545f-459">La configurazione mostrata nel file JSON precedente viene archiviata in Azure Key Vault usando la notazione a doppio trattino ( `--` ) e i segmenti numerici:</span><span class="sxs-lookup"><span data-stu-id="8545f-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="8545f-460">Chiave</span><span class="sxs-lookup"><span data-stu-id="8545f-460">Key</span></span> | <span data-ttu-id="8545f-461">valore</span><span class="sxs-lookup"><span data-stu-id="8545f-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="8545f-462">Ricarica segreti</span><span class="sxs-lookup"><span data-stu-id="8545f-462">Reload secrets</span></span>

<span data-ttu-id="8545f-463">I segreti vengono memorizzati nella cache finché non `IConfigurationRoot.Reload()` viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="8545f-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="8545f-464">I segreti scaduti, disabilitati e aggiornati nell'insieme di credenziali delle chiavi non vengono rispettati dall'app fino a quando non `Reload` viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="8545f-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="8545f-465">Segreti disabilitati e scaduti</span><span class="sxs-lookup"><span data-stu-id="8545f-465">Disabled and expired secrets</span></span>

<span data-ttu-id="8545f-466">I segreti disabilitati e scaduti generano un' <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="8545f-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="8545f-467">Per impedire che l'app venga generata, fornire la configurazione usando un provider di configurazione diverso o aggiornare il segreto disabilitato o scaduto.</span><span class="sxs-lookup"><span data-stu-id="8545f-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="8545f-468">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="8545f-468">Troubleshoot</span></span>

<span data-ttu-id="8545f-469">Quando l'app non riesce a caricare la configurazione usando il provider, viene scritto un messaggio di errore nell' [infrastruttura di registrazione ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="8545f-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="8545f-470">Le condizioni seguenti impediranno il caricamento della configurazione:</span><span class="sxs-lookup"><span data-stu-id="8545f-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="8545f-471">L'app o il certificato non è configurato correttamente in Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="8545f-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="8545f-472">L'insieme di credenziali delle chiavi non esiste in Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8545f-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="8545f-473">L'app non è autorizzata ad accedere all'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="8545f-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="8545f-474">Il criterio di accesso non include le `Get` `List` autorizzazioni e.</span><span class="sxs-lookup"><span data-stu-id="8545f-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="8545f-475">Nell'insieme di credenziali delle chiavi i dati di configurazione (coppia nome-valore) sono denominati, mancanti, disabilitati o scaduti in modo errato.</span><span class="sxs-lookup"><span data-stu-id="8545f-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="8545f-476">L'app presenta il nome dell'insieme di credenziali delle chiavi errato ( `KeyVaultName` ), Azure ad ID applicazione ( `AzureADApplicationId` ) o l'identificazione personale del certificato Azure ad ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="8545f-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="8545f-477">La chiave di configurazione (nome) non è corretta nell'app per il valore che si sta provando a caricare.</span><span class="sxs-lookup"><span data-stu-id="8545f-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="8545f-478">Quando si aggiungono i criteri di accesso per l'app all'insieme di credenziali delle chiavi, il criterio è stato creato, ma il pulsante **Salva** non è stato selezionato nell'interfaccia utente dei **criteri di accesso** .</span><span class="sxs-lookup"><span data-stu-id="8545f-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8545f-479">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8545f-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="8545f-480">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="8545f-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="8545f-481">Microsoft Azure: Key Vault documentazione</span><span class="sxs-lookup"><span data-stu-id="8545f-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="8545f-482">Come generare e trasferire chiavi HSM protette per Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8545f-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="8545f-483">Classe KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="8545f-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="8545f-484">Guida introduttiva: Impostare e recuperare un segreto da Azure Key Vault tramite un'app Web .NET</span><span class="sxs-lookup"><span data-stu-id="8545f-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="8545f-485">Esercitazione: Come usare Azure Key Vault con una macchina virtuale Windows di Azure in .NET</span><span class="sxs-lookup"><span data-stu-id="8545f-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

